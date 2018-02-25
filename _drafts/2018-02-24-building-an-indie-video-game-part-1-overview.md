---
date: 2018-02-24
title: How to Build an Indie Video Game from Concept to Release - Overview (1/7)
categories:
  - guides
  - video game development
  - ios
  - android
author_staff_member: dan
featured_image: double-diamond/double_diamond_overview.png
---

This is a multi-part series walking you through creating a video game from concept to release. I'll cover a development practice I use that is transferable to any type of software development. I'll take you through my failed games that didn't make it to market, what I learned along the way, and ultimately what I started doing that lead to 2 successful game releases, Slanky's Big Climb and Mort's Minions.

### Background
Ever since I built my first program, a basketball shooting Lego Robotics trebuchet, I fell in love with code development. If I recall the program was something like:
~~~~
1. Drive forward until the ground sensor sees the foul line
2. Rotate in a circle until the eye sensor sees the hoop
3. Launch the ball and miss almost every time
~~~~

Complicated, right? You'd be surprised how similar that logic is to the games I write today. In high school I was fortunate to take 2 programming classes and used the time to build a Breakout clone with VB6, then a 5 card draw poker game with Java. I didn't have a distribution platform for these games, so they went little further than showing my teachers on our school computers. It met the need at the time, but I had my eyes set higher! In college I built a FPS 3D game using OpenGL and C++ with a friend, in which your character ran around with a slingshot and shot "apples" at sheep to feed them (or at least that's what we told our professor _:)_) and once the sheep "got enough apples" they disappeared off the screen. This was really fun and we had a great start, but I spent too much time "socializing" and it didn't quite make it further than my laptop.

Skip ahead 5 years, I graduated college, starting working full time, got married, and realized I had never fulfilled my passion to unleash my own video game on the world. I set my mind to it, committed to releasing a game, and after a few false starts I have finally done it (twice) and "released" my own video games! Thanks to the amazing mobile distribution platforms that Android's Google Play and the iOS App Store provide, I have been able to get my game "off of my laptop" and into the hands of thousands of people in over 20 countries.

![Morts Minions First Day Numbers - over 10 countries and 100 downloads]({{ site.baseurl }}/images/morts-first-day.png){: class="screenshot center-block"}

> <sub><sup>These were Mort's Minions' first day numbers in the App Store. Sure it's nothing crazy, but the mobile app stores make it possible to go worldwide in the click of a button!</sup></sub>

It has been a fun journey and I have learned more than I could have ever imagined. All in all, before I released my first video game to the App Store, I had built at least 5 games that were at least 50% done, all completely playable to an extent, but they never made it to completion. Now that I have completed 2, I know what I did wrong, and I want to share what I learned so that you can avoid the same mistakes. Over the next several weeks I'll share what I have learned about starting and finishing a mobile video game.

### The Process: Double Diamond
So how do I make sure I actually release my work now? I built a pattern for myself that I later learned pretty closely followed the "Double Diamond" design pattern. I don't claim credit for this strategy, it is a pattern that is used all over and has been around since 2005 by the British Design Council. This is what it looks like:

![Double Diamond Process Overview: Idea -> Discover -> Define -> Vision -> Develop -> Deliver -> Iterate -> Release]({{ site.baseurl }}/images/double-diamond/double_diamond_overview.png){:.center-block}

1. **Discover**: Capture ideas for a game and explore what is possible. Think "Brainstorming"!
2. **Define**: Define a high level vision of what the game is
3. **Develop**: Build a Proof of Concept (POC) to test the viability of the idea early, with minimal effort
4. **Deliver**: Get early feedback on your POC and determine if the core game mechanics will work
5. **Iterate**: Incorporate POC feedback and build towards a minimum viable product by using a process of "Build, Measure, Learn".
6. **Release** & Post-release

I'll get into each section and how you can apply it to your game in the coming weeks. Stay tuned for **Part 2 - Discovery**!