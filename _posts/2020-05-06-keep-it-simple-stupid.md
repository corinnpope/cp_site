---
layout: post
title:  "Keep it Simple, Stupid"
date:   2020-05-06 09:08:49 -0600
categories: development
---

# Keep it Simple, Stupid
May 6, 2020


Speckled has been a work in progress for 3 months now. Crazy!

It's been a blast, but as like most programming projects it has taken about 3x longer than expected. My programming skills are getting better and I'm getting faster, but it's still mildly infuriating to close my eyes at night and _feel_ like I've made negative progress on my codebase, even if I did learn a bunch. 

## Education != Knowledge

Part of my slow progress has been due to my naivete. I started this project with little understanding of how models work in real projects. I only had the academic knowledge that database tables should be normalized, with foreign keys linking related tables and things repeated as little as possible. 

When I drew everything out, I broke everything down into wayyyyy too many tables. For the main functionality of scoring things, I had the following tables:

* Items
* Formulas
* Factors
* Scores
* Composite Scores

## Stuck in has___many___through Hell

The number of nested relationships I had to keep track of was becoming a serious time and energy suck. I was getting stuck very frequently with just trying to figure out how to access some of the records in my database. 

Need an item's score for "factor x"? Well, you'll have to join the formulas and factors tables to find which ones belong to their team, and then find the record where the item_id is the one you're looking up, and then get the value from that row. Or something like that. I try to forget. It was a lot of rigmarole.

One day, I was trying to debug an issue with a friend (I had/have a lot of issues). His blank stare as I tried to explain some of the relationships to him was an indicator of how crappy my initial design decisions had been. 

You know that feeling when you're explaining something to someone and in the middle of your explanation, you go "Shit. There's gotta be a better way to do this"? Well, I had that feeling while talking to him...more than a couple of times. 

## Tear It All Down

So, like any good teacher, he suggested I simplify it. 

Suddenly, five tables became two. TWO freaking tables! Oh, how much easier that would be to manage! 

It made so much more sense. 

However, I did feel slightly annoyed at myself for not having thought of it before, even though I am still a beginner and should be a little gentler on myself. Only with experience will some of this stuff start to click. 

Thankfully, other people have way more experience than me and can provide guidance when I'm stuck. 

Now I'm refactoring everything to use these simplified models. 


## What did I learn?

Well, I now know _a lot_ more about relationships in Rails then most advanced beginners, so I got that going for me. 

I learned that having a teacher who has more experience than you is one of the fastest ways to learn and see your mistakes.

And third, I learned it is almost always better to keep things simple. 

After all, why use 5 tables when 2 will do?