---
title: "HiKS - Generative Design Tool"
date: 2022-09-06T09:31:32+01:00
summary: "HiKS is a generative design tool developed and designed for the Swedish platform HiKS - Hitta Kliniska Studier. It was designed in collaboration with Swedish design firm BBDO Nordics."
showTOC: true
tags: ["Vue","JavaScript","Tailwind","p5.js","matter.js","WASM","electron","WebGL"]
---

HiKS is a generative design tool developed and designed for the Swedish platform
HiKS - Hitta Kliniska Studier. It was designed in collaboration with Swedish
design firm BBDO Nordics. The tool features a stylized simulation of cells in a
petri dish and was created using Vue(UI), Tailwind(CSS),
[p5.js](https://p5s.org) + WebGL (graphics) and
[matter.js](https://brm.io/matter-js) (soft body physics). The [WASM library
h264-mp4-encoder](https://github.com/TrevorSundberg/h264-mp4-encoder) was used
for in-app creation of high quality mp4 output. The HiKS tool was then
distributed as an Electron application to the client. The source code of this
project is closed. 

![](/images/hiks/UI.png)

## The HiKS Tool 

The control panel on the left side of the user interface allows the creation of
cells. Size, irregularity and color (from a predefined color palette) are
parameters that can be controlled. The WebGL canvas on the right side of the
interface is interactive. Cells can be clicked on to be selected for deletion.
The simulation can also be paused and continued.

![](/images/hiks/concept.png)

High quality stills and videos can be rendered from this tool, allowing for use
in web design, poster design or social media content design. The appearance of
the cells was designed in close collaboration with BBDO Nordics, who helped
guide me to sharpen my eye for visual detail.

![](/images/hiks/close-up.png)

## Application Structure
The application uses simple state management using Vue's Reactivity API. A
central `store` object was created to store all parameters of the entire
application, including user interface, graphics, physics engine and recording
module. As there were many nested components in the application, this allowed
for simple communication between them.

## Timeline
The tool was developed from initial conception to client delivery over a period
of 8 weeks.
