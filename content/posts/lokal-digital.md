---
title: "📺 Lokal/Digital - a machine learning enabled performance"
date: 2020-08-29T14:40:27+01:00
showToc: true
tags: ["C++","OpenFrameworks","Python","Machine Learning","Keras","PureData"]
summary: "Lokal/Digital is a interdisciplinary performance combining contemporary dance, free improvisation and live audiovisuals enabled by machine learning. "
---

Lokal/Digital is a interdisciplinary performance combining contemporary dance,
free improvisation and live audiovisuals. The performance was conceptualized in
collaboration with dancer-choreographer [Evelin
Stadler](https://atelierhaus-stadler-gerhardt.jimdo.com/atelierhaus/stadler). 

![](/images/lokaldigital/hero.JPG)

The performance involved a number of moving parts. For one, Evelin was
performing in a separate room with various props and was telecasted live onto a
screen on stage. In the main stage hall, I improvised on the piano,
manipulating in realtime electronic effects of the soundfeed while a visual
projection reacts to my playing. 

{{< video "/videos/lokaldigital/physarum-test.mp4" "perf" >}}

This article is mainly about the visual aspect of the performance.

## The visuals


The live visual projections consisted of two parts: 
- an OpenFrameworks application running a stylized physarum simulation
- 8 neural networks running in parallel, listening to my playing via PureData
  and controlling the simulation
  
{{< tweet 1100809319136804864 >}} 

The idea for using a physarum simulation was inspired by artist [Sage
Jenson](https://www.sagejenson.com), who wrote [a comprehensive article](https://cargocollective.com/sagejenson/physarum) about
implementing the simulation. I found the visual expression of physarum to be
particularly suiting to our performance and developed [my own implementation](https://github.com/somecho/openframeworks-physarum) in OpenFrameworks.

## An octet of neural networks


The reason there are 8 neural networks is so that each one can control a
separate parameter in the physarum application. The parameters exposed for
control are: 
- the number of directions a particle can look at
- the amount of deposit it leaves behind 
- a particle's max speed 
- the max force that can be applied on a particle 
- the decay rate of the deposit trails
- how far ahead a particle can sense 
- how steep the difference of the trails should be 
- the size of the displacement of the trails 

The networks control these parameters separately, like an ensemble, to change
the visual expression of the physarum in realtime. 

## Training the networks

{{< video "/videos/lokaldigital/physarum-train.mp4" "train1" >}}

The networks are trained on recordings of my own playing. I created a PureData
patch that analyzes my playing, which recorded the analyses as a CSV file. The
analysis outputs the following features:

- 37 Mel-frequency cepstral coefficients
- onset amount
- Average RMS level
- Spectral Centroid
- Spectral Irregularity
- Spectral Speed
- Spectral Brightness
- Harmonicity
- Spectral Skew

The notebook used to train the networks can be found
[here](https://github.com/somecho/lokal-digital/blob/master/notebooks/csvtonumpy2.ipynb). Disclaimer: the repo contains
almost no structure and proper filenaming. It was developed before I knew what
best practices were!


## Realtime Setup

{{< video "/videos/lokaldigital/physarum-train-2.mp4" "train2" >}}

A [python
script](https://github.com/somecho/lokal-digital/blob/master/lokaldigital.py)
ran an OSC server in realtime, listening to live input from the PureData patch,
passing it to the neural network for predictions and then sending the result
directly to the physarum application. 
