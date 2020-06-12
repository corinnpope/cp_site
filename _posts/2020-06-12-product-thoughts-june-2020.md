---
layout: post
title:  "Product Thoughts - June 2020"
date:   2020-06-12 09:08:49 -0600
categories: personal
---

# Random Product Thoughts - June 2020
June 12, 2020

I woke up this morning and just had an insatiable urge to write. About what I'm not sure, so let's see how the words flow out of my brain this morning. Stream of consciousness...let's go! 

## Texas Covid-19 cases are rising...fast

Texas is currently seeing a massive spike in covid-19 cases. We're on a three day streak of record highs. If we have to go back into lockdown I'm going to be super bummed and a little bit pissed. 

Personally, I don't really go anywhere "normal" anymore. No library. No coffee shops. No restaurants. No Target runs. Nada. The only thing I still do on occasion is go to Trader Joe's. And even then, a TJ's run mean a mask and post-shopping shower. I do run outside, although on the commute through my building to the path I still wear a mask. 

Still, there's something heavier about being under official lockdown, even if without it you still follow all the "rules". 

## Datagrid successes (kinda)
Anyways, enough with the rant and negativity. Let's talk about something positive....

I had been struggling with this darn Vue datagrid I have in my app for months now. I knew at the beginning it was going to be the hardest part of building, but I didn't realize just how hard it would be. 

For experienced programmers, it'd probably be a breeze. Maybe it'd take them a week. But since I'm pretty new to Javascript _and_ vue, it was an exercise in patience and persistence that's lasted for many months. 

The table pulls from multiple database tables, has lots of nested JSON, requires sort, search, editing, and deleting. 

It look me embarassingly long to go from concept and trying to figure out if this needs javascript (I was holding out hope in the beginning) to actually getting it to a place where I can use it myself. 

But...now I can use it myself! And I'm using it to figure out my TODO list and rank it in order of priority. 

Up next are fixing an annoying roadmap bug that used to not be there. Then I need to start think about how onboarding is going to work. 

It's actually INCREDIBLY helpful. Now I just add things to my TODO list, rank them, and pick things from the top and work my way down. 

I get what they say about eating your own dogfood. Doing so has helped me tweak a number of things already and I'm sure will help me in the future. 

## Planning out user onboarding

About onboarding though...

I think the "aha" moment for people will be when they add their first 3-5 items and realize that what they thought may be "top" priority may not be. The question then is how do I get them to that point as fast as humanly possible? And then keep them coming back when they have more items to add?

Part of the things that are required to get them there include:
* having a scoring model (default RICE one created at account creation)
* creating a board (have a default board on upon account creation)
* adding items (haven't made any defaults, maybe I should add at least one)

I'm wondering if I should add some default items as kind of a meta onboarding. Let people edit them. Let them archive them. Show them on the roadmap. 

Would it be just one item? Or should there be a bunch of tasks?

Or maybe a quick demo video would be helpful.

I've also tossed around the idea of having a more detailed setup process that lets users select from multiple scoring models or create their own right after signup. There would be different options depending on what stage they're in and what they're looking for. 

BUT, making them create their own score and _then_ add items may be too complex. What if they get stuck on the scoring stuff and never add items...even if a custom scoring methodology would be better for them longer term?!

If they add items to the default model and _then_ customize their scoring model, they'll probably have a better intuition as to how the application works and how to handle adding scoring methods. I think that's probably the better way to go.

Plus, it'll be fastest to the aha moment. 

Both are worth testing out though once there are enough users to draw some conclusions. 

Onboarding plan: I'm going to add a default item and/or items and give them the option of watching a quick onboarding video. 

Guess I need to record a demo video.

## Getting them to come back

How will I get them to come back after they've prioritized their list? 

I'm not realy a fan of roadmaps with strict dates. I won't be able to ping them on things that are coming up or are "due" soon.

Could I see if they've integrated with anything and ping them when they've added something that may also need to be added to Speckled? Perhaps. Although depending on the user, I feel like this may overwhelm then with notifications and that's no beuno either.

I could go with the variable rewards system in the "Hooked" book model. Although it feels a little dirty. But then again, we're all PMs here so we all know the game and how to turn it off if we don't like it. Still. 

Maybe broadcast roadmap changes to slack. 

I'd also like to add custom backgrounds. That'd be fun. 

Or a little celebration when they mark something as done or archive something.

## Am I getting ahead of myself?

Little things. So many little things. They all need wrapping up. 

The last 10% of this project is taking 90% of the time. It's so frustrating. 

There are bugs in the datagrid. If you select more than one item to edit, you can only save one item. Anything else will be overwritten. 

There's a bug in the roadmap. If you drag a story away and that old column is now empty, it just disappears. 

There's a bug in bulk importing. The form only appears after refresh. 

There's a bug in asana imports. Sometimes they don't work. 

I haven't gotten around to changing the active class in the selection menu. It always looks like you're on the dashboard.

Still, I think it's good to think about big things like onboarding and retention in parallel with these little things. 

Just so many little things! And so many big things too!

## End ramble

I guess I should stop rambling and get back to work. That was oddly therapeutic though. 