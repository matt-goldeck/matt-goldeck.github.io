---
layout: post
title:  "New Blog, Who This? Or: A Coming of Age"
date:   2021-01-01 12:00:00 -0500
categories: ramble
---

Friends, randos, netizens, lend me your ears:

I come to bury my Django blog, not to praise it. The wretched maintenance that such projects demand lives after their prime; the good is oft interred with their bones.

So let it be with the Django blog. The noble Goldeck hath told you the blog was ambitious, perhaps even 'sick af': if it were so, it was a grievous fault, and grievously hath the blog answer'd it.

-----

As my time at university began to wind down and my ambitions wandered towards professional endeavors, I became obsessed with having an honest-to-god, big-iron database-backed, built-with-my-own-two-hands, knock-your-socks-off cool dynamic website entirely of my own creation. 

Partly, I think this was because I was brought up to value building things, building them the right way, and finding meaning in the things you've built.

I also think somewhere in the tumult of my college years this became distorted into some self-righteous John Galt-esque mantra of "I must do everything myself for it is the only way", which if not obvious to you the reader, is probably not an adage to be applied to everything.

![me, circa 2019](https://upload.wikimedia.org/wikipedia/commons/a/a1/Dore-I_watched_the_Water-Snakes-Detail.jpg)

So this albatross hung around my neck: if I am to do something interesting, I would probably want to blog about it, and if I am to blog about it, I must make the blog myself.

Of course this was totally ridiculous. I spent my time in university toiling away at Django-driven interface for my research lab. I threw Flask at every academic assignment that crossed my way. I even work professionally full-time on what amounts to a complicated series of web-apps. I don't even care that much about web frameworks! Yet once the mania descended, all I knew was that I was worthless without the big-iron blog.

[I eventually built this albatross](https://github.com/matt-goldeck/pine-raider) using my ideal stack: a Django driven, psql-backed blog with dynamic user-generated media hosted in S3. It had user accounts, an admin page, a bunch of complex templates and settings, and was hosted on AWS with 24/7 uptime and no waiting for a dyno to spin up. 

And it was... just *okay*. Having finally accomplished what I thought so desperately needed to be done, the end result was something that:
- Costs money on a recurring basis
- Requires maintenance on a recurring basis
- Has a large and undocumented footprint 
- Generally looks pretty not-great, as I'm not an FE engineer
- Stresses me out and gets in the way of writing and doing what I want

I rode the high for a few weeks but very quickly forgot about it. The next time Heroku and AWS hit my credit card, I just switched the site into maintenance mode and let it die. 

![](https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/InstrumentShopsatNBS_011.jpg/1280px-InstrumentShopsatNBS_011.jpg)

When the smoke cleared and I starting reflecting on where I'd gone wrong, I started looking at the blogs of those in tech that I admire, and I pretty quickly figured it out. 

[Paul Graham's blog](http://www.paulgraham.com/) is distinctly spartan. [Tobi Lutke's blog](https://tobi.lutke.com/) is well-styled, but wholly indistinguishable from your standard wordpress site. John Carmack doesn't even seem to have a blog, [he just hangs out on Twitter](https://twitter.com/ID_AA_Carmack). 

The technical and procedural autopsy of my ill-conceived architecture could fill a book, but it wouldn't scratch the root of the problem -- being a professional engineer, or even an adult I suppose, is about *choosing your battles*. In essence: if a pipe burst in John Carmack's kitchen, he's certainly capabale of watching a YouTube video and fix it himself, but why would he want to? He's probably a bit busy changing the face of virtual reality.

Now if you find some fullfillment in building blogs, if to you it is novel and exciting, or if you find a meaningful hobby in plugging away at boilerplate monoliths, then by all means please venture forth and conquer. But I don't, it wasn't, and currently employed in such endeavors, I can decidedly say it's not hobby-material.

So this is a very long winded way of saying I've gone to the darkside and migrated to [Jekyll](https://jekyllrb.com) via [GitHub pages](https://github.com/jekyll/jekyll). I will not apologize. It's lightweight, stupid-easy to configure, and a felicitous choice for blogging. 

The technical minutia of *why* doesn't really matter -- the technical details of things that I don't care about are staying out of the way of technical details that I do care about. I've chosen my battle: I want to build other things, not this thing.
