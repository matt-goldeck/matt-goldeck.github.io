---
layout: post
title:  "Untying the Knot"
date:   2021-02-01 18:00:00 -0500
categories: project
---
My toxic trait is that I enjoy doing things myself when buying something off the shelf would have taken 10x less time. Thats's why, when it came time to send out invitations for my upcoming wedding, instead of doing the reasonable thing and using [The Knot](www.theknot.com) to manage RSVPs like everybody else, I decided to build a cool little web app.

You can find it [here](https://www.nicoleandmatt2022.com).

![](/assets/theknot/landing.png)

## Acceptance Criteria
1. We needed both:
    * A mechanism for our guests to RSVP
    * A repository of information for guests to reference
2. The record of an RSVP needed to be persisted somewhere
    * That somewhere had to be reliable -- any loss of data was unacceptable
3. The interface needed to be incredibly frictionless and easy to use. *Incredibly.* Your dog needed to be able to use it.
    * Combine this with a small guest list and authenticity of an RSVP isn't really a concern. This means absolutely no passcodes or emails or any of that crap.
4. It needed to work across a wide range of devices. Basically any device imaginable. 

## The Stack
![](/assets/theknot/diagram.png)
* Data is stored in [MongoDB Atlas](https://www.mongodb.com/pricing) under the 'Shared' tier.
    * Cost: **Free**
* A simple RESTful API written with [FastAPI](https://fastapi.tiangolo.com/) exchanges RSVP data hosted on [Railway](https://railway.app/).
    * Cost: Under the 'developer' plan:
        * Memory: `$0.000231 / GB / Minute`
        * CPU: `$0.000463 / vCPU / Minute`
        * `$5.00` free credit every month
        * Usage never exceeded free credit -> **free**
* A [React](https://reactjs.org/) webapp serves up some text, images, and a form for submitting RSVP info. Hosted on [netlify.app](https://app.netlify.com/)
    * Cost: Page hosting free, but domain $12/yr through Google domains. 

### Backend
[wedding-api](https://github.com/matt-goldeck/wedding-api) is a FastAPI hackjob hosted on [Railway](https://railway.app/). Railway is a handy IAAS alternative to Heroku that I saw mentioned on Reddit. I don't know much about it other than the fact its cheap, easy to use, and has a cool train logo. YMMV.

The API has three endpoints.
1. `POST /rsvp/` - Accept an RSVP object via JSON and store it in the DB.
2. `GET /rsvps/` - Retrieve a list of all RSVP objects in the DB via JSON.
    * Supports a quick and dirty admin view that displays all received RSVPs.
3. `GET /rsvps_csv/` - Download a CSV file containing all RSVP objects in the DB.

The latter two [sit behind an API key](https://github.com/matt-goldeck/wedding-api/blob/master/main.py#L48). This key is just configured as local environment var on Railway and very professionally provided to the FE client by the user. I expected to hit this second endpoint maybe a handful of times to monitor progress, and the third endpoint exactly once when it came time to make table arrangements. My life wouldn't end if my guest list was leaked to the internet, so I didn't really pay much attention to them.

Data is [passed to Mongo](https://github.com/matt-goldeck/wedding-api/blob/master/clients/database.py#L19) via the MongoDB SDK which is incredibly handy. I typically like to configure and use a RDBMS but at this small of scale and with this low of stakes it made sense to follow the path of least resistance. 

### Frontend
[wedding]() is a React webapp hacked together with my piecemeal knowledge and furious web searching.

I chose React because it's what I know. I also chose React because, if used correctly, it should perform well and scale smoothly on lots of different devices. 

It uses [ReactRouter](https://reactrouter.com/en/main/start/overview) to provide client-side routing so users can navigate around the site without having to make separate requests. This enables me to provide a slightly more performant experience with very limited (read: free) resources.

![](/assets/theknot/form.png)
*The RSVP submission form*

![](/assets/theknot/form-submitted.png)
*The form submitted*

#### Sidenotes
[BrowserRouter](https://reactrouter.com/en/main/router-components/browser-router) replicates the traditional web experience of storing the current navigational state via a clean URL and the browser history stack, but I had some issues with this early on and swapped over to using the uglier [HashRouter](https://reactrouter.com/en/main/router-components/hash-router). I forgot to transition back and didn't notice until just now. Woops.


In the 11th hour I decided it would be cool to see a list of all received RSVPs without having to log into MongoDB. I hacked together an [ugly and 'admin panel'](https://github.com/matt-goldeck/wedding/blob/master/src/rsvp_list/RSVPList.js) that accepts an API key from a form and passes it to the API. An optional link will download a CSV. How neat.