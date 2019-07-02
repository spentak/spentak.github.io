---
layout: default
title: AIController Won't Move Character
parent: Blueprint
nav_order: 1
comments: true
---
## My AIController Was Working But Now It Isn't!

So this thing keeps happening where I have a character with a custom AIController and he is moving and doing what he is suppose to do.  Then *suddenly*, out of nowhere, the character stops moving. In fact no AI seems to be working at all.

**GRRR**

This has probably happened to you and you are now reading this hoping for an answer.  We'll, I have the solution.

## What Probably Happened

What probably happened is you did a lot of your game testing for your AI with Blueprints that you *dragged* into the viewport.  But then you wrote some code that *spawns your AI character at runtime*.  

By default your AI is set to only work on AI's that are dragged into the viewport.

![](/assets/images/spawn.png)

## The Fix
<div class="code-example" markdown="1">
1. Go into your character Blueprint and look at the Class Defaults
1. Under Pawn look for Auto Possess AI
1. Change that from *Placed in World* to *Placed In World Or Spawned*
</div>

That's it. Now when you spawn your AI or drag it into the viewport it will work.


{% include disqus.html %}
