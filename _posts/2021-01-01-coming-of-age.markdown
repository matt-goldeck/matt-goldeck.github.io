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

To preface: I value building things, building them the right way, and I try to find meaning in the things I've built.

I think somewhere in the tumult of my college years this became distorted into some self-righteous John Galt-esque mantra of "I must do everything myself for it is the only way", which if not obvious to you the reader, is probably not an adage to be applied to everything.

As my time at university began to wind down and my ambitions wandered towards professional endeavors, I became obsessed with having a blog. Being an 'engineer's engineer' I had to do it 'the right way' -- it would have to be a big-iron PSQL backed, 'built with my own two hands', dynamic whirlwind of a blog that would knock any hiring manager's socks off and prove to the world how cool I am.

![me, circa 2019](https://upload.wikimedia.org/wikipedia/commons/a/a1/Dore-I_watched_the_Water-Snakes-Detail.jpg)

So I had hung this albatross around my neck: if I am to do something interesting, I would want to blog about it, and if I am to blog about it, I must make the blog myself.

Of course, this was totally ridiculous. I spent my time in university toiling away at a Django-driven interface for my research lab. I threw Flask at every academic assignment that crossed my way. I even began working professionally full-time on what amounts to a complicated series of web-apps. I had all the web experience in the world, it really wouldn't do much more for me -- but the mania had long since set in and the only way out was through.

[I eventually built this albatross](https://github.com/matt-goldeck/pine-raider) using my ideal stack: a Django driven, psql-backed blog with dynamic user-generated media hosted in S3. It had user accounts, WYSIWYG post editing, a bunch of complex templates and settings, and was hosted on AWS with 24/7 uptime and no waiting for dynos to spin up. 

And it was... just *okay*. Having finally accomplished what I thought so desperately needed to be done, the end result was something that:
- Costs money on a recurring basis
- Requires maintenance on a recurring basis
- Has a large and undocumented footprint 
- Generally looks not-great, as I'm not an FE engineer
- Stresses me out and gets in the way of writing and doing what I want

The next time Heroku and AWS hit my credit card, I just switched the site into maintenance mode and let it die. 

![](https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/InstrumentShopsatNBS_011.jpg/1280px-InstrumentShopsatNBS_011.jpg)

I forgot about it for a long time, but when the smoke cleared and I starting reflecting on where I'd gone wrong, I started looking at the blogs of those in tech that I admire, and it didn't take too long to figure it out.

[Paul Graham's blog](http://www.paulgraham.com/) is distinctly spartan. [Tobi Lutke's blog](https://tobi.lutke.com/) is well-styled, but wholly indistinguishable from your standard wordpress site. John Carmack doesn't even seem to have a blog, [he just hangs out on Twitter](https://twitter.com/ID_AA_Carmack). 

The technical and procedural autopsy of my ill-conceived architecture could fill a book, but it wouldn't begin to touch the root of the problem -- being a professional engineer, or even an adult I suppose, is about *choosing your battles*. I seriously doubt that John Carmack is incapable of reading some Django docs -- he'd just rather spend his time changing the face of virtual reality.

And that ability to compromise is really one of the tentpoles of engineering: we abstract and delegate to gain a lot of time and productivity. In software we have these crazy things called programming languages. In electrical engineering you probably source your components from a trusted supplier. In civil engineering you might use prefabricated sections and standardized grades of materials. It's all the same: we're working towards higher and more important goals, and we don't always have the time or specialized skill to literally reinvent the wheel. It doesn't make you a lesser engineer; in fact, it often makes you a better one.

Building and maintaining the blog was easy. Over its course it probably eventually cost me 10-20 hours of time spent actually working, which honestly is very small potatoes. It won't haunt me and nobody is judging me; this is a very first-world non-issue. It just deeply irks me that mentally I was so caught up in the popular 'lone cowboy' conception of the software developer. Had you told me any of this over coffee I would have unequivocally agreed with you and waxed poetic about the importance of abstraction, but here I was trapped in my own minor Ayn Rand fever-dream spiral. 

Humans are weird I guess.

So this is a very long winded way of saying I've gone to the darkside and migrated to [Jekyll](https://jekyllrb.com) via [GitHub pages](https://github.com/jekyll/jekyll). I will not apologize. It's lightweight, stupid-easy to configure, and a felicitous choice for blogging. 

The technical minutia of *why* Jekyll doesn't really matter -- the technical details of things that I don't care about are staying out of the way of technical details that I do care about. I've chosen my battle: I want to build other things, not this thing.
