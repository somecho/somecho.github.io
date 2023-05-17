---
title: "Creative Coding with Circles"
subtitle: "I coded circles everyday for a month and this is what I came up with."
tags: ["Kotlin", "OpenRNDR", "OpenGL"]
date: "2022-09-30"
---


_This article was originally published
[here.](https://uxdesign.cc/creative-coding-with-circles-fb4356b7f12c)_

{{< image
alt="A trail of elongating circles in orange and blue."
src="https://cdn.sanity.io/images/4hxtp24c/production/52d4af3c7f1220f3eb8497af4da3e97e2371a3ff-400x400.gif"
caption="Circle trail"
>}}

Right around the beginning of May, in the last few days of April, I began
learning [OpenRNDR](https://openrndr.org/). As a way to learn the framework,
the first things I tried to code were circles. And lots of them! I coded
circles in such profundity as I’ve never had in my life.

With that impulse, I found myself coding every single day and most of the work
I produced involved circles. I would like to share what I found with you!

## Trails

By varying the size and the position of the circle, then drawing it as it
moves, taking care not to erase its path, the outcome is something that
resembles pulled candy. The animation at the beginning of the article was
created using this technique.

{{< image
alt="A pulsating trail of circle that looks squiggly. The color is changing all the time."
src="https://cdn.sanity.io/images/4hxtp24c/production/546b80c8602b09823c994a1f74ee512c31bf92f1-400x400.gif"
caption="Pulsating circle trail"
>}}

You can always store previous positions in an array and then continue to vary
the size and color as new circles get drawn. You end up with something funkier!

{{< image
alt="a grid of circular tubes which are pulsating as if swallowing something. They all have different colors."
src="https://cdn.sanity.io/images/4hxtp24c/production/0bb2c7ff459d8a07c9a55488b4df53007ec0ed6a-400x225.gif"
caption="Different colors"
>}}

Another technique is to have multiple trails being drawn at the same time. In
the animation above, it looks like the camera is panning, following the pulsing
circles. But it actually isn’t!

The circles calculates its size based on the size of the circle above it. In
other words, the radii of the circles are propagated down the circle stack.
It’s a technique that artist Zach Lieberman — who I absolutely revere — uses.
By doing so, you don’t end up with a gigantic array of circles and only have to
deal with a finite number of circles.

## Soddy Circles

Please don’t ask me who coined the term Soddy Circles. But let me explain it
anyways. Given three mutually tangent circles — that is, three circles touching
each other — a soddy circle is a circle that is tangent to **all three
circles** at the same time.

{{< image
alt="A moving diagram showing the relationship between soddy circles"
src="https://cdn.sanity.io/images/4hxtp24c/production/cadd643a8d1351b6c0f214533b6a83e310e48eff-600x600.gif"
caption="Soddy circles"
>}}

The construction of soddy circles begins with the construction of three
mutually tangent circles! Given three arbitrary points, you can always
construct three circles such that they are mutually tangent and where they
don’t contain one another.

There are a algebraic methods to solve this problem, but since my math is bad,
I opted for the geometric method. The blue lines you see above are the
intermediate construction geometries that you need to first draw in order to
find the solution.

Working on this problem felt like I was back in primary school, with a compass,
ruler and a piece of paper, working out what I could and couldn’t draw.

With that problem solved, it’s now time to find the soddy circles! You could
read up about it before hand, but in trying to solve this, I discovered a
property about soddy circles that freaked me out. It turns out, given the
constraints, you could always find an inner soddy circle. In fact, the previous
geometric constructions help you find it!

{{< image
alt="A moving chart showing three mutually tangent circles and their inner and outer soddy circles"
src="https://cdn.sanity.io/images/4hxtp24c/production/ee6a43856979e82d996f332ca7180a568e2760b8-400x400.gif"
caption="Inner and outer soddy circle"
>}}

However, there isn’t a clear formula for finding the outer soddy circle.
Logically, I thought that if there could always be an inner circle, then there
should always be an outer circle! But this assumption is wrong.

While there can always be a larger circle mutually tangent to all three, there
are times where it overlaps and thus negates the solution. Finding this out was
pretty neat but it wasn’t really artistic…yet.

{{< image
alt="Mutually touching circles moving around leaving colorful trails behind"
src="https://cdn.sanity.io/images/4hxtp24c/production/b0d9e97815c90958546286efab1812c6db3df2b4-600x600.gif"
caption="soddy rings"
>}}

It starts to look better with a little color and a trailing effect! The
trailing effect is the very same technique as the one mentioned in this
article.

{{< image
alt="Mutually touching circles moving around leaving colorful trails behind"
src="https://cdn.sanity.io/images/4hxtp24c/production/ac49775a9047bd8c4dd5b457b1ad3ede35a51940-400x400.gif"
caption="soddy rings with transparency"
>}}

Adding some transparency and shortening the trails as new ones are drawn gives
a more interesting result.

{{< image
alt="Mutually touching circles moving around leaving colorful trails behind. It is interacted upon by a cursor."
src="https://cdn.sanity.io/images/4hxtp24c/production/4c55316c9d15ba50b73437dc00aa9151465b17b9-400x400.gif"
caption="Interactive soddy circles"
>}}

And lastly, I added some interaction. If you’d like to play with it, [click
here](http://hic.art/52096)!

## Trees

One technique I explored with circles was the idea of trees. I formulated the
problem this way:

- Start out with a circle in the center
- Spawn a circle that doesn’t intersect or contain another existing circle
- Repeat until the screen is filled

{{< image
alt="A black square is progressively filled with circles, branching out from a center one."
src="https://cdn.sanity.io/images/4hxtp24c/production/16f61981ef117e51d2315201ec0db6d0abab3421-500x500.gif"
caption="Branching circles"
>}}

I animated this to solve one step of the problem per frame and coded up my own tree structure to store the relationships. Every time a circle is spawned, it stores a reference to the circle it is tangent to and vice versa.

{{< image
alt="A black square progressively filled with branching circles, but only lines between root and leaf circles are drawn, creating branch like image"
src="https://cdn.sanity.io/images/4hxtp24c/production/601888e7b70ccc756c031c10b7f172dbd3b5d2a5-400x400.gif"
caption="Branching circles, only with connecting lines"
>}}

Varying the size gives you patterns with different qualities. Drawing the lines between the circles shows you a root like structure.

{{< image
alt="an image of a glowing, pulsating light traversing a tree, leaving a trail behind"
src="https://cdn.sanity.io/images/4hxtp24c/production/122f8b2b191a240f3598acf9a0effe89617a72e7-400x400.gif"
caption="Traversing the tree of circles"
>}}

Again, combining this circle tree technique, I can now draw a circle trail to show the relationship between the circles!

I wrote some code to traverse my circle tree so that I could draw a pulsing trail.

It’s not obvious in this highly compressed gif, but I added some post processing — from within OpenRNDR itself — to lend some pizazz to the final output. Some bloom goes a long way!

{{< image
alt="traversing the circle tree but with poisson filling"
src="https://cdn.sanity.io/images/4hxtp24c/production/b3b9a87d29cece1f19f00443aeb4a8c5641eca5e-400x400.gif"
caption="Poisson filled"
>}}

Doing Poisson fill on it yields satisfyingly smooth gradients. Click the image to see the original one on Instagram as GIF compression kills it!

## Other Techniques

Here are some other circular ideas I tried out.

### Spirograph

Another idea that took me back to my primary school days was the spirograph. One of those mathematical drawing kits where you traced the path of a smaller circle rolling within a larger circle.

{{< image
alt="A moving image of a wonky spirograph-like apparatus leaving pulsating trails"
src="https://cdn.sanity.io/images/4hxtp24c/production/0d3baacab68a87ed6f097ba75015c3a09ed35376-400x400.gif"
caption="spiroplayer"
>}}

Fortunately for creative coders, we don’t have to simulate everything so that it represents physical reality! You can tweak how fast the inner circle rotates around its own center as well as how fast it rotates around the larger circle. Varying this yields different patterns. In fact, I made [an interactive version](http://hic.art/59749) where you can do exactly just that!

### Physics

{{< image
alt="a mouse cursor herding white circles on a black background"
src="https://cdn.sanity.io/images/4hxtp24c/production/368211330c16a23bf8bfc299cebf3551e39c59fd-400x400.gif"
caption="chasing circles"
>}}

I have never really explored physics in creative coding before so I decided to try my hand at it!

What you see at your left is a bunch of circles whose radii vary over time. They are all trying to chase the moving dot but they get pushed around by each other as they do.

Doing this simulation, I noticed something. The circles naturally form some kind of local optimum for packing themselves, with the smaller circles always somewhat in the interior.

As usual, I always try to see if I can iterate an idea and combine techniques I’ve already developed. An interactive version of these can be [found here](http://hic.art/65103).

> Always be iterating. — Zach Lieberman’s 2017 Motto

{{< image
alt="a mouse cursor grabs a circle and throws it around, deforming it like a pizza in the air"
src="https://cdn.sanity.io/images/4hxtp24c/production/be8b4471bf5677fba1c6e5c09cfe75e1e92b2b51-320x320.gif"
caption="Deform"
>}}

Another physics technique was to use spring forces!

You can manually create a circle mesh and then connecting all the vertices with a spring.

By varying the spring’s stiffness and damping, you alter the behavior of the circle significantly.

For the interactive version, [click here.](http://hic.art/102576)

### Vertex Transformations

This technique is a bit niche, but it’s based on the way the graphics card draws things on the screen — specifically, the way OpenGL draws things on the screen.

Without, getting too much into detail, you can feed OpenGL a bunch of vertices, tell it to draw a circle and it will draw a circle. But why do this manually when you can just `circle()` in any good creative coding framework?

The answer: **vertex attributes.**

Vertices aren’t just point on a screen. They contain data such as normals, texture coordinates and color. By manually feeding the data, you can get OpenGL to do stuff for you that normally would be too tedious to do yourself.

One such thing would be gradients!

{{< image
alt="an interface showing a circle with rainbow gradients. A vertical and horizontal slider on the left and bottom moves around as the gradients change"
src="https://cdn.sanity.io/images/4hxtp24c/production/dc73b52de91c3ba463ced57acddce354e9b05c04-400x400.gif"
caption="Phosphene"
>}}

The interactive version can be found [here](http://hic.art/76467).

### Webbing

Another reason to think in vertices is webbing!

{{< image
alt="Relationships between moving circles are drawn by connecting nearest points between them"
src="https://cdn.sanity.io/images/4hxtp24c/production/b46feedad53b5a718ff713da970f0e3a5ce3a486-400x400.gif"
caption="Webbed circles"
>}}

By defining my circles as a set of vertices, I have access to these vertices. I can do maths over them. I can iterate over each vertice and check how close they are to the vertices of other circles. If they are near enough, I draw line.

### Dubin Paths

Dubin paths are a specific kind of curve. Long story short, they can be defined with circles.

{{< image
alt="a trail is being drawn in dubin curves"
src="https://cdn.sanity.io/images/4hxtp24c/production/02fba640d73ed88565ba0c5addadd5542ac623a9-400x400.gif"
caption="dubin path"
>}}

The algorithm for this is simple:

*   Trace along the circumference of a circle
*   At an arbitrary point, spawn a circle that is tangent to the current one
*   Repeat

## Summary

Thanks for reading this article all the way until the end! I hope some of these
techniques inspired you somewhat. I encourage you to give it a go and try it
out yourself. If you create something you feel worth sharing, reach out to me
on Instagram or Twitter!

It can’t be said enough how important it is to iterate and look for
combinations. There are quite a few artists doing daily art—[Alexis
Andre](https://www.instagram.com/mactuitui/), [Zach
Lieberman](https://www.instagram.com/zach.lieberman/), [Saskia
Freeke](https://www.instagram.com/sasj_nl/), [Erika
Anderson](https://www.instagram.com/ericaofanderson/), [Jason
Ting](https://www.instagram.com/mactuitui/), just to name a few — and they all
do just that: iterate.

## Recommended reading/watching
- [Constructing tangent circles](https://www.ics.uci.edu/~eppstein/junkyard/tangencies/three-circles.html)
- [Soddy Crescent Construction](https://www.codeproject.com/Articles/808880/Soddy-Crescent-Construction)
- [The Book of Circles](https://www.goodreads.com/book/show/31934617-the-book-of-circles) by Manuel Lima
- [Dubins Paths](https://en.wikipedia.org/wiki/Dubins_path)
- [Vertex Buffers and Drawing a Triangle in OpenGL](https://www.youtube.com/watch?v=0p9VxImr7Y0&t=646s)
- [Programming Balls #1 Circle Vs Circle Collisions C++](https://www.youtube.com/watch?v=LPzyNOHY3A4)
- [Coding Challenge #160: Spring Forces](https://www.youtube.com/watch?v=Rr-5HiXquhw&t=1411s) on Coding Train
