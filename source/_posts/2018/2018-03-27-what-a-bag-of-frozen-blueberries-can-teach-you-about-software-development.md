---
title: What a Bag of Frozen Blueberries Can Teach you about Software Design
tags:
  - design
date: 2018-03-27 21:30:00
alias:
---


I recently opened my freezer found this bag of frozen blueberries:

{% asset_img IMG_9826.jpg %}

I was a bit surprised to say the least, especially since the bag has a "E-Z Tab" along with a "PULL TO OPEN" (all caps) instruction label.

{% asset_img IMG_9827.jpg %}


What does this have to do with software development? Everything. The rest of this post compares the bag of blueberries with software development.

## What happened?

The most obvious thing to ask is why is their such a huge tear in the bag? Clearly someone didn't take a few seconds to look at the bag and see that it already had built-in resealable functionality. Instead they determined it faster to tear open the bag (which it wasn't) and then spent the additional time to find an elastic and in an unsuccessful attempt (first image) seal it back up.

## Bad Design Encourages Bad Design

I've encountered the "torn blueberry bag" in many systems I've worked on over the years (some which have been my doing). I noticed that they all share the same pattern. A bad/quick design was put in place and then everything was piled on top of it just making the problem worse. When I run into these issues I try to analyze them and see if/how we can improve them. Here's my detailed analysis on the bag of blueberries:

### 1- The Tear

This was a horrible decision for various reasons. First the opening now is too big which leads to more problems such as spillage, and does not make it easy to re-seal (more of this later). The "man-made" hole is not straight. Taking a pair of scissors would have made it more uniform, thus easier to maintain in the long run.

### 2- The Elastic

Now that the bag has been (poorly) torn the solution to re-seal it was to use an elastic that clearly wasn't properly put on. This lead to a few stray blueberries scattered in the freezer when I took the bag out. 

The elastic is a key point in this solution. When shortcuts occur in software people tend to focus on the elastic and come to the following conclusions:

- The elastic wasn't put on properly
- Since the elastic wasn't put on properly, the freezer had a bunch of loose blueberries in it
- I spent time cleaning out the freezer to get the loose blueberries

It's easy to blame the how the elastic was put on and the side effects it caused but it's the wrong approach. Instead of focusing on issues right in front of you, step back and ask yourself if this was the root cause of the problem or a side affect of a bad design decision.

### 3- The Future

For the rest of the life of the bag I have to find a way to properly reseal it. Sometimes the hack elastic job will work, sometimes it won't. I'll probably still be dealing with stray blueberries in my freezer, thus adding to the mess. Thankfully I like blueberries and will go through the bag quickly. I won't have to deal with the torn bag for too long but software usually lasts a very long time.


## Recap

A post-implementation analysis would have brought up the following questions:

- Did tearing the bag open rather than spending a minute to see if there was a better way save time? No.
- Did tearing it open make the situation better? No.
- What gain was made quickly tearing the bag open? None.
- What additional side affects were caused because of it? Wasted time, spilled blueberries, and this blog post ;-).

The perceived gain is that someone accessed the blueberries quicker for their needs. The reality is they just made things harder and created a compounding problem.

## Lessons Learned

- When working on software projects always remember that taking a some extra time to think things through and review options can save a lot of time
- Rushed decisions usually lead to bad designs
- When encountering these issues, focus on the root cause rather than the symptoms
- Bad designs lead to more bad designs
