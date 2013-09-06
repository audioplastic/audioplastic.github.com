---
layout: post
title: "Accurately measuring round-trip audio latency on iOS"
date: 2013-09-06 12:02
comments: true
categories: 
published: true
---

Round-trip audio latency is the time taken for the sound to enter a real-time system, be processed by the hardware, and then be reproduced as a processed sound. This figure is particularly important for any application where a user interacts with sound in some way. For example, if you're using software to simulate a guitar amplifier, you do not want to hear a delay between when you strike the strings, and when you are able to hear the processed output. Too much delay will mess up your performance. I work with assistive listening software that processes environmental sounds for hearing impaired listeners. Just like in the musical example, any delay in such a system will detract from the user experience.


I was recently testing my [AUD-1 software](www.aud1.com) on a variety of devices that I had to hand, and noticed what I perceived to be a higher audio latency on the iPod Touch 5th generation. It sounded like there was a greater audio delay than on any other devices that I'd tested. A search on the web revealed relatively little on the subject, apart form a discussion thread on the [Loopy forums](http://forum.loopyapp.com/discussion/591/help-latency-with-loopy-ipod-touch/p1). Loopy is some pretty neat audio software, an the developer posted a simple method for testing the round-trip audio latency of your setup . . 

<iframe width="560" height="315" src="//www.youtube.com/embed/UNR_nWlw8Xs?rel=0" frameborder="0" allowfullscreen></iframe>

Inspired by this, I baked a latency testing utility right into [AUD-1](www.aud1.com). This allows the user to make accurate latency measurements without the need for an external computer. It also allows the user to test various hardware configurations. See the following video for a demo and some data . . 

<iframe width="420" height="315" src="//www.youtube.com/embed/C8StPwiHTCc" frameborder="0" allowfullscreen></iframe>

For the best results, you want the latency figure to be as low as possible. It needs to be around 10 ms or less not to be a nuisance. Unfortunately, in my tests, I could not get a latency figure of < 50 ms using the 5th generation iPod touch. Maybe this will be fixed in an iOS software update?