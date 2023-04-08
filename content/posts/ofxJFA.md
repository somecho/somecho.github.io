---
title: "OfxJFA - OpenFrameworks Jump Flooding addon"
emoji: "🧩"
date: 2021-02-19T10:54:07+01:00
summary: "ofxJFA is an OpenFrameworks addon of the Jump Flooding algorithm implemented with GLSL shaders."
tags: ["C++","OpenGL","GLSL","OpenFrameworks"]
---


{{< image 
src="https://user-images.githubusercontent.com/26333602/108489927-6fbf2b80-72a2-11eb-946b-d081ad984988.png"
alt="a voronoi diagram colored by jumpflooding" 
caption="color map of jump flood output">}}

[OfxJFA](https://github.com/somecho/ofxJFA) is an addon for the C++ creative
coding framework [OpenFrameworks](https://openframeworks.cc) of the
Jump Flooding algorithm implemented with GLSL shaders. The Jump Flooding algorithm
is a method of constructing [Voronoi
diagrams](https://en.wikipedia.org/wiki/Voronoi_diagram) by encoding the
distance and ID of the closest seed in a texture for every pixel. This addon
follows the description of implementation by [this article by Ryan Kaplan](https://www.rykap.com/graphics/skew/2016/02/25/voronoi-diagrams/).

## Using ofxJFA
Using ofxJFA is as simple as passing an `ofxTexture` to the `update()` method of
an instance of the `ofxJFA` class. The class then does 11 passes of Jump
Flooding, writing the output each time to an `RGBA32F` texture and returns it in
the end.

### Declare and Init
```cpp
//ofApp.h
#include "ofxJFA.h"
ofxJfa jfa;

//ofApp.cpp 
//width and height of source
jfa.init(1280,720); 
```
### Update 
```cpp
//ofApp.cpp

//pass in source texture
jfa.update(sourceTexture); 
```

## Using the output
The output of this addon is a JFA encoding, where the XY coordinates to the
nearest seed is recorded in the red and green channels of the texture and the
distance to it in the blue channel. 

The texture can then be used in another shader for other purposes.
```cpp
//ofApp.cpp

// passing the texture into another shader
shader.setUniformTexture("jfa", jfa.getTexture(), 0);
shader.setUniformTexture("src", sourceTexture(), 1);
```
```glsl
//shader.frag

//get encoded data
vec4 loc = texture(jfa, gl_FragCoord.xy); 
//use encoded data to get color data from source
gl_FragColor = texture(src, loc.xy); 
```

