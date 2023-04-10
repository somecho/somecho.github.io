---
title: "OfxJFA - OpenFrameworks Jump Flooding addon"
emoji: "🧩"
date: 2021-02-19T10:54:07+01:00
summary: "OfxJFA ist ein OpenFrameworks Addon von dem mit GLSL-Shadern implementierten Jump Flooding-Algorithmus."
tags: ["C++","OpenGL","GLSL","OpenFrameworks"]
---


{{< image 
src="https://user-images.githubusercontent.com/26333602/108489927-6fbf2b80-72a2-11eb-946b-d081ad984988.png"
alt="ein von Jump Flooding eingefärbtes Voronoi-Diagramm" 
caption="Farbzuordnung nach Jump Flooding">}}

[OfxJFA](https://github.com/somecho/ofxJFA) ist ein Addon für das
C++ Framework OpenFrameworks des Jump Flooding-Algorithmus, der mit
GLSL-Shadern implementiert wurde. Der Jump Flooding-Algorithmus ist eine
Methode zur Konstruktion von Voronoi-Diagrammen, indem die Entfernung und ID
des nächstgelegenen *Seeds* für jeden Pixel in einer Textur codiert wird. Dieses
Add-On folgt der Implementierungsbeschreibung in diesem [Artikel von Ryan
Kaplan](https://www.rykap.com/graphics/skew/2016/02/25/voronoi-diagrams/).

## Verwendung ofxJFA
Die Verwendung von ofxJFA ist so einfach wie das Übergeben einer `ofxTexture` an
die `update()`-Methode einer Instanz der Klasse `ofxJFA`. Die Klasse führt dann 11
Durchgänge des Jump-Flooding-Algorithmus durch, schreibt jedes Mal das Output
in eine `RGBA32F`-Textur und gibt sie am Ende zurück.

### Declare and Init
```cpp
//ofApp.h
#include "ofxJFA.h"
ofxJfa jfa;

//ofApp.cpp 
//Breite und Höhe der Quelltextur
jfa.init(1280,720); 
```
### Update 
```cpp
//ofApp.cpp

//Quelltextur eingeben
jfa.update(tex); 
```

## Verwendung des Outputs 
Das Output dieses Add-ons ist eine JFA-Kodierung, bei der die XY-Koordinaten
zum nächstgelegenen Seed in den roten und grünen Kanälen der Textur und die
Entfernung dazu im blauen Kanal aufgezeichnet werden.

Die Textur kann dann in einem anderen Shader für andere Zwecke verwendet werden.
```cpp
//ofApp.cpp

// Texture in einen anderen Shader geben
shader.setUniformTexture("jfa", jfa.getTexture(), 0);
shader.setUniformTexture("src", tex, 1);
```
```glsl
//shader.frag

//Kodierung abrufen
vec4 loc = texture(jfa, gl_FragCoord.xy); 
gl_FragColor = texture(src, loc.xy); 
```

