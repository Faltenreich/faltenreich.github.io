---
layout: post
header: /assets/images/posts/2023-01-standing-in-line.jpg
title: Your app is the least important
categories: mobile
tags: app
---

Libre 3 with its media notifications and Webcam app with its control over the media volume as prime examples for annoying apps that think they are the most important

I use a app that is tracking medical readings. These readings are important, so I need to be informed about unexpectedly high or low values. The app does so by generating a local notification. This notification uses vibration and sound to draw attention and does so by using the audio channel called [STREAM_MUSIC](https://developer.android.com/reference/android/media/AudioManager#STREAM_MUSIC). This means if I want to mute music and video in occasions in which disturbances should be avoided, e.g. in a meeting, theatre or in school, then all notifications of the medical tracking app become muted as well. The app then responds with an in-app message and another notification advising to unmute.

I have but in the end ultimatedly decided to mute the corresponding audio channel completedly until a better solution appears.

I use another app that accesses a live stream I have setup at home to see if our cat is fine. This app is the : Barely configurable and free of updates for five years now. This app automatically sets the volume of the media channel to the maximum.

### A series of unfortunate events

My partner and I visited friends at New Year's Eve and stayed over the night. During this evening and as expected my medical readings went wild and so did my phone. After medically derailing, one notification after one roared from my pockets and alerted everyone in the room including several people who have not been introduced yet to my condition and therefore wondered what was going on with my phone. *"Just a reminder to check our cat!"* is an excuse that works well once but not so the third and fourth time. Everytime I pulled out my phone the question arose why it was blasting at full volume even though I had muted it minutes before. 

I have forgotten about that webcam app.

Between the wrestling rounds with my phone, my partner repeatedly wanted to check out our cat and as the hours went on these intervals became shorter. To do so she used my phone as the app has not been installed on her phone since the setup was somewhat of a hassle. After opening the live stream, looking for the cat, sending a few meow meow's over the internet, she handed my phone back to me and the process started all over again.

It took some time until it clicked to me and I realized the two apps affecting each other. The next day I decided to change something about this unfortunate mixture of apps that dominate my phone.

### A fine day to exit

Remember that potential better solution mentioned before? This solution is another app which allows way more customization and uses the correct audio channel [STREAM_NOTIFICATION](https://developer.android.com/reference/android/media/AudioManager#STREAM_NOTIFICATION). Its setup cost plenty of time as it is not officially supported. In the end it took me a few hours and days to finally be able to use another app and to stop and uninstall the annoying official one. An additional benefit of the unofficial app is it being open source, so even a dead end could be resolved by forking the project or creating a pull request. I am aware that this is not a solution suited for everyone and every use case which leads me to my original point.

Your app is not the most important one, so try not to pretend it is. Most users are not that patient and most use cases not that critical in order to hassle that much with something that should be easy to use. Most users will simply uninstall your app.

Remember: Your app is the least important.