---
title: "The Best String Compression in JavaScript"
subtitle: "A mini comparison of some string compression algorithms"
description: "A mini comparison of some string compression algorithms"
date: "2023-05-22"
tags: ["JavaScript", "p5.js"]
---

Lately, I needed to find a decent algorithm to compress strings in JavaScript
so that I could encode it as a URL param. Since the strings to be encoded where
[p5](https://p5js.org/) sketches that ran in the [p5.cljs web editor](https://p5cljs-editor.onrender.com/), it was important that the
compressed string was short enough to enable longer sketches to be written.

## The algorithms

After some searching on the web, I found some algorithms that I could use for this purpose. These are:
- [u-lzss](https://code.google.com/archive/p/u-lzss/) - an algorithm created by Google
- [lz-string](https://github.com/pieroxy/lz-string) - an LZ-compression based algorithm
- [lzw](https://gist.github.com/revolunet/843889) - another algorithm also based on LZ-compression

If you don't know what LZ compression is, well, I confess ... neither do I.
What I noticed was that LZ-based compression is rather ubiquitous.

### Hiccups

There were many more compression algorithms that I could find for JavaScript.
However, many of them were either only for Node.js, using the `Buffer` api, or
just didn't work.

## The benchmark

Since the only thing that mattered to me was the resulting base64 string at the
end of the compression process, the benchmarks I ran only measured the length
of the resulting string. The compression process looks like this:
- compress the string using the algorithm
- encode the resulting string as a `Uint8Array`
- convert this the `Uint8Array` to base64

I used three different string sources to test the algorithms.
- `sketch` - an example sketch for the p5.cljs web editor. Lines: 42.
- `simplexNoise` - a GLSL snippet for 4D simplex noise. Lines: 92.
- `triangle` - a JS wrapper for the C triangulation library [triangle](https://www.cs.cmu.edu/~quake/triangle.html). Lines: 391.

Since `triangle` contained template literals, I had to modify the source to escape the backticks and `$`.

## The results
### character count

|                | sketch  | simplex noise | triangle |
|----------------|---------|---------------|----------|
| raw source     | 1320    | 2936          | 9112     |
| base 64        | 1760    | 3916          | 12152    |
| u-lzss         | 1276    | 2768          | 5224     |
| lz-string      | 1100    | 2244          | 4876     |
| LZW            | 1404    | 2968          | 6936     |


### ratio
|                | sketch  | simplex noise | triangle |
|----------------|---------|---------------|----------|
| base 64        | 133.33% | 133.37%       | 133.36%  |
| u-lzss         | 96.66%  | 94.27%        | 57.33%   |
| lz-string      | 83.33%  | 76.43%        | 53.51%   |
| LZW            | 106.36% | 101.08%       | 76.11%   |

The base 64 conversion of the string always expanded the length. This is just how base64 works. 

From this mini-benchmark, we see that the lz-string algorithm performed the
best, reaching almost a compression ratio of almost 50% for the largest source
text.

For all three algorithms that were tested, the compression ratio improved as
the length of the source string increased. Using _any_ compression algorithm
was always better than not using any at all.

## Reversibility
|                | sketch  | simplex noise | triangle |
|----------------|---------|---------------|----------|
| base 64        | ✅      | ✅            | ✅       |
| u-lzss         | ✅      | ✅            | ❌       |
| lz-string      | ✅      | ✅            | ✅       |
| LZW            | ✅      | ✅            | ✅       |

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
server somewhere. In this situation, I'll be choosing lz-string for my use
case.

If you want to checkout the benchmark, it's [on
github](https://github.com/somecho/js-string-compression-benchmarks). Also, I
don't have a deep understanding of compression and maybe missing out on
something. If you know better, do reach out on my socials or email!
