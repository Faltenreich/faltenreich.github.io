---
layout: post
header: /assets/images/posts/2023-01-standing-in-line.jpg
title: Your app is the least important
categories: mobile
tags: app
---

Libre 3 with its media notifications and Webcam app with its control over the media volume as prime examples for annoying apps that think they are the most important

I use a app that is tracking medical readings. These readings are important, so I need to be informed about unexpectedly high or low values. The app does so by generating a local notification. This notification uses vibration and sound to draw attention and does so by using the media channel ([STREAM_MUSIC](https://developer.android.com/reference/android/media/AudioManager#STREAM_MUSIC)). This means if I want to mute music and video in occasions in which disturbances should be avoided, e.g. in a meeting, theatre or in school, then all notifications of the medical tracking app become muted as well. The app then responds with an in-app message and another notification advising to unmute.

I tried to adapt to the app's behavior, but of course failed to manually mute a few times, which resulted in my phone acting up in inappropriate situations. Finally, I decided to mute the media channel completely until a better solution appears.

I use another app to access a livestream from a webcam I have setup at home as baby monitor for our cat. This app is barely configurable and free of updates since 2018 which is reflected by the 2.6 star rating on Google Play. It also automatically sets the volume of the media channel to the maximum every time a livestream is opened. You may already sense where this blogpost is heading to.

### A series of unfortunate events

My partner and I visited friends and stayed over the night. During that evening my medical readings went wild, as expected from the wild mixture of delicious food and equally unhealthy cocktails, and so did my phone. After medically derailing, one notification after another roared from my pockets and alerted everyone in the room including several people who have not been introduced yet to my condition. *"Just a reminder to check our cat!"*, was my spontaneous reaction to counter the astonished looks, but after the fourth and fifth time this excuse no longer made sense even in an buzzed state. Each time I pulled out my phone the question arose why it was blasting at full volume even though I had muted it minutes before.

I have forgotten about that webcam app.

Between the wrestling rounds with my phone, my partner wanted to check out our cat and with the hours, those requests were repeated and their intervals became shorter. As the responsible partner I am trying to be, I handed over my phone as hers was out of battery. After opening the livestream, looking for the cat and sending a few meow meow's through the webcam's speakers, she returned my phone and the process started all over again.

It took some time until it clicked to me and I realized the two apps were affecting each other. This series of unfortunate events should then become the straw that broke the camel's back.

### A fine day to exit

Remember that better solution mentioned before? This solution is another app which allows way more customization and uses the correct audio channel ([STREAM_NOTIFICATION](https://developer.android.com/reference/android/media/AudioManager#STREAM_NOTIFICATION)). Its setup cost plenty of time as it is not officially supported. In the end it took me a few hours and days to finally be able to use another app and to stop and uninstall the annoying official one. An additional benefit of the unofficial app is it being open source, so even a dead end could be resolved by forking the project or creating a pull request. I am aware that this is not a solution suited for everyone and every use case which leads me to my original point.

Your app is not the most important one but only one among many on most users' phones. Most users are not that patient and most use cases not that critical in order to invest a lot of time in something that should be easy to use. Most users will simply uninstall such an app.

Remember: Your app is the least important on your user's phone.