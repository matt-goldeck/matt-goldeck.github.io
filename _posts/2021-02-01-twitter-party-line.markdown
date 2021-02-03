---
layout: post
title:  "Twitter as a Party Line Or: How I Can't Learn to Stop Worrying and Love the Bomb"
date:   2021-02-01 16:00:00 -0500
categories: project
---
 
Social media caustically eroding our social fabric gives me anxiety. So I made [this](https://avalanche-puce.vercel.app/):
 
![](/assets/twitterparty/screenshot.png#center-img)
 
It's a goofy and rickety React app I conjured up over a few days on top of the cheapest and silliest stack I could muster.
 
As if Jack Dorsey lived in a rural 1930s farm town with a party-line, I bring to you the top 50 trending topics of Twitter come to life and blasting directly into your ears via a multitude of voices and accents -- all courtesy of [your browser's built in TTS system](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance).
 
I'm not an artist, political theorist, social scientist, or even a particularly great full-stack developer -- so take this all with a huge dump truck of salt and don't laugh at me too hard.
 
**Stack**
 
It's very silly and very free, and mostly courtesy of Heroku.
 
![](/assets/twitterparty/overview.png#center-img)
 
* A PSQL database via Heroku to store collected tweets
* A scraper script written in Python that isn't actually a scraper script because it doesn't scrape anything and just uses Twitter's API
* A FastAPI backend to source data from Heroku via REST
* A React frontend built with Create-React-App and hosted on Vercel
 
 
**'Scraper'**
 
I originally envisioned this as something that would scrape twitter every 15 minutes for fresh tweets, probably with BeautifulSoup, but very quickly realized Twitter provides an API with *fairly* generous rate limits.
 
The [full script](https://github.com/matt-goldeck/avalanche_be/blob/master/src/scraper.py) involves:
1. Pulling the top fifty topics from [Twitter's API](https://developer.twitter.com/en/docs/twitter-api/v1/trends/trends-for-location/api-reference/get-trends-place) for New York City.
2. For each topic, pulling one hundred of the most recent tweets [from the API.](https://developer.twitter.com/en/docs/twitter-api/v1/tweets/search/api-reference/get-search-tweets)
3. Parsing all retrieved tweets, comparing given ids against existing stored ids to protect against duplicates
4. Trimming enough tweets from the database (Heroku enforces a 10k row limit on free PSQL databases) to insert
5. Inserting all tweets in a huge insertion query
 
Unfortunately, I later found out Heroku's free scheduler only allows you to run jobs in 10-minute, 1-hour, or once-a-day intervals.
 
I could run in 10-minute at half capacity or 1-hour at full capacity to stay within Twitter rate-limits, but then I'd exhaust all my free Heroku dyno time, so I opted to just run this once a day at full capacity around 1 PM EST.
 
**'Avalanche' React FE**
 
My exposure to developing FE apps is *very* limited.
 
All the cool kids in college threw React at every problem, and at work a lot of the stuff I build eventually empowers React apps, so I figured it was a good place to start seriously dipping my toes into the JavaScript world. Since then I've been toying with online React tutorials and learning bits and pieces here and there, but I don't think I'm very good at this yet.
 
![](/assets/twitterparty/avalanche.png#center-img)
 
The ideal path should ideally be as follows:
 
A primary component [PartyLine](https://github.com/matt-goldeck/avalanche/blob/master/src/PartyLine.js) is loaded and calls a function [bufferTweets()](https://github.com/matt-goldeck/avalanche/blob/master/src/PartyLine.js#L105) on `componentDidMount()`. A sub function `getTweets()` retrieves 250 random tweets from our FastAPI `/tweets` endpoint. After tweets have been retrieved, another sub function [listenForBufferToLoad()](https://github.com/matt-goldeck/avalanche/blob/master/src/PartyLine.js#L105) is called to keep an eye on the buffer to call [loadTweet()](https://github.com/matt-goldeck/avalanche/blob/master/src/PartyLine.js#L105) when the first tweet is loaded onto it. It does this because sometimes my free Heroku dyno takes 10-30 seconds to spin up, and thus, the promise chain of the standard fetch-then pattern will keep chugging on without any content retrieved until it realizes there aren't any tweets loaded and explodes violently. There's probably a more elegant solution to this, but as I said, I have no idea what I'm doing.
 
`loadTweet()` pops a random tweet from the buffer and plops it onto an array `loadedTweets`. After this, tweets are only loaded onto the `loadedTweet` array when a tweet's content is finished uttering -- but we'll get there soon.
 
Because I firmly believe a website should not blast sound into your ears without at least asking first, a component [ConsentPrompt](https://github.com/matt-goldeck/avalanche/blob/master/src/ConsentPrompt.js) greets the user and asks for consent to begin loading tweets. Only once consent is granted is the first [Tweet](https://github.com/matt-goldeck/avalanche/blob/master/src/Tweet.js) component mapped from the `loadedTweets` array and rendered in the DOM.
 
The `Tweet` component displays the tweet's content and reads it aloud. It slowly loads its content from props into state to give the effect of loading the text onto screen. In conjunction with this, it uses [SpeechSynthesisUterrance](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesisUtterance), part of the broader Web Speech API, to read aloud the tweet's content. I decided to parse out any URLs with a regex from the content before being uttered. I think they add quite a bit towards cluttering up the visual space, which is nice and stressful, but they're entirely unintelligible when read aloud, which is just annoying.
 
`SpeechSynthesisUtterance` has a cool property `onend` to which we pass as a callback to our friend `loadTweet()`. This has the effect of loading a second tweet onto the buffer as soon as the first is finished uttering, and so on and so forth until the buffer is [close to empty](https://github.com/matt-goldeck/avalanche/blob/master/src/PartyLine.js#L44) and another 250 are loaded. If you're a true masochist and exhaust all 10,000 tweets in the database, we'll reset the offset and start from the beginning.
 
 
**Software as Civic Infrastructure**
> We shape our tools, and thereafter our tools shape us. -- Marshall McLuhan
 
At the turn of the last century, as cities in the United States became congested and squalid, politicians and city planners devised a fusion of politics, philosophy, and urban planning known as the [City Beautiful movement](https://en.wikipedia.org/wiki/City_Beautiful_movement). Its proponents argued that through building cities that were beautiful, pleasant to live in, and conductive to social harmony, we could shape the society that resides in them towards a more harmonious nature.
 
![penn station 1930s?](https://upload.wikimedia.org/wikipedia/commons/6/60/Penn_Station_interior.jpg#float-left)
Argue its efficacy all you want, but the base principles are provably true: you feel like a Greek god on a trip through Grand Central Terminal; you feel like a rat scurrying out of Penn Station.
 
So if we try to make our physical civic spaces beautiful and harmonious, why isn't the internet the same way?

The internet has certainly become a civic space -- perhaps the most daring and all-encompassing civic space we've ever constructed. It's the fantastical downtown business district of all of humanity. Here we do our learning, shopping, working, and socializing, and as we spend time here and shape it, it too spends time with and shapes us. 

And as we have taken very little care in shaping it, the internet is not a City Beautiful. The big picture of the mainstream internet is that of a raging, churning, violent cesspool -- and the shaping it's doing to us is not [of the harmonious type](https://en.wikipedia.org/wiki/2021_storming_of_the_United_States_Capitol).

Let's play a game: pick a FAANG and append 'statistic' to the Google search:
* Facebook now has somewhere around [2.7 billion monthly-active users](https://www.statista.com/statistics/264810/number-of-monthly-active-facebook-users-worldwide/).
* Google handles upwards of [3.5 billion searches a day](https://www.internetlivestats.com/google-search-statistics/).
* Netflix is fast approaching [200 million paying subscribers](https://www.comparitech.com/tv-streaming/netflix-subscribers/).


![penn station 1910](https://upload.wikimedia.org/wikipedia/commons/a/ab/Penn_Station%2C_Interior%2C_Manhattan_1935-1938.jpg#float-left)
This is more traffic than Penn Station will see in [100 years at current levels](https://www.statista.com/statistics/552797/amtrak-ridership-leading-passenger-station/). Even more traffic than a [continental interstate system](https://www.fhwa.dot.gov/policyinformation/tables/02.cfm) could ever cope with. These are very serious, nigh science fiction-esque levels of usership the likes of which humanity has never grappled with -- and it doesn't seem like we're taking that too seriously.

These aren't silly things. Very rapidly, without us realizing, these silly things became very impactful, very tangible, and very un-silly parts of every person's daily life.

And yet the average trip to Facebook is *stressful* in the same way a trip through Penn Station is. Your closest friends and family are screaming at each other, algorithmically curated content calculated to elicit your attention is incessantly blasted into your eyeballs from every corner of the page, and the feed is designed to continue to drip feed you this torrent of pain until either you die or navigate away.
 
Yet you can't *just* navigate away. No, it's not that easy anymore. You share pictures with grandma there, you sell your old stuff there, you chat with your old college friends and plan your parties there. It's become a meaningful fixture of your civic infrastructure, and much like Penn Station, no matter how many gastly sights and smells you trudge through on a given Tuesday morning, you'll be back that Tuesday night.

We care so much about our public spaces that we've built great walls of civic protection around them. If you want to so much as build a new sidewalk in Greenwich Village you'll need to get an environmental impact study, placate the Landmark Preservation Commission, and navigate a debacle of community board politics that would make Henry Kissinger himself cry. But if you want to build a service that 2.7 billion people actively build their lives around: by all means, do whatever you want. *Nobody* cares, there's *no* rules.

 ![chicago](https://upload.wikimedia.org/wikipedia/commons/b/b1/Delineator-_Jules_Guerin%3B_Architects-_Edward_Herbert_Bennett_and_Daniel_Hudson_Burnham_-_Plan_of_Chicago-_Civic_Center_-_Google_Art_Project.jpg#float-left)
I don't think it's anyone's fault per say. Decry Big Tech and the naive governments that failed to 'stop them', but we as a society were all equally complicit in whiffing our opportunity to build the City Beautiful. 

I think we (software engineers, governments, users, etc) didn't see it coming. We didn't realize we were building a city at all, much less a beautiful one. Software was silly, and for the most part just for a lark. Many of us still think this way. But somewhere along the line it suddenly became a very unsilly thing and we drove it off the tracks.

I think it's going to take a very long time for our culture to adapt and mature to this new environment we've built for ourselves. From both a cultural and biological perspective we currently are just not equipped to live and work in a place as raucous, ill-conceived, and all-consuming as our modern mainstream internet. I certainly don't know what that maturity will look like, I'm not even sure anybody does right now, I just hope we make it there.
 
I think the internet can be a wonderful thing, and like most things used in moderation, beneficial and enriching to our lives. I'm just not sure if moderation has been on the table for a while now.
 
Anyway, like I said: it scares me, so I built this silly thing.
 