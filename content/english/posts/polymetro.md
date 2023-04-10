---
title: "Polymetro - PWA metronome"
emoji: "🥁"
date: 2020-03-17T20:17:38+01:00
summary: "Polymetro is a polyrhythmic metronome built as a PWA using Vue for the frontend and Web Audio as the sound engine."
tags: ["Vue","WebAudio","JavaScript","PWA"]
---

{{< image 
src="/images/polymetro/overview.png"
alt="screenshots of mobile interface of polymetro in dark and light mode">}}

[Polymetro](https://polymetro.netlify.app) is a polyrhythmic metronome built as
a PWA using Vue for the frontend and Web Audio as the sound engine. The app can
only be accessed via mobile. [Source code here.](https://github.com/somecho/polymetro-2)

## Background

I designed and developed Polymetro in March of 2020, when I was learning Magnus
Lindberg's Twine. The piece is filled with polyrhythms, many of which I have
never encountered before.

{{< image 
src="/images/polymetro/twine.png"
alt="snippet of Magnus Lindberg's twine"
caption="snippet of Magnus Lindberg's Twine">}}

In order to learn these rhythms, it was necessary to *flatten* the polyrhythm
into one composite rhythm and subsequently learn it as a chunk. This wasn't
always easy when faced with a dense rhythm, like 9 against 8. To easy this
process for myself, I set out to create Polymetro.

## Development

The idea was to have a digital metronome that could play both single rhythms and
polyrhythms. Setting the desired metrum, the pulse grid would adjust itself to
visually show the number of beats per cycle.

{{< image 
src="/images/polymetro/pulsegrid.png" 
alt="screenshot of the pulsegrid used in polymetro"
caption="The pulse grid">}}

Each glyph in the pulse grid could be toggled by tapping on it, cycling between
three states: strong, weak and silent. 

The metronome is also trilingual. Clicking on the settings, a modal will appear
with the option to select between English, German and Chinese.
