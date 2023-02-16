---
layout: post
image: /assets/images/posts/2023-02-importance-in-line.jpg
title: Your app is the least important
tags: app ux
---

Developing software takes time. Deadlines pass by, priorities shift and there may even come a time when the whole world revolves around that single project. It may be hard to imagine that a user base does not share the same deep feelings about a piece of software as its developer, but pretending otherwise can lead to fatal flaws. This blog post aims to prevent such bad user experiences by highlighting this potential gap between developer and user, using a scenario from the author's daily life as an example.

### An unexpected party

I use an Android app for tracking medical readings. These readings are important for me, so I want to be informed about unexpectedly high or low values. This medical tracking app does so by generating a local notification. This notification causes my phone to ring and vibrate. The ringing is controlled by the media channel, which is the same for music- and video players such as Spotify or YouTube. This means that adjusting the volume of one will affect the other and vice versa. The media channel is internally labelled [STREAM_MUSIC](https://developer.android.com/reference/android/media/AudioManager#STREAM_MUSIC), "Used to identify the volume of audio streams for music playback".

I tried to adapt to the medical tracking app's behavior by manually muting the device when silence was demanded. Of course I failed to remember that repeatedly, which resulted in my phone acting up in inappropriate situations. Finally, I decided to change my sounds settings and mute the media channel completely until a better solution appears. To add insult to injury, the medical tracking app comments this muting with a reappearing notification and multiple in-app messages advising to unmute. Working around this by selecting "None" as notification sound counts as muting nonetheless.

![](/assets/images/posts/2023-02-importance-notification.jpg)
*My sound settings (left), that reappearing notification (middle) and in-app messages on every app restart (right)*

This situation, as inconvenient as it is, does not really interfere with my daily routine and therefore would not be a deal-breaker on its own. That part begins with a second app I use to access a baby monitor for our cat at home. This webcam app is barely configurable and free of updates since 2018 which is reflected by the 2.6 star rating on Google Play. It also automatically sets the volume of the media channel to the maximum every time a livestream is opened. You may already sense where this blog post is heading.

### A series of unfortunate events

The other day my partner and I visited friends and stayed overnight. During that evening my medical readings went wild, as expected from a mixture of delicious food and sweetened cocktails, and so did my phone. After medically derailing, one notification after another roared from my pockets alerting not only me but everyone in the room. *"Just a reminder to check our cat!"*, was my spontaneous reaction to counter the astonished looks, but after the fourth and fifth time this excuse no longer made sense even in an buzzed state. Every time I pulled out my phone, I wondered why it was playing at full blast when I had it on mute a couple of minutes earlier.

I have forgotten about that webcam app.

Between the wrestling rounds with my phone, my partner wanted to check out our cat, and with the hours passing, those requests became repeated and their intervals shorter. As the responsible partner I am trying to be, I handed over my phone as soon as hers was out of battery. After opening the livestream, looking for the cat and sending a few meow meow's through the webcam's speakers, she returned my phone and the process started all over again.

It took some time until it clicked and I realized the two apps were affecting each other. This series of unfortunate events should then become the straw that broke the camel's back.

### A fine day to exit

Remember that better solution mentioned before? This solution is another medical tracking app which allows way more customization and uses the [correct audio channel](https://developer.android.com/reference/android/media/AudioManager#STREAM_NOTIFICATION). Its setup was time consuming and challenging, since several hoops were necessary to receive data from a closed-source medical device, but in the end I was finally able to uninstall that annoying official app. This is by no means a solution for everyone and every use case, nor should it be necessary in the first place, which brings me back to my original point.

Most users are not that patient and most use cases are not that critical. Why spend time on something that should be easy to use? Many users will simply uninstall and never look back, which is the worst thing that can happen to the software in which you have invested so much time and energy.

Your app is not the only one on your users' phones, so do not expect them to treat yours any differently. Resist the urge to force a certain type of behaviour on your entire user base. Instead, take the path of least resistance and integrate seamlessly into an unfamiliar environment, or provide customization options to adapt to your users' needs. This approach boils down to a single mindset:

### Your app is the least important.