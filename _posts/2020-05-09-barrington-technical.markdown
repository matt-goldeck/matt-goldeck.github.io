---
layout: post
title:  "The Barrington 16mm Film Scanner - Technical"
date:   2020-05-09 13:14:00 -0500
categories: barrington
---

![header](https://pineraider-static.s3.us-east-2.amazonaws.com/media/blog_images/5/5-0.gif#center-img)

I had endeavored to find a way to digitally preserve my family’s collection of 16mm home movies. This comprised 22 400-foot rolls and 11 50-foot rolls, most of which were badly damaged.

I will now explain to you in absurdly high detail how I accompolished this.

## Observations 

My first thought that there was two approaches to this: projecting the film traditionally and recording the output, or scanning the film frame by frame for maximum detail. 

The first is much, much easier and probably good enough for most people, but you’re not so much scanning the film as the blur that occurs as its whizzes by, so there exists somewhat of a ceiling on quality.

I decided I wanted to have a little fun and try it the slightly more thorough way: I would build something capable of automatically scanning each individual frame of a reel of film and processing it into a completed mp4. 

I trawled about dusty old hobbyist forums and analog film communities on Reddit and found a few avenues of attack. An open-source project called [Kinograph](https://www.kinograph.cc/) exists, and some folks had [great success in implementing it](https://www.youtube.com/watch?v=luGacxJMZI8). This was a strong contender, and is probably the correct way to do this, but it had been done and I wanted to pave my own road.

I didn't want to pave too much of my own road though: if I could leverage an existing system to my advantage, preferably some cheap garbage off of Craigslist, I could save myself a good deal of pain.

I stumbled upon a very foolish dream that many had of converting projectors into scanners. As they come prepackaged with all the infrastructure to pipe film around, project its image, and spool it back up onto a reel - it seemed like everything was done for me except the small implementation detail of how to capture the image. This foolish dream instantly became my foolish dream and I embarked on an equally foolish quest to accomplish it.


## A Plan

So I would scan the film automatically with existing film equipment. To do this, I imagined four core systems:

**A platform** that came prepackaged with everything I need. Probably a cheap projector or camera system that I didn’t need to mess with very much.

**A means of film locomotion**, where the word ‘locomotion’ is important because it sounds very sophisticated.

**A digital imaging system** somehow capable of programmatic control and of high enough quality to make this effort worthwhile.

**Some post-processing software** to clean up and compile the images into a video, though emphasis should be placed on preserving the source images to keep the door open for better processing later.

This turned out to be bit more of a journey than I had anticipated.

## Implementation

### Platform
For my platform I sourced a projector: a toy Excel P-16 produced in the Chicago area sometime in the early ‘40s. 

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-1.jpg#float-left)

I think it was probaly intended to project basic animations frame by frame at less-than breakneck speeds. This was in fact perfect because I was trying to capture films frame by frame at less-than breakneck speeds.

For this reason it stuck out to me. It was driven by a single belt-driven pulley on the end of a simple DC motor, and if I could replace the motor with an electronically controlled one of my own, basically all the work would be done for me.

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-11.jpg#float-left)

I bought it off of eBay for about $15 from a seller who hopefully will not be upset that I defiled his/her antique. I immediately gutted the interior of the lightbox and replaced it with a friendly lamp assembly and LED bulb from IKEA.

To accommodate larger reels, I would later build a sort of ‘expansion module’ out of plywood that the projector body and spools would sit on. I added some cozy bubble wrap and packing foam to cut down on the ridiculous rattling the motors would cause. 

### Locomotion
Given the nice and easy platform, the answer to the locomotion was obvious: stepper motors. 

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-2.gif#center-img)

The Excel P-16 moves film up through the projector and past the film plane with a forked metal prong, which I’ll refer to as the film fork because it sounds fun. The fork moves in a circuit where it grabs the film by its sprocket holes and tugs it up and through the film plane by a single frame.

My thought was that with a stepper motor, it would be trivially easy to time this circuit and move the film exactly one frame at a time.

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-3.gif#center-img)

The fork is attached to gearing on the back of the projector, which is spun by belt-driven pulley on the end of a DC motor that looks like it belongs in the engine bay of a Sherman tank. 

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-4.png#float-left)

To replace this dieselpunk nightmare, I utilized a pair of calipers and a dubiously student-licensed version of [Fusion 360](https://www.autodesk.com/products/fusion-360/overview) to 3D print a replica pulley and press onto the end of a stepper motor.

Adafruit sells perfectly sized [12v NEMA 17 stepper motors](https://www.adafruit.com/product/324), [custom fabricated HATs](https://www.adafruit.com/product/324) to wire them into Raspberry PIs, and provides an awesome (if somewhat undocumented) [Python library](https://github.com/adafruit/Adafruit-Motor-HAT-Python-Library) to interface. I drilled a few holes into the frame of the projector and screwed the motor in the place of the old monstrosity.

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-5.jpg#float-left)

I wrote a [Python script](https://github.com/matt-goldeck/barrington) to drive the motor. It's a complex algorithm upon which I’ve brought to bear the might of my education and experience. It goes something like this: `Move x steps, take picture, repeat`

The original idea was to measure the gearing and calculate a correct number of steps x for the motor to take as to move exactly one frame of film through the projector plane. Unfortunately I quickly found out that with this setup it's practically impossible. Not only does the metal belt slip, and not only does the film fork sometimes get stuck, but as the film shifts from feed spool to take-up spool, the energy required to spin the latter becomes ever greater and the whole system goes nuts.

This was very clearly a non-deterministic game, so instead of predicting and planning I would need to observe and correct. 

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-8.gif#center-img)

[IR Breakbeam Sensors](https://www.adafruit.com/product/2167) return true if the receiver can detect the light from the emitter and false otherwise. By hot-gluing the emitter behind the revolving film fork such that its light is totally blocked just before the arm begins to move the film through the imaging plane, the scanner can always know where the arm is and when it has successfully moved an entire frame. 

The logic then looked like this: `Move fork until in position, take picture, move arm until out of position, repeat`

With the new sensors, I didn’t need to delicately tip-toe the film through the projector either - I could give the motor [unlimited power](https://www.youtube.com/watch?v=Sg14jNbBb-8) and let it plow through reels in 1-2 seconds per frame. This cut the average time per frame in half, which doesn’t sound like a lot, but on a scale of tens of thousands of frames it's a godsend.

**Unnecessary Babysitting**

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-9.jpg#float-left)
Even with the sensors, the system was still finicky and had to be watched at all times, which sorta defeats the purpose of automating the process. 

The first major solution was implementing some failsafe logic that made sure the motor didn’t run too long without completing a circuit or a response from the camera server. 

I also implemented some code to specify a number of frames to scan before shutting off, so that if something horrible did happen, at least it wouldn’t happen for too long. This made me much more confident to let it run over night, which I consistently do now without problem.

This didn't solve the root of the problem: the film is *damaged*. Some reels are taped together with Scotch tape, others are bent or corroded, and a lot of the sprocketing on even the best reels is munged up and useless. 

Thusly, the film fork sometimes fails to grab a sprocket hole and begin to bunch up. When this happens, the projector sprocketing will eventually pull the tension from the film and fix it, but will result in the film subtly coming out of alignment such that the fork will never quite be able to sync with the sprocketing again. This was often so subtle that it often doesn't become apparent until after I had scanned and compiled the film, wasting days of work and causing me great misery.

To automatically correct this, I implemented some further logic that moves the fork in a ‘rebasing’ circuit on completion of every film-second (24 frames) of footage. 

This involves moving the fork backwards towards the beginning of the circuit so that it’ll fall into one of the misaligned holes and back into alignment, ensuring that no matter what the scanner encounters, there should never be more than a second of finished footage out of alignment. If you look carefully you can sometimes see this in the scans, which still somewhat pisses me off, but it's something I can live with.

### Imaging
This was honestly very tricky. Film is very small, and a projector has a lot of stuff that gets in the way.

Others going down this path had used DSLRs and electronically fired them via a remote shutter. This made for a great image, but I think has a dramatically limited lifespan. At 24 frames per second, a minute of film is 1,440 unique pictures. The average low-end DSLR apparently has a shutter-life of about 100,000 [actuations](https://improvephotography.com/935/how-long-will-my-shutter-last/), which would have given me about 70 minutes of film scanned per DSLR. Given the fact that I did not possess a DSLR nor the capital or inclination to acquire multiple DSLRs and destroy them, this was problematic. 

With all the other pieces to the project en-route, I went on somewhat of a manic bender looking for options with less expensive moving parts. I considered webcams but they suck, industrial vision cameras but they're expensive and hard to interface with, and I also considered buying a mirrorless camera but they're just way too big and wouldn't fit into my scanning rig without lots of cutting.

Eventually, on the train one night, my girlfriend recommended I should just use a cellphone. Any phone worth its salt in the past five years has a great camera, there are definitely lots of apps to control them, and if I didn’t already have a drawer full of damaged old ones, they’re very cheap to buy used.

![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-6.jpg#float-left)

So that’s what I did. I dug out my old Galaxy S7 and installed the [IP Webcam](https://play.google.com/store/apps/details?id=com.pas.webcam&hl=en_US) app. It hosts a server on the local network and can be accessed via a web interface, but that interface ultimately just wraps an API which I interacted with by POSTing via Python’s Requests library. To round it off, I bought a cheap [flexible tripod](https://www.amazon.com/gp/product/B06Y2VP3C7/ref=ppx_yo_dt_b_asin_title_o04_s00) and moved on with my life.

There would eventually be a few minor issues with this approach. The tripod gets always ended up getting bumped or jiggled around by the motor, so the resulting images bounce around like a [DVD screensaver](https://www.youtube.com/watch?v=Kxms-OtUXS0). This is now fixed in post-production by smart cropping each image in ImageMagick before compiling the video.

Also: at random intervals, requests to the server will timeout and the server’s settings will reset. I circumvented this by writing some code to account for timeouts and re-specify the camera’s image settings, but it's really annoying.

### Post Processing

I needed software I could run programmatically on massive batches of tens of thousands of images. I decided on using [FFmpeg](https://www.ffmpeg.org/) to compile and edit video and [ImageMagick](https://imagemagick.org/index.php) to crop the images. Both use a simple CLI which made it easy to entirely automate the process. Also, both are free, which is nice.

I’m not going to lie to you and say I fully understand them. The incantations required to use these suites are incredibly powerful, but esoteric and Tolkein-esque, spoken of only by wizened VFX artists in dusty and long abandoned corners of the internet. If you must see for yourself, gaze into the late ‘90s radness that is [ImageMagick’s documentation](http://www.imagemagick.org/Usage/crop/) page for the crop operator.

I tinkered with commands until I figured out what I needed to do and compiled them into a bash script. I could then simply drop the raw scanned images and script into a folder, run it, and in 30-60 minutes have a complete and finished video.

It amounts to this:

`mogrify -trim -background black -fuzz 25% -define trim:percent-background=20% *.jpg` 

This automagically detects the aforementioned black (within a 25% margin of error) border around each image and removes it.

`ffmpeg -r 24 -start_number 1 -i frame-%d.jpg -vf ""pad=ceil(iw/2)*2:ceil(ih/2)*2,transpose=2,transpose=2,hflip"" test.mp4` 

This compiles the images matching the pattern `frame-%d.jpg` in sequential order at 24fps, applying a filter that pads out each image to a standard size, rotates it 180 degrees, and mirrors it. 

The final product isn't the greatest thing in the world, so  I’m keeping all the original raw-scans to always have the option to reprocess with different settings or software entirely. It’s cool to at least be able to save whats on the film, though.


### Results
![](https://pineraider-static.s3.amazonaws.com/media/blog_images/5/5-12.gif#center-img)

The resulting machine is... very interesting to say the least. I don't think I'm winning any design awards here, but it's managed to scan quite a bit without any fires or destruction of historical artifacts.

Some highlights: 

<div class="videowrapper">
    <iframe width="706" height="524" src="https://www.youtube.com/embed/WKuQr09TkT4?list=PLuS_-YgXGfUfVWk-5kj0gmPYiW29hIcaS" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

<div class="videowrapper">
    <iframe width="709" height="524" src="https://www.youtube.com/embed/aVtc7DoQ44U?list=PLuS_-YgXGfUfVWk-5kj0gmPYiW29hIcaS" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
 
<div class="videowrapper">
    <iframe width="915" height="573" src="https://www.youtube.com/embed/6AB0p_5j2i8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

I even managed to scan a few 400-foot reels of film. At 40 frames per foot, and somewhere between 2-3 seconds per frame, it takes on average about 11 hours to scan a single reel -- so I'm never particularly jazzed to scan these. They often have the best content though.
<div class="videowrapper">
    <iframe width="690" height="524" src="https://www.youtube.com/embed/S8SfZ--DI88?list=PLuS_-YgXGfUfVWk-5kj0gmPYiW29hIcaS" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## Closing Remarks

I'm not massively satisfied with the actual compiled video. It's pretty jumpy, washed out, and generally makes it obvious that I don't know what I'm doing - but I'm chiefly concerned with just preserving the images at this point. Maybe once thats done, a future project will be doing something cool with them like [upscaling to 4K with AI](https://www.engadget.com/2020-02-04-how-ai-helped-upscale-an-antique-1896-film-to-4k.html) or just actually making watchable video.

I have to say that from an engineering standpoint this was stupidly frustrating. There were just so, so many problems - some of which continue to plague me. The projector was a very really whimsical idea at first, but I've spent far too many late nights hunching over the franken-scanner abomination begging it to just please scan the film properly this time. It's not a great place to be mentally or physically.

But I also have to say that now it is massively satisfying to have sketched up this crazy plan and actually delivered on it. I’m not sure I really expected this thing to work, and now that it actually does and is chugging along tirelessly preserving my family history, I can be a little proud of myself.

And that’s really what this was all for: as far as I know these films aren’t duplicates - they’re very old, very damaged, and very original. They may hold the last copies of their stories. My hope is that, however silly this project was, that it'll save at least a fraction of that history from scattering away into the sands of time. It means a lot to me.

Cheers.
