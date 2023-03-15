---
title: "🎚 Polymetro - a PWA metronome"
date: 2020-03-17T20:17:38+01:00
summary: "Polymetro is a polyrhythmic metronome built as a PWA using Vue for the frontend and Web Audio as the sound engine."
showToc: true
tags: ["Vue","WebAudio","PWA","JavaScript"]
---

![](/images/polymetro/overview.png)

[Polymetro](https://polymetro.netlify.app) is a polyrhythmic metronome built as
a PWA using Vue for the frontend and Web Audio as the sound engine. The app can
only be accessed via mobile. [Source code here.](https://github.com/somecho/polymetro-2)

## Background

I designed and developed Polymetro in March of 2020, when I was learning Magnus
Lindberg's Twine. The piece is filled with polyrhythms, many of which I have
never encountered before.

![](/images/polymetro/twine.png)

In order to learn these rhythms, it was necessary to *flatten* the polyrhythm
into one composite rhythm and subsequently learn it as a chunk. This wasn't
always easy when faced with a dense rhythm, like 9 against 8. To easy this
process for myself, I set out to create Polymetro.

## Development

The idea was to have a digital metronome that could play both single rhythms and
polyrhythms. Setting the desired metrum, the pulse grid would adjust itself to
visually show the number of beats per cycle.

![](/images/polymetro/pulsegrid.png)

Each glyph in the pulse grid could be toggled by tapping on it, cycling between
three states: strong, weak and silent. 

The metronome is also trilingual. Clicking on the settings, a modal will appear
with the option to select between English, German and Chinese.

## The metronome

The sounds of the metronome are synthesized in realtime using Web Audio using
square waves with a sharp attack and decay. This was out of personal preference,
as I found sharp digital clicks to be most effective for my own practicing
habits. The strong and weak pulses of the two metronomes in polyrhythmic mode
have different pitches to help differentiate between rhythms.

![](/images/polymetro/scheduler.png)

The audio timeline has a simple structure. Every time the play button is
activated, two separate `setTimeout` events are triggered, each with its own
time interval. When the stop button is triggered, all `setTimeout` events are cleared.

## Thoughts

The browser's `setTimeout` event is not reliable, as the browser only tries to
execute the callback when the thread is free. This means that overtime, the two
metronomes can end up out of sync. A better design would be to have a single
`setTimeout` timeline that has intervals that are smaller, where both rhythms
fit and have the sounds be played when they're up next. 

Some other smaller improvements would be to implement different metronome sounds
for those that might find the square wave click to be too sharp. Seeing as the
particular sound of a metronome is a huge deciding factor for whether musicians
use them, this may well be worth implementing!


