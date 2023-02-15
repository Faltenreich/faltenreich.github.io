---
layout: post
image: /assets/images/posts/2023-02-importance-in-line.jpg
title: Your app is the least important
tags: app ux
---

I use an app for tracking medical readings. These readings are of the highest interest to me, so I need to be informed about unexpectedly high or low values. This medical tracking app does so by generating a local notification. This notification causes my phone to ring and vibrate in order to draw my attention. Those notifications use the media channel which is meant "to identify the volume of audio streams for music playback", according to its [official documentation](https://developer.android.com/reference/android/media/AudioManager#STREAM_MUSIC). This means if I want to mute music and video in occasions in which disturbances should be avoided, e.g. in a meeting, theatre or in school, then all notifications of that app become muted as well. To add insult to injury, the app comments this with an in-app message and another notification advising to unmute.

I tried to adapt to the medical tracking app's behavior by manually muting the device when silence was demanded. Of course I failed to remember this from time to time, which resulted in my phone acting up in inappropriate situations. Finally, I decided to mute the media channel completely until a better solution appears.

I use another app to access a livestream from a webcam I have setup at home as baby monitor for our cat. This webcam app is barely configurable and free of updates since 2018 which is reflected by the 2.6 star rating on Google Play. It also automatically sets the volume of the media channel to the maximum every time a livestream is opened. You may already sense where this blog post is heading.

### A series of unfortunate events

The other day my partner and I visited friends and stayed overnight. During that evening my medical readings went wild, as expected from a mixture of delicious food and sweetened cocktails, and so did my phone. After medically derailing, one notification after another roared from my pockets and alerted everyone in the room including several people who have not been introduced yet to my condition. *"Just a reminder to check our cat!"*, was my spontaneous reaction to counter the astonished looks, but after the fourth and fifth time this excuse no longer made sense even in an buzzed state. Every time I pulled out my phone, I wondered why it was playing at full blast when I had it on mute a couple of minutes earlier.

I have forgotten about that webcam app.

Between the wrestling rounds with my phone, my partner wanted to check out our cat, and with the hours, those requests became repeated and their intervals shorter. As the responsible partner I am trying to be, I handed over my phone, as hers was out of battery. After opening the livestream, looking for the cat and sending a few meow meow's through the webcam's speakers, she returned my phone and the process started all over again.

It took some time until it clicked to me and I realized the two apps were affecting each other. This series of unfortunate events should then become the straw that broke the camel's back.

### A fine day to exit

Remember that better solution mentioned before? This solution is another app which allows way more customization and uses the [correct audio channel](https://developer.android.com/reference/android/media/AudioManager#STREAM_NOTIFICATION). Its setup was time consuming and challenging, since several hoops were necessary to receive data from a closed-source medical device, but in the end I was finally able to uninstall that annoying official app. An additional benefit of this solution is it being open source, so even a dead end could be resolved by forking the project or creating a pull request. Surely this is no solution for everyone and every use case, which leads me back to my original point.

Your app is not the most important one on your users' phones, but only one among many others, so resist the urge to force your users into a behavior. Either follow the path of least resistance and integrate seemlessly into an unknown environment, or provide options to support customization in order to adapt to your user's needings. Most users are not that patient and most use cases not that critical to spend time for something that should be easy to use. Most users will simply uninstall and never look back.

Your app is the least important.