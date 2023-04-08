---
title: "OfxSwatch - OpenFrameworks Color Palette addon"
emoji: "🎨"
date: 2021-03-30T11:21:53+01:00
draft: true
summary: "A texture-based color gradient and palette generator addon for OpenFrameworks."
tags: ["C++","OpenFrameworks","GLSL"]
---

![](https://user-images.githubusercontent.com/26333602/112969389-7c5e5b80-914d-11eb-9352-a9582e5aa765.png)

[OfxSwatch](https://github.com/somecho/ofxSwatch) is a color palette and gradient generator addon for the C++ creative
coding toolkit [OpenFrameworks](https://openframeworks.cc), generating textures for
later use in shaders or sampling. It internally converts RGB colors to the [LAB
color space](https://en.wikipedia.org/wiki/CIELAB_color_space), allowing equidistant gradients to be created.
A texture based color palette and gradient generator. ofxSwatch can generate multirow gradients and palettes with equidistant color points. 


| ![](https://user-images.githubusercontent.com/26333602/112970321-5c7b6780-914e-11eb-8940-384c14162478.png) |
| :---: |
| *colors sampled from a texture created by ofxSwatch* |

## Using ofxSwatch
1. Setting the OpenGL version makes sure that the programmable rendering
   pipeline is enabled.

```cpp
//main.cpp
ofGLFWWindowSettings setting;
setting.setSize(1280, 720);
setting.setGLVersion(4, 6);
ofCreateWindow(setting);
```

2. Setup the swatch by determining what the desired size of the output should be. 
```cpp
//ofApp.cpp
ofxSwatch swatch;
swatch.setup(WIDTH, HEIGHT);
```

3. Add the colors then 'compile' the swatch. This code generates the texture
   example above. A `vector` of `ofColor` can be passed into `addPalette()` to
   create a row of discrete colors. Passing it to `addGradient()` will create a
   new row of colors that will be rendered as an equidistant gradient. The
   `addColor()` method can be used to add colors to a row after a row is
   created. The first argument takes an `ofColor` and the second argument takes
   the index of the row, which the color should be inserted in.
```cpp
//creating a palette using a premade vector
vector<ofColor> row1 {
    ofColor(0),
    ofColor::fromHex(0xb01d1e),
    ofColor::fromHex(0x006be4),
    ofColor::fromHex(0xF16826),
    ofColor::fromHex(0x7e63b4),
    ofColor::fromHex(0xead35f),
    ofColor::fromHex(0x00bbad),
    fColor(255),
    ofColor(0)
    };
swatch.addPalette(row1);

//individually adding colors
swatch.addGradient();
swatch.addColor(ofColor(0), 1);
swatch.addColor(ofColor(255), 1);

//anonymous vector
swatch.addGradient({
    ofColor(0,255,0),
    ofColor(255,0,0),
    ofColor(255,0,255),
    ofColor(255)
});

//generate the swatch
swatch.createSwatch();
```

## Sampling the swatch

The generated texture of ofxSwatch can then be used as input to another shader.
Alternatively, a `sample()` method is available for use, taking two arguments:
`sample(VALUE,ROW)`, where `VALUE` is a `float` between `0.0` and `1.0` and
`ROW` is the index of the color row in the palette. This allows smooth
interpolation between multipoint gradients.

