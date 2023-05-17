---
title: "Learning ClojureScript with P5.js"
subtitle: "Creative Coding tutorial in ClojureScript"
date: "2022-12-25"
tags: ["ClojureScript", "P5.js"]
---

It's December and you know what this means. T'is the season to learn new
languages! Lately, I have been learning ClojureScript and have been having a
blast creative coding with it. I think you should give it a try.

## Who this tutorial is for
In order to follow this tutorial, you should be familiar with coding in P5
already. If you are, great! It will be a breeze to follow along. Let's get
started setting up.

Please note that I am not an expert in ClojureScript. Still, I'd like to share
this with you.

## Setup
I've created a [P5-CLJS template](https://github.com/somecho/p5cljs-template).
Go ahead and clone the repo to your machine and follow the instructions on the
`README.md`. Once the terminal shows `Build completed`, you can open
`localhost:3000`. You should see a red screen. I recommend using VSCode with the
plugin Calva. With this, we're now ready to code.

## First steps
Our sketch is located in the file `src/sketch.cljs`. It looks like this:

```clj
(ns sketch
  (:require [goog.object :as g]
            [p5 :as p5]))

(defn setup[]
  (js/createCanvas js/window.innerWidth js/window.innerHeight))

(defn draw[]
  (js/background 255 0 0 ))

(doto js/window
  (g/set "setup" setup)
  (g/set "draw" draw))
```

It looks a lot like our familiar P5 sketch, with the setup and draw functions.
Go ahead and try to change the background of the sketch! You can do so with
this line:

```clj
(js/background 100 180 180)
```

## Syntax

This line looks a lot different than Javascript. ClojureScript is a dialect of
Lisp. You can compile it to Javascript so that you can run it in the browser.

Being a Lisp, everything is an expression and every expression is surrounded by
parentheses.

`(+ 420 69)` is a valid expression but `+ (420 69)` is not.

By now you would have noticed, or you may already even know, that the first
item within the parentheses is a function. This syntax is called prefix
notation. The operation or function which we want to use comes first in an
expression. The syntax we are used to, like 1+1 is called infix notation.

## JS Interop

You can call global Javascript functions from within Clojure by prefixing
functions with `js` followed by a forward slash. For example: `(js/console.log
"Oi!")` calls `console.log("Oi!")`. You'll find that you don't need the console
logs, because `(println "Oi!")` does the same in ClojureScript.

