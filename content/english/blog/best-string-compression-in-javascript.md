---
title: "The Best String Compression in JavaScript"
subtitle: "A mini comparison of some string compression algorithms"
description: "A mini comparison of some string compression algorithms"
date: "2023-05-22"
tags: ["JavaScript", "p5.js"]
---
_edit: 23.05.22 - add benchmarks of gzip, zlib and deflate via wasm-flate_

Lately, I needed to find a decent algorithm to compress strings in JavaScript
so that I could encode it as a URL param. Since the strings to be encoded where
[p5](https://p5js.org/) sketches that ran in the [p5.cljs web editor](https://p5cljs-editor.onrender.com/), it was important that the
compressed string was short enough to enable longer sketches to be written.

## The algorithms

After some searching on the web, I found some algorithms that I could use for this purpose. These are:
- [u-lzss](https://code.google.com/archive/p/u-lzss/) - an algorithm created by Google
- [lz-string](https://github.com/pieroxy/lz-string) - an LZ-compression based algorithm
- [lzw](https://gist.github.com/revolunet/843889) - another algorithm also based on LZ-compression
- gzip - this, and the next two algorithms, are a part of [wasm-flate](https://drbh.github.io/wasm-flate/)
- zlib 
- deflate

If you don't know what LZ compression is, well, I confess ... neither do I.
What I noticed was that LZ-based compression is rather ubiquitous.

### Hiccups

There were many more compression algorithms that I could find for JavaScript.
However, many of them were either only for Node.js, using the `Buffer` api, or
just didn't work. For algorithms using the `Buffer` api, wasm implementations
are a godsend.

## The benchmark

Since the only thing that mattered to me was the resulting base64 string at the
end of the compression process, the benchmarks I ran only measured the length
of the resulting string. The compression process looks like this:
- compress the string using the algorithm
- encode the resulting string as a `Uint8Array`
- convert this the `Uint8Array` to base64

I the following string sources to test the algorithms.
- `sketch` - an example sketch for the p5.cljs web editor. Lines: 42.
- `simplexNoise` - a GLSL snippet for 4D simplex noise. Lines: 92.
- `quil` - a part of the [Quil](https://github.com/quil/quil) source code. Lines: 191.
- `triangle` - a JS wrapper for the C triangulation library [triangle](https://www.cs.cmu.edu/~quake/triangle.html). Lines: 391.
- `modele` - a part of [Open Music's](https://github.com/openmusic-project/openmusic) source code. Lines: 588.

Since `triangle` contained template literals, I had to modify the source to escape the backticks and `$`.

## The results
### character count
|                | sketch | simplex noise | quil  | triangle | modele |
|----------------|--------|---------------|-------|----------|--------|
| no compression | 1320   | 2936          | 7745  | 9112     | 23766  |
| Base64         | 1760   | 3916          | 10328 | 12152    | 31688  |
| u-lzss         | 1276   | 2768          | 5572  | 5224     | 15980  |
| lz-string      | 1100   | 2244          | 4444  | 4876     | 12292  |
| LZW            | 1404   | 2968          | 6096  | 6936     | 19100  |
| GZIP           | 864    | 1708          | 3196  | 2808     | 7776   |
| ZLIB           | 848    | 1692          | 3180  | 2792     | 7760   |
| DEFLATE        | 840    | 1684          | 3172  | 2784     | 7752   |

{{< image
src="/images/compression.png"
alt="a bar chart of the compression results by character count"
caption="" >}}

### ratio
|                | sketch  | simplex noise | quil    | triangle | modele  |
|----------------|---------|---------------|---------|----------|---------|
| no compression | 100%    | 100%          | 100%    | 100%     | 100%    |
| Base64         | 133.33% | 133.37%       | 133.35% | 133.33%  | 133.33% |
| u-lzss         | 96.66%  | 94.27%        | 71.94%  | 57.33%   | 67.23%  |
| lz-string      | 83.33%  | 76.43%        | 57.37%  | 53.51%   | 51.72%  |
| LZW            | 106.36% | 101.08%       | 78.70%  | 76.11%   | 80.36%  |
| GZIP           | 65.45%  | 58.17%        | 41.26%  | 30.81%   | 32.71%  |
| ZLIB           | 64.24%  | 57.62%        | 41.05%  | 30.64%   | 32.65%  |
| DEFLATE        | 63.63%  | 57.35%        | 40.95%  | 30.55%   | 32.61%  |

{{< image
src="/images/compression-ratio.png"
alt="a line chart of the compression results by ratio"
caption="compression ratio increases as file size gets larger" >}}
The base 64 conversion of the string always expanded the length. This is just how base64 works. 

From this mini-benchmark, we see that the deflate algorithm performed the
best, reaching almost a compression ratio of around 30% for the largest source
text. That said, gzip, zlib and deflate have almost the same results.

For all three algorithms that were tested, the compression ratio improved as
the length of the source string increased. But beyond a certain size, it seems
to go back down.

## Reversibility
|          | sketch | simplex noise | quil | triangle | modele |
|----------|--------|---------------|------|----------|--------|
| Base64   | ✅      | ✅             | ✅    | ✅        | ✅      |
| u-lzss   | ✅      | ✅             | ❌    | ❌        | ❌      |
| lz-string| ✅      | ✅             | ✅    | ✅        | ✅      |
| LZW      | ✅      | ✅             | ✅    | ✅        | ✅      |
| GZIP     | ✅      | ✅             | ✅    | ✅        | ✅      |
| ZLIB     | ✅      | ✅             | ✅    | ✅        | ✅      |
| DEFLATE  | ✅      | ✅             | ✅    | ✅        | ✅      |

Testing the reversibility of the algorithms, I was surprised to see that
Google's u-lzss wasn't 100% reversible. For longer source strings, the
decompressed string contained corrupted and malformed strings towards the end
of the file.
### Snippet of original string

```
class TriangulateIO {
    static get LENGTH() { return 23; }
    constructor(props = {}) {

```
### Malformed output of u-lzss
```
lass TangleuleMoIO
cstr ic         etTy LENGTH({
eturn nu23; }   const tctor =(prop= pt{}{
```
## Conclusion
Compression is probably better done elsewhere than a browser environment. But
sometimes you just need to have a handy compression tool, like in the p5.cljs
editor, which shares sketches by storing them as a url param instead of in a
server somewhere. In this situation, I'll be choosing deflate for my use
case.

If you want to checkout the benchmark, it's [on
github](https://github.com/somecho/js-string-compression-benchmarks). Also, I
don't have a deep understanding of compression and maybe missing out on
something. If you know better, do reach out on my socials or email!
