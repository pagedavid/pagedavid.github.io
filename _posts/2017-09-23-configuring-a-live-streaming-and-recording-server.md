---
layout: post
title: Configuring a Live Streaming and Recording Server
---
This is going to be a long post because putting this system together was a process that moved at the rate of church bureaucracy. In the end, the final system is a computer running Gentoo, a PTZOptics camera, and a tablet with an interface to control the camera shots. All that needs to be done on the user end is turn on the camera, turn on the computer, click the browser bookmark icon on the tablet, and the service is recorded.

There were a number of very large funerals last summer at St. Thomas' church. More people were expected to attend than would fit in the church so overflow seating was planned shortly before the first took place. Given the amount of notice, companies were quoting at lease $6000 to handle simulcasting. This sounded ridiculous to me, and I told the parish administrator (Don) that I could do it with a $60 RasberryPi (a single chip minicomputer), an HD webcam, and a router.

Did I know exactly how I'd do it? No. But I knew it was something that could be done, and I had a nine days.

There's no wifi in the church so I'd put the router in the choir area. The shot would be stationary, but I'd set the Raspberry Pi and webcam up in the back of the church. I'd configure the RPi to broadcast the stream wirelessly, where I'd pick it up on a laptop I'd have by the soundsystem so I could pull in the audio via its headphone jack into the laptops mic jack. The laptop would combine the video and audio streams, and send them to youtube where it's free to stream.

In the overflow seating all I'd need is a laptop with internet access and a projector. 

I found two possible command line programs that could theoretically do what I needed. Gstreamer and ffmpeg. I also needed to run video4linux on the RPi to access the webcam video. I think I started out with Gstreamer, but then ended up using ffmpeg because I got it working faster. 

At the last minute though a local school volunteered the use of a camera they use to livestream sports games and a student who could operate it. My system was passed over, though I did successfully demonstrate it to Don and he was very pleased.

Livestreaming services had been something the church has discussed for years, and having seen it done once there was a new push to make it happen. Don liked the capabilities of the camera used for the funeral and thought we should get one of those. He also asked what else we would need if the church wanted a permanent installation. (My system involved taping down cat5 cable though the back of the church...)

I looked into cameras and picked a couple I figured would work in the $500-600. My main requirement was a network enabled pan-tilt-zoom capable camera. While I was looking though I fell in love with PTZOptics second generation cameras. They were specifically open-source, though cost $1700. Don likes me, and when I said that that camera would definitely be the best that's what he purchased.

The camera would be mounted underneath the balcony below the speakers. The speakers could be daisy-chained so I'd drop a cable down and into the camera. Since the camera does the work combining the video and audio, the computer that actually sends the feed to youtube wouldn't need amazing specifications; I could use the extra desktop in the communications office. That computer would simply forward the feed to youtube and run a webserver with the browser-based camera contoller. Then, from a tablet or even cell phone, someone could sit in the back of the service and click on pictures of preprogrammed camera angles to move the camera.

All I needed was an ethernet cable and power outlet in the back of the church, and I'd have the system together in a week. 

Fast forward about a 10 months because that's how long it takes to get backburner projects done at a church. 

When the ethernet cable and power were finally run to the back of the church and the camera mounted I set to work configuring the server.

I really like Gentoo, and that's the Linux distribution I put on the desktop. I like that I can slim down programs and only compile what I'll use. I like open-rc as the init system. I also like that nobody will ever turn it on and get a 45 minute updating screen. 

Though it wasn't necessary to put on a desktop I thought it would be a good idea to anyway. If someone ever wants to preprogram more angles on the camera it'd be nice if they had a desktop designated to do that. The only programs it would need to run would be a browser and VLC.

I went with XFCE as the desktop becuause it's lightweight. I didn't install a graphical login though because if the computer was just going to be recording/streaming there's no need to start any desktop processes and waste resources.

The camera controller webpage is served via Nginx. I took one of the demo controllers PTZOptics offers and stripped out all of the additional controls. The interface is simple: nine photos of the nine camera positions. Focus on any one of them and the camera moves and that picture is highlighted red.

I wrote a very simple init script and set that to run when the computer boots. All it does is call the "stwrecord" script which records the stream via ffmpeg and saves it to the user's desktop as a dated .mp4 file. The only bug I encountered was when the power button was used to shutdown the computer it would corrupt the .mp4 file. The fix was to add a pkill command to the shutdown process. 

The church preferred if streaming was an option so to stream the service someone does have to login and type "stwstream" though I might change that to "stream" or "livestream". That command runs separate from the recording so if the stream gets killed the recording doesn't stop. (Earlier versions of the command I used would tee the output to a file and to youtube. I encountered a problem though when I was livestreaming the nursery school graduation and youtube cut the feed for copyright infringment because they were playing a piano arrangement of Disney music as people took their seats.)

Currently, everything is in perfect working order, but until an aestheically pleasing box is made and put in the back of the church the camera must be controlled from the server room. (All the box needs to hold is a wifi access point, a network splitter, and a small power strip. People would flip if all those wires were just ducktaped to the wall.)