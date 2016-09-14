---
title: Crafting Fractals
author: sarnthil
profile_picture: /images/sarnthil.jpg
about_stub: I struggle. I live.
author_site: https://github.com/sarnthil
---

>Why is geometry often described as "cold" and "dry?" One reason lies in its
>inability to describe the shape of a cloud, a mountain, a coastline, or a
>tree. Clouds are not spheres, mountains are not cones, coastlines are not
>circles, and bark is not smooth, nor does lightning travel in a straight line.  
> --- _Benoît B. Mandelbrot, The Fractal Geometry of Nature_

![Fractal turtles](/images/fractal_intro.png)

This post is out of nostalgy and because I am since my first fractal an
incurable mandelbrotian. Here you can hear about how I couldn't dig
Sierpinsky's triangle using turtles and how fun and slow is to dig fractals on
your own world. I haven't done this in survival mode cause I am a total
beginner of Minecraft and all those creatures spawning near my turtle terrify
me too much.

Back in the days when I studied
[Logo](https://en.wikipedia.org/wiki/Logo_(programming_language)) and I played
with fractals things were easier than in Minecraft because I could rotate the
head of my turtle to whatever angle I wanted whereas here I can only turn
90 degrees. This constraint can of course be tackled but the waiting time is
already too long without aproximating every curve to a staircase.

I tried out Lua for the first time not while working on the neural models that
I should work on but in the console given by my turtle using
[ComputerCraft](http://www.computercraft.info).

The first fractal I tried was the [Koch
Island](https://en.wikipedia.org/wiki/Koch_Island) which is at its first level
a square. It fits minecraft pretty well. I think it's a nice maze with which
you can fill your world...

![Koch island fractal](/images/koch_island.png)

My second try was the cross fractal, which didn't actually work. Its first
level was a square without a side but the 2nd level was a nice cross.

Third: [Levy](https://en.wikipedia.org/wiki/L%C3%A9vy_C_curve) which looks a
lot like a plant.  [L3viathan](https://github.com/L3viathan2142) helped me a
bit there with my function, after I stared at it for 20', not understanding why
I attempt to call `nil`. Here are some shots of this beautiful maze dug in
sandstone.

![Levy curve](/images/levy.png)

And the fourth fractal today is Squarepinsky! Since rotating by 60 degrees is
not that easy I switched to Sierpinsky's carpet which is a square divided in 9
identical squares but of smaller dimensions out of which a bigger one is in the
center of the initial square. All the small squares are again divided and so
on. These self-similar creatures have [interesting
properties](https://en.wikipedia.org/wiki/Sierpinski_carpet).

![Sierpinsky carpet](/images/squarepinsky.png)

I stop here for now cause my turtle doesn't move as fast as I wish.

You can look at the lua scripts for the fractals
[**here**](https://github.com/sarnthil/crafting-fractals/).
