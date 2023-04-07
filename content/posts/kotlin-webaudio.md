---
title: "WebAudio externals for KotlinJS"
date: 2021-05-27T10:44:41+01:00
emoji: "🔊"
summary: "A growing set of KotlinJS externals for the WebAudio API."
showTOC: "true"
tags: ["WebAudio", "Kotlin","KotlinJS"]
---

I started developing a [library of WebAudio externals for
KotlinJS](https://github.com/somecho/webaudio-kotlin) when I was
using Kotlin for most of my projects. The strange thing about KotlinJS (at the
time of writing) is that it does not have built-in externals for the WebAudio
API, as documented in [this issue](https://youtrack.jetbrains.com/issue/KT-39846).

## Usage
This library can be added as a local maven artifact. To use, clone the
repository, publish to Maven local and import it directly with gradle into the KotlinJS project. It then can be used directly. 

### Example
```kotlin
fun main() {
    /*
    The Audio Context can be created with a JS object. 
    This library includes a jsObject helper function
    to create JS objects.
     */
    val ctx = AudioContext(jsObject {
        latencyHint = "interactive"
        sampleRate = 44100
    })

    val osc = ctx.createOscillator()
    osc.type = "square"
    osc.frequency.setValueAtTime(440.0,ctx.currentTime)
    osc.start()

    val gain = ctx.createGain()
    gain.gain.setValueAtTime(0.0,ctx.currentTime)

    osc.connect(gain)
    gain.connect(ctx.destination)

    window.addEventListener("mousedown",{ e ->
        gain.gain.setValueAtTime(1.0,ctx.currentTime)
        console.log("mouse down")
    })
    window.addEventListener("mouseup",{ e ->
        gain.gain.setValueAtTime(0.0,ctx.currentTime)
        console.log("mouse up")
    })
}
```

## Status

This project is still a work-in-progress and is not yet on a 1:1 parity with the
WebAudio API. These features have been implemented as externals:

- BaseAudioContext
  - AudioContext
- AudioNode
  - AudioDestinationNode
  - AudioScheduledSourceNode
    - OscillatorNode
  - BiquadFilterNode
  - GainNode
- AudioParam