Here's an [article about JS
Interop](https://lwhorton.github.io/2018/10/20/clojurescript-interop-with-javascript.html)
to find out more about how to call and use JS from within ClojureScript. For
us, this would be enough to continue coding.

## Rotating Rectangle

I've changed my sketch to look like this:

```clj
(defn setup []
  (js/createCanvas js/window.innerWidth js/window.innerHeight)
  (js/rectMode js/CENTER))

(defn draw []
  (js/background 0)
  (let [x (/ js/width 2)
        y (/ js/height 2)]
    (js/translate x y)
    (js/rotate (* js/frameCount 0.01))
    (js/fill 250 0 0)
    (js/rect 0 0 100 100)))
```

You should see a rotating square. Notice that whenever you change and save your
code, the sketch updates immediately! It's not immediately apparent, but the
state of your program remains. For example, `frameCount` continues
incrementing, even when you update your code. Go ahead and print it to the
console to see for yourself. This is a feature about ClojureScript I really
love.

However, if you make changes to the setup function, you'll have to refresh the
page manually. For code that executes only absolutely once (like creating the
canvas), put this in setup. For code you'd like to execute once every time you
change the sketch, just put it in global scope.

### The `let` block

`let` is a _special form_. It's used when you want to have local variables.
There are three parts to `let`. First is the let keyword itself, followed by
_bindings_, and then the expressions you want to run.

    
```clj
(let [a 50 
      b 100]
  (+ a b)) 
;;yields 150
```
 

The bindings are put in square brackets and are variable-value pairs.
Basically, in the example above, `a` takes the value of 50 and `b` 100. Once
the bindings have been defined, you can use the variables in your expressions.
Here is [`the official documentation for ClojureScript's
let`](https://clojuredocs.org/clojure.core/let).

## Tubes

Let's try this:

```clj
(defn draw []
  (js/background 0)
  (js/noStroke)
  (doseq [i (range (/ js/width 4))]
    (let [x (* i 4)
          y ( js/height 2)
          time (* js/frameCount 0.05)
          theta (+ (* i 0.05) time)
          r (* (+ (* (Math/sin theta) 0.5) 0.5) 255)]
      (js/fill r)
      (js/circle x y (+ r 50)))))
```
 

You should see a row of red circles growing towards the left. This is an example on how to use loops in ClojureScript

### DoSeq

To do loops, we can use the `doseq` special block. Like let, it has three
parts. It starts with the `doseq` keyword, followed by bindings in square
brackets and then by the expressions to be evaluated/executed. However, the
bindings here are a bit different. The value of the variable-value pairs needs
to be a sequence or a collection. This simply means an array or a list of
items. In the example above, our collection is `(range (/ js/width 4))`, which
basically is an array with numbers from 0 until 1/4th of our screen's width. Go
ahead and print it out in the console to see what it looks like!

Check out [the official docs about
DoSeq](https://clojuredocs.org/clojure.core/doseq) for more info.

### Too many parentheses!

There was a really gnarly line in our last example, when we were calculating
the radii of the circles. Three nested parentheses! Luckily, there's a feature
in ClojureScript called threading (not to be confused with concurrency). If
you're familiar with the command line, it's similar to piping! Let's take a
look at what that looks like.

    

```clj
(-> theta 
    (Math/sin) 
    (* 0.5) 
    (+ 0.5) 
    (* 255))
```
 

The expression starts with `->`, followed by our initial value `theta`. This
gets plugged into the next expression `(Math/sin)`. The output of this gets
plugged into `(* 0.5)` and so on. This, to me, is really beautiful, because you
can easily shuffle, insert and append operations if you need to without
worrying about the parentheses or operation order.

Here's [the official documentation to ClojureScript's
->](https://clojuredocs.org/clojure.core/-%3E).

## State

At this point, we've been doing everything statelessly. We don't have any
global variable which stores some data that we update. This is because
ClojureScript is by default immutable. Every expression returns a new value and
never modifies the values or variables you plug into it. This makes it really
easy to reason about the logic of our programs.

However, this doesn't mean that ClojureScript cannot handle state. It does so
with something called atoms. Here's a good article about [using state and atoms
in ClojureScript](http://langintro.com/cljsbook/atoms.html).

## About our template

We've gone this far without really talking about the template that we are
using. The build tool that we are using is _ShadowCLJS_. It basically acts like
a code hot reloader. In my opinion, it's the best build tool to use if you use
a lot of NPM packages.

In your sketch, you'll also see that it starts out with `ns sketch`, followed
by a couple of requires. This declares the namespace of sketch for the entire
file and pulls in the dependencies that we need: p5 and goog.object.

Goog.object is a module from Google Closure's library for working with JS
objects. Here's the [API for
it](https://google.github.io/closure-library/api/goog.object.html). Google
Closure library is incidentally a great library with many other modules for the
DOM, maths and more.

At the bottom of our sketch file, you'll see this code block:


```clj
(doto js/window
  (g/set "setup" setup)
  (g/set "draw" draw))

```
 

This just sets the global variables of "setup" and "draw" to our setup and draw
functions, which is how P5 works. The syntax may look a bit strange, since it
is using the `doto` special form.


```clj
(g/set js/window "setup" setup)
(g/set js/window "draw" draw)
```

 

This is exactly the same as above. Since `js/window` gets called twice, we use
the `doto` special form to reduce this. You may later want to add your
`keyPressed`, `mouseClicked`, `windowResized` functions. Using the `doto`
special form means you don't have to type `js/window` each time.

## What next?

This is it for this tutorial! It may seem like we covered only a little, but
actually, it's quite a lot. ClojureScript, being a Lisp, has very simple
syntax. This tutorial covers everything you might need to use for basic
sketches.

If you enjoyed this tutorial, I recommend going through a more thorough
document about the ins and outs of ClojureScript. This [online
ebook](https://www.learn-clojurescript.com/) is a great resource.

It's also worth trying out different tutorials using different build tools. For
me though, this can be rather confusing. I stuck to learning [about
shadow-cljs](https://github.com/thheller/shadow-cljs) instead, which has been
sufficient for my needs so far.

With _#Genuary2023_ just around the corner, I challenge you to follow along in
ClojureScript!
