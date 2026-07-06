---
title: "3 Months in Telegram, The analysis of 75,000 Persian Telegram channels"
date: 2021-05-14
author: "Mohsen Tahmasebi"
description: "A large-scale look at Persian Telegram channels, including copied content, APK distribution, phishing, malware, VAS ads, and betting campaigns."
tags:
  - telegram
  - security
  - research
draft: false
slug: "3-months-in-telegram-the-analysis-of-75000-persian-telegram-channels"
---

_This is the translation of my research on Persian channels on Telegram, 2 years ago. This is a quick translation so expect language errors._

_Note: In this post, I mention VAS services and VAS apps. In a time period, there were some services on Iranian mobile networks which charge the user periodically once they’ve registered. Some malwares secretly registered users in such services and the second generation tried to convince users with false claims to register. The first-gen was dead by the time of research so by VAS apps I mean the second generation._

I have been writing and developing Internet crawlers for a long time (computer programs that collect information and spread in an environment such as the web or telegram and monitor new places) and one of these crawlers was on the Telegram.

It started spreading from ten telegram channels and now observes more than 80,000 channels. In this post, I am going to give interesting information and statistics from the Telegram environment, which were obtained from the analysis of information during these three months (more than thirty million messages). I need to give some reminders before I start.

First, because the crawler is constantly expanding, so much information is miscalculated that some data, such as trending topics, is completely invalid. I did my best to prevent this error (as you do not see any information or charts about trends or changes) but there is a very low percentage of error in the statistics given.

Second, a lot of the information given in this post is presented without accurate statistics because the data about which statistics are given (such as VAS apps) are very coherent and consequently highly researchable, but for example in the field of malware for reasons such as Inconsistency and lack of facilities do not provide accurate statistics.

And finally, many topics, especially in the field of malware analysis, to avoid over-technicalization of the post, are not brought here, which again includes my above description.

## General information

During its nearly three-month run, the crawler discovers and examines more than 75,000 active channels (as of this data collection). During this period, more than thirty million telegram posts were analyzed and 76,373 apk files were extracted.

## Telegram = phishing + VAS + gambling + copy!

Ever wondered how an ad can reach thousands of channels at a given time? Widespread networks are the advertising networks in Telegram that cover a large number of channels. The issue even goes beyond the Widespread networks and reaches the Widespread networks alliance, where they can easily send an ad to millions.

Now these Widespread networks sometimes become a bad actors and a person with a lot of money buys a high percentage of advertisements from these networks and it no longer matters what the content of the advertisement is. You also trust an ad that has been viewed millions of times, right?

Analysis of the text and multimedia posts of the channels shows that a large amount of telegram content is copied from elsewhere, copies that are sometimes made in less than a minute, and in many cases their source is Twitter. Accordingly, there are patterns that some people seem to try to control public opinion or spread rumors by identifying these important data feeding points and feeding their content, which we will not discuss further …

## VAS, the king of Android apps in Telegram

Among these ads, a percentage is allocated to the Android installation files (APK).

{{< figure
  src="1.png"
  alt="Only about 7% of the monitored channels had an apk file."
  caption="Only about 7% of the monitored channels had an apk file."
>}}

One of the most important reasons for this is the widespread networks fear of malware and its consequences, but what about this small percentage?

According to the information collected, the first four channels in the field of sending apk are all dedicated to VAS advertisements!

In fact, a high percentage of advertised files are sent from these private channels.

{{< figure
  src="2.png"
  alt="About 45% of the monitored files are forwarded."
  caption="About 45% of the monitored files are forwarded."
>}}

With more analysis on this network, the distribution of value-added programs revealed interesting points.

Almost all of these channels are private, two of the mentioned channels published duplicate versions of their programs many times with different hashes (probably for statistical reasons and to prevent file interception) and their names are also interesting in their own way!

{{< figure
  src="3.png"
  alt="Different channels, with different programs, with the same names! (Top five value-added channels, four of which are the first four in the field of apk ads)"
  caption="Different channels, with different programs, with the same names! (Top five value-added channels, four of which are the first four in the field of apk ads)"
>}}

Each of these channels broadcast a separate list of VAS programs, some of which were advertised within a short time and then eliminated. Even some of the observed versions first register the user in the VAS service and then download the main program!

{{< figure
  src="4.png"
  alt="Programs advertised by these channels (all VAS), some of them using the name of popular apps"
  caption="Programs advertised by these channels (all VAS), some of them using the name of popular apps"
>}}

VAS apps are undoubtedly the king of Android apps in Telegram…

But the VAS in Telegram is not limited to apps, but the main form of their activity (like Instagram) seems to be more based on websites and ads such as free internet, which of course is not comparable to the volume of their ads on Instagram.

## Phishing and malware

{{< figure
  src="5.png"
  alt="Graph of distribution of apk files in Telegram (nodes indicate channels)"
  caption="Graph of distribution of apk files in Telegram (nodes indicate channels)"
>}}

Unlike VAS apps, malware and phishing applications were as decentralized as possible and were sporadically advertised by the networks or single channels. Phishing apps are generally made by Iranian App builder services which criminals can steal from the victim's bank account without a need of a single programming line, easily and simply by designing four menus and putting the fake Bank portals. Next, most of the other malwares are generally made by basic4android.

Also, the most common type of malware in the Telegram environment in Iran is HIDDAD or Hidden app malware, which, by installing on the victim’s phone and gaining access, hides from the user and shows annoying advertisements.

{{< figure
  src="6.png"
  alt="This app exploited people’s religious beliefs to infect them and display annoying ads, forced telegram adds, and forced app installations. Over several months, it was monitored about 1,300 times on various channels."
  caption="This app exploited people’s religious beliefs to infect them and display annoying ads, forced telegram adds, and forced app installations. Over several months, it was monitored about 1,300 times on various channels."
>}}

{{< figure
  src="7.png"
  alt="Apps that are easily designed in minutes and have taken many victims."
  caption="Apps that are easily designed in minutes and have taken many victims."
>}}

In the meantime, more serious malwares are more limited, usually written by the Russians and the Chinese; Interestingly, many of these apps are published by hacking channels for fun and … so they are easily available to anyone.

{{< figure
  src="8.png"
  alt="(Left) ransomware called Telegram post views. In the Android operating system, ransomware can be easily removed and bypassed. (Right) This malware factory reset the victim’s phone after gaining access."
  caption="(Left) ransomware called Telegram post views. In the Android operating system, ransomware can be easily removed and bypassed. (Right) This malware factory reset the victim’s phone after gaining access."
>}}

{{< figure
  src="9.png"
  alt="Some of these malwares are ready to be used and you only need to change a few parameters in the apk file to get your personal malware ready! (Image of one of the spreading malware)"
  caption="Some of these malwares are ready to be used and you only need to change a few parameters in the apk file to get your personal malware ready! (Image of one of the spreading malware)"
>}}

But the market of fake telegram clients was also hot, with more than 800 copies of fake telegrams with features such as forced group adds and spy being discovered, which generally are down…

Of course, there were a few files that were intended to test a limited way to secretly join VAS services. Most of these malware (advertisements and forced installations) use Iranian push notification services to send commands to the victims’ phones (using them as C&C), and the Iranian services, after all these warnings, apparently have not yet managed to organize their systems.

## Gambling and betting

It is very unlikely that being active in Telegram and not see the tempting betting ads. According to research on betting advertising campaigns, the name of the betting site is changed every once in a while, but with the same scope and pattern of activity. In fact, it seems that one person/team are behind the advertising campaign of many of these sites or even all of them.

The advertising pattern of gambling sites looks much more regular than other advertisements and is more calculated in terms of timing and productivity.

## Conclusion

By comparing the above, the volume of VAS advertisements (app and non-app) is obviously more than the other items mentioned, after that, there are ads and activities of betting sites that act more calculated, and finally, malware and phishing that are supposed to be more scattered and secretive.

According to the manual analysis performed on several published malware, it is difficult to say that malware or an advanced attack is present on the Telegram platform, and the general widespread infection through Telegram is related to simple advertising malwares.

Now, years later, Telegram has given the market to Instagram for malicious activities for reasons such as Telegram being blocked in Iran (despite Telegram’s very high potential in this field).

More importantly, after the Golden Telegram servers were shut down, the number of new and active fake telegrams dropped dramatically.

In the end, this project has progressed throughout its operation with very few resources and my own personal cost, but with the raw information obtained, they have the potential to extract much more interesting data, which unfortunately I do not have the energy and time to do. Due to the large size of the files, it was not even possible to analyze them automatically.

Raw and technical information is not published in this post, however, I will provide the information to a few people to do their own research. If you think there is something wrong with this report, please let me know on Twitter so that it can be reviewed and answered with sufficient documentation.

Thanks for taking the time :)
