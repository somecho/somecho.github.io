---
title: "Testing the p5.cljs editor"
description: "Getting Jest to play well with Vite, React and Markdown"
subtitle: "Getting Jest to play well with Vite, React and Markdown"
date: "2023-05-25"
tags: ["React", "JavaScript", "Jest"]
---

There was never a time where I the JavaScript ecosystem more than when I tried
to write tests for the [p5.cljs web
editor](https://github.com/somecho/p5-cljs-web-editor). Things broken left and
right, missing plugins, babel, transpiling, modules, _npm_. **Disclaimer**: I
don't hate the JS ecosystem in earnest. Just ... _sometimes_.

This article is about why I chose Jest as the test runner for the p5.cljs
editor and the steps necessary to get it working with third-party components I
was using.

## Why not Vitest?

Because of this [issue](https://github.com/uiwjs/react-codemirror/issues/506).
I love Vite. With a previous project that used Vite's Env Variables
(`import.meta.env.VARIABLE`), the only way to set up tests without a headache
was to use Vitest. Needless to say, if you can use Vitest to test your
Vite-React project, you should. It was made for that. 

But now and then you hit a deadend, like the issue mentioned above with
CodeMirror. From what I understand, the issue is due to how Vitest bundles
dependencies. I'm not sure how, but if you have a clue, do reach out!

And this is why I switched to Jest.

## Setting up Jest

My first distaste with Jest started with just how many dependencies you needed
to install for Jest to work.

```sh
yarn add --dev jest babel-jest @babel/preset-env @babel/preset-react react-test-renderer
```
I also needed `@testing-library/react` and `jest-environment-jsdom`.

With the installation out of the way, I got started writing my first test. The
first test rendered the base app, just to check if I got everything setup correctly.

## The First Trial - ES Modules
Let's go `yarn test` and ...

```sh
Jest encountered an unexpected token

Jest failed to parse a file. This happens e.g. when your code or its dependencies use non-standard JavaScript syntax, or when Jest is not configured to support such syntax.
```

Some more details:
```sh
Details:

/re/dac/ted/p5-cljs-web-editor/node_modules/react-markdown/index.js:6
export {uriTransformer} from './lib/uri-transformer.js'
^^^^^^

SyntaxError: Unexpected token 'export'

> 1 | import ReactMarkdown from 'react-markdown'
    | ^
  2 | import hljs from "highlight.js"
  3 | import { useEffect } from 'react'
```
After much researching, I learned that **[Jest has limited support for ES
Modules](https://jestjs.io/docs/ecmascript-modules)**. Many encountered this
issue and the solution was to prevent Jest from transforming the
`react-markdown` module. I went a head and created a `jest.config.cjs` which
contained this configuration:

```js
/** @type {import('jest').Config} */
const config = {
	"transform": {},
	"transformIgnorePatterns": [],
};

module.exports = config;
```

## The Second Trial - JSX
Let's go `yarn test` round 2 and ..
```sh
Details:

/re/dac/ted/p5-cljs-web-editor/src/App.integration.test.jsx:5
import { render, cleanup, screen } from '@testing-library/react'
^^^^^^

SyntaxError: Cannot use import statement outside a module
```
The previous error was now gone and I was treated to another error to solve.
After some researching (again), the solution was to transform `.jsx` files so that
Jest could consume it. My `jest.config.cjs` now looks like this:

```js
/** @type {import('jest').Config} */
const config = {
	"transform": {
		"^.+\\.jsx?$": "babel-jest",
	},
	"transformIgnorePatterns": [],
};

module.exports = config;
```
Admittedly, this is a rookie mistake. Because if you knew how Jest worked (in
Node), you would know that `.jsx` files needed some special treatment. But hey,
we are all learning.

## The Third Trial - Markdown
Let's go `yarn test` round 3 and ...
```sh
Details:

/re/dac/ted/p5-cljs-web-editor/src/pages/about.md:1
({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,jest){# About
```
Another `unexpected token` error. This time for `.md` files. Learning from my
previous mistake, I now know that you need to also transform `.md` files for
Jest. Jest must be some special kid, huh? **Just kidding.** You generally need _some_
configuration to get markdown loading properly in a JS project.

A quick search allowed the
[jest-transformer-mdx](https://github.com/bitttttten/jest-transformer-mdx) to
reveal itself to me. Last commit: May 26, 2021. _Yikes_. Two years ago. Jest
went through 2 major versions since then. Jest
[27.0.0](https://github.com/jestjs/jest/releases/tag/v27.0.0) was released May
25, 2021. I'm on Jest 29.5.0.

I went and `yarn add --dev jest-transformer-mdx` and updated my `jest.config.cjs`.
```js
/** @type {import('jest').Config} */
const config = {
	"transform": {
		"^.+\\.jsx?$": "babel-jest",
		"^.+\\.(md|mdx)$": "jest-transformer-mdx",
	},
	"transformIgnorePatterns": [],
};

module.exports = config; 
```

## The Fourth Trial - Transformation
Let's go `yarn test` round 4 and ...

```sh
Error: Invalid synchronous transformer module:
  "/re/dac/ted/p5-cljs-web-editor/node_modules/jest-transformer-mdx/index.js" specified in the "transform" object of Jest configuration
  must export a `process` function.
  Code Transformation Documentation:
  https://jestjs.io/docs/code-transformation
```
Why did I expect this to work on the first go? Okay, it's not so bad though.
Jest has made it really explicit here that it's a problem with the transformer.
I was not ready to, after spending a few hours on setting Jest up, to go down
the rabbit hole of figuring out how transforming files for Jest. _Not yet_.
This is a [known
issue](https://github.com/bitttttten/jest-transformer-mdx/issues/25) with the transformer.

The author of the transformer
[suggested](https://github.com/bitttttten/jest-transformer-mdx/issues/25#issuecomment-1307501813)
rolling back to `@3.0.0-beta.0`. I rolled back and ran the test again.

```sh
Error: Invalid return value:
  `process()` or/and `processAsync()` method of code transformer found at
  "/re/dac/ted/p5-cljs-web-editor/node_modules/jest-transformer-mdx/index.js"
  should return an object or a Promise resolving to an object. The object
  must have `code` property with a string of processed code.
  This error may be caused by a breaking change in Jest 28:
  https://jestjs.io/docs/28.x/upgrading-to-jest28#transformer
  Code Transformation Documentation:
  https://jestjs.io/docs/code-transformation
```
A new error, not even more concrete. I went into
`jest-transformer-mdx/index.js` and found this function:

```js
function createTransformer(src, filename, config) {
	const withFrontMatter = parseFrontMatter(src, config.transformerConfig)
	const jsx = mdx.sync(withFrontMatter)
	const toTransform = `import {mdx} from '@mdx-js/react';${jsx}`

	return process(toTransform, filename, config).code
}
```
It was not returning and object with a `code` property. I changed the return value to:
```js
return process(toTransform, filename, config)
```
Then I ran `yarn test` again. 
```sh
ModuleNotFoundError: Cannot find module '@mdx-js/react' from 'src/pages/about.md'

Require stack:
  src/pages/about.md
  src/App.jsx
  src/App.integration.test.jsx

    at Resolver._throwModNotFoundError (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-resolve/build/resolver.js:427:11)
    at Resolver.resolveModule (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-resolve/build/resolver.js:358:10)
    at Resolver._getVirtualMockPath (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-resolve/build/resolver.js:619:14)
    at Resolver._getAbsolutePath (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-resolve/build/resolver.js:587:14)
    at Resolver.getModuleID (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-resolve/build/resolver.js:530:31)
    at Runtime._shouldMockCjs (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-runtime/build/index.js:1699:37)
    at Runtime.requireModuleOrMock (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-runtime/build/index.js:1036:16)
    at Object.<anonymous> (/re/dac/ted/p5-cljs-web-editor/src/pages/about.md:8:14)
    at Runtime._execModule (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-runtime/build/index.js:1429:24)
    at Runtime._loadModule (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-runtime/build/index.js:1013:12)
    at Runtime.requireModule (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-runtime/build/index.js:873:12)
    at Runtime.requireModuleOrMock (/re/dac/ted/p5-cljs-web-editor/node_modules/jest-runtime/build/index.js:1039:21)
    at require (/re/dac/ted/p5-cljs-web-editor/src/App.jsx:12:2) {
  code: 'MODULE_NOT_FOUND',
  hint: '',
  requireStack: [
    '/re/dac/ted/p5-cljs-web-editor/src/pages/about.md',
    '/re/dac/ted/p5-cljs-web-editor/src/App.jsx',
    '/re/dac/ted/p5-cljs-web-editor/src/App.integration.test.jsx'
  ],
  siblingWithSimilarExtensionFound: false,
  moduleName: '@mdx-js/react',
  _originalMessage: "Cannot find module '@mdx-js/react' from 'src/pages/about.md'"
}
```
A big stack for a small error. `@mdx-js/react`, which `jest-transformer-mdx`
depends on, was not found. I added it as a dev dependency and ran `yarn test` once more.

## Success
```sh
PASS  src/App.integration.test.jsx
      renders the app (669 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        3.249 s
Ran all test suites.
Done in 4.24s.
```
Finally! Now I can get down to business and write some **real** tests. Among which is to
- mock the cljs compiler function, since it's made available globally by a
  script and cannot be tested in a component
- mock the fetches properly for the markdown files (which I already did with
  dummy valuesfor this test)

## Conclusion
Testing is a destructive act. Well, according to the book _The Art of Software
Testing_ anyways. Through the bare act of trying to setup testing, I already
found issues with my application that needed my attention. 

I'm not a pro at testing. _Not yet_. But I encourage all, especially frontend
developers, to try testing their applications if they haven't already. If
you're like me, coming from a background of creative coding, you might not have
found the need to test your programs. If it looks good, it must be working.
Frontend developers might suffer this too. The act of trying to figure out
_what does it mean to have a working frontend_ or even to _actively try to
break the frontend_ will make you a more robust developer.
