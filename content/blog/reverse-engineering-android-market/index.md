---
title: "Reverse Engineering And Scanning The Largest Android Market In Iran"
date: 2023-03-22
author: "Mohsen Tahmasebi"
description: "How I reverse-engineered an Android market and scanned 100,000 of its apps."
tags:
  - android
  - reverse-engineering
  - security
draft: false
slug: "reverse-engineering-and-scanning-the-largest-android-market-in-iran"
---

**_IMPORTANT NOTE: This post is translated from my Persian blog, originally posted on September 2021. This research was performed in 2019 and 2020. I believe its results are outdated, so I skipped translating the scan results. Most of this post is automatically translated (due to my laziness), so please forgive its strange form and errors._**

CafeBazaar reverse engineering project was one of my most interesting and complex projects. Its results were discussed in detail at [here](https://moh53n.net/blog-fa/cafebazaar-android-marketplace-security-study) and [here](https://moh53n.net/blog-fa/cafebazaar-security-followup-quick-review-after-months) and its scripts and information were published at [https://github.com/moh53n/bazz](https://github.com/moh53n/bazz) but in this post, I want to go into the technical process of reverse engineering the app and API of CafeBazaar, so we will be a little more technical :)

CafeBazaar is the largest Android app market in Iran. With millions of users and hundreds of thousands of apps, it’s wildly accepted as the Google Play alternative in Iran.

At the beginning of July 2018, after reading a funny Japanese article about the security of the CafeBazaar platform, I decided to start a project to scan the entire CafeBazaar…

## Decompiling the CafeBazaar

Android apps, and in fact Java apps, can be decompiled to a great extent due to being compiled to Java ByteCode, which means that the output of the Java compiler can be converted into the original Java source (to some degree). This makes almost any Android application decompilable to its original source. Well, that’s not good, Not only in terms of security but also in terms of the possibility that your app can be published by someone else with a slight change. As a result, obfuscation methods and tools can be used. These tools and methods (not limited to Java language) mess up the source code and the compiler’s output, causing the source code-checking tools and the researcher to have problems after decompilation (or even during decompilation). How? Like this:

{{< figure
  src="1.png"
  alt="The section related to creating a request"
  caption="The section related to creating a request"
>}}

As you can see, the names of many things (functions, classes…) have changed to a, b, and… and so in a situation where you are dealing with tens (or hundreds) of different classes and methods that are related to each other, you and your tools easily get lost and get confused.

When I first decompiled CafeBazaar version 7.31.1 with jadx and saw the output, I was almost sure it would be complicated…

## Facing chaos and ambiguity

My ultimate goal was to understand how client requests are made to install a program (give a download link) and how the server’s response is handled, so the first thing I did after getting lost in the source code and getting frustrated with static analysis (reading and checking the code) was to go to the dynamic analysis (checking the program during execution) to check the connection between the client and the server of CafeBazaar. In the simple case that I expected, after bypassing the standard HTTPS connection (which was actually done by installing a self-made certificate on Android VM to provide the possibility to read the SSL/TLS connection in an unencrypted form), I should have faced something like this :

```json
{"id":1,"method":"getAppDownloadInfo","params":["com.example.android",27]}
```

A simple JSON request that expressed its request very clearly and received an answer from the server, so maybe I didn’t even need to check the source code of the program, but the request I encountered was not like this…

{{< figure
  src="2.png"
  alt="well…"
  caption="well…"
>}}

This was obviously not a simple SSL pinning, and I had almost no idea what was going on. After some thinking, these things became clear:

1. By looking at iv, you can understand that p and iv are related to the AES encryption algorithm and p is the key, but there are two p here!

2. Fortunately, the method parameter was clear and specified the API method we were looking for.

3. After ten or twenty times of testing this type of request (and other types of requests), the id was always 1, so it didn’t really matter.

4. With a little guess, it was understood that enc_resp stands for encrypted response, which means requesting an encrypted response from the server.

5. With the same guess, non_enc_params were the unencrypted parameters of our request, which were generally metadata related to the user’s device.

6. The hash parameter should be the hash of the request to ensure that the request is not tampered with.

7. The params parameter was empty in the request I wanted, so it didn’t matter.

8. And finally, the most important parameter, packed, which was obviously encrypted and probably contains the details of the request…

After many attempts to make an AES request with a sample iv and p, no success. There was no choice but to return to the static analysis of that hell.

## Overcoming chaos and ambiguity

All clues were in the same first photo:

{{< figure
  src="3.png"
>}}

After a few minutes of looking for the part of the code responsible for making the JSON request, I reached the above picture. So I assumed following the values in the JSON would have made it clear how they were made, but that was just easy in words…

{{< figure
  src="4.png"
  alt="In search of encryption"
  caption="In search of encryption"
>}}

And finally, getting lost:

{{< figure
  src="5.png"
  alt="WHAT? (wrong path)"
  caption="WHAT? (wrong path)"
>}}

After hours of sleeplessness, it was clear that I was facing an AES/CBC/PKCS5Padding in the packed section, but there was also an RSA/None/OAEPPADDING, which I had not seen before, and now it was clear that I was dealing with several encryption steps. If just one of the parameters wasn’t appropriately encrypted and in the expected order, the server didn’t answer correctly, and I still didn’t know what the packed part looked like before being encrypted…

## Working with Frida

After twenty hours of non-stop work, I concluded that even though I know what types of encryption are used, I can’t figure out the order and final payloads just by reading the code. At least for me, this was impossible, so I changed my work method a little.

Frida is a dynamic analysis tool that gives great power to the analyzer to check and manipulate the app at run time. By running Frida’s server on Android VM and connecting it to my system, I could run scripts in the form of JavaScript (which I really hate), which allowed me to have a deeper look at the program’s execution process. One of the most beneficial things was being able to hook some tools and libraries used by CafeBazaar. With this, I could read the input and output of some methods before executing them and sometimes changing them. Until that moment, I had never worked seriously with Frida because I had not been challenged to this extent, so I spent a few hours (many hours) learning and writing a Frida script.

{{< figure
  src="6.png"
  alt="Frida script"
  caption="Frida script"
>}}

This way, I tried to read the values created inside the client before being encrypted. The results were promising:

{{< figure
  src="7.png"
  alt="The output of Frida script"
  caption="The output of Frida script"
>}}

First of all, this helped me to understand the sequence of the creation and encryption of the request components, but I had to understand what the final requests look like before being encrypted and why the created key and iv are different from the keys in the final request. After dozens of various tests and hooking all kinds of CafeBazaar internals and encryption calls, the first significant success was achieved, the raw request and the unencrypted value of the packed parameter:

{{< figure
  src="8.png"
  alt="The original value of the packed parameter for the app details request"
  caption="The original value of the packed parameter for the app details request"
>}}

In the same way, I also got the original value of the packed parameter to get the app download link (getAppDownloadInfo), but the SHA1 hash of this value did not match the hash in the request. After some tests, it became clear that it does not hash the main payload but creates a special string with a hardcoded UUID and then hashes it!:

```json
{"7cc78271-e338–4edc-849c-b105c5d51ba5":["getAppDownloadInfo","com.example.android",27]}
```

While playing with Frida and demystifying the program’s normal process of using its encryption tools, I finally noticed the use of RSA. It was used on iv and p. So, it was the reason for the difference of keys in the final request. I also got the used RSA public key by hooking the app (which later turned out to be serialized in the app’s resources, and I was blind to it).

Finally, one problem remained, why were there two p and actually two AES keys? The checks with Frida showed that one of the keys does not play a role in encryption at all (p2), so I made an unused AES key and sent it as p2 along with the crafted final request, and after sixty or seventy hours, the first correct response to my crafted request was received! In the subsequent investigations, it was found that p2 was probably the key that the server was expected to encrypt its response and send it to the client…

{{< figure
  src="9.png"
  alt="The complete and correct response of the server to the created request"
  caption="The complete and correct response of the server to the created request"
>}}

In conclusion, CafeBazaar used an extraordinary self-made multi-stage encryption system like this:

1. Create a JSON object and insert the desired method and the required parameters (here is the package name of the requested app)
2. Build an iv and two AES keys.
3. Encrypt the output of step 1 with iv and the first key by AES/CBC/PKCS5Padding algorithm.
4. Encrypt the iv and two AES keys using a hard-coded RSA public key with the RSA/OAEPPADDING algorithm.
5. Hashing the parameter and method of step 1 in a specific format along with a hardcoded UUID with SHA1 algorithm
6. Creating a new JSON object and placing the method along with the output of step 3 in the packed parameter, placing the three outputs of step 4 including iv and two encrypted keys, the hash of step 5, and a little (a little more than a little) unencrypted metadata.

{{< figure
  src="10.png"
  alt="The basic script for making a request to download an app"
  caption="The basic script for making a request to download an app"
>}}

With the determination of the formula for making a valid request, I made this process automatic in a script. The first version of this script is in the GitHub link. So all the available apps of CafeBazaar (100,000 apps!) were sent to VirusTotal for scanning (which, due to the limitation of VirusTotal API and my empty pocket, took months).

## Final Word

This was one of my most beautiful and heavy projects at that time, which significantly increased my knowledge. The research results were presented at the Shabgard conference, which was apparently not well received by CafeBazaar for some reasons…

I hope this post was helpful to you; if you have any questions, ask in the comments or on my Twitter. Thank you for reading ;)

## Notes

1. This article addressed the old CafeBazaar API, which, although it still works, a newer and apparently much simpler API is being used.
2. Some of the images and samples in this post are related to the time of research, and some are related to the time of writing this post.
3. Obfuscation, done by tools like proguard, is not necessarily used to make reverse engineering more difficult, but it is also used for optimization.
4. No attempt was made to “hack” CafeBazaar or disrupt its services. As is clear, this article and research only intended to discover the communication formula between the client and the server of CafeBazaar.
5. Ironically, during my research, I sent tens of thousands of files from CafeBazaar servers to VirusTotal, but CafeBazaar API did not realize that these requests were crafted (based on metadata), and there was no interruption in the scanning process!

## Good to Read:

1. Frida: [https://frida.re/docs/home/](https://frida.re/docs/home/)
2. AES: [https://en.wikipedia.org/wiki/Advanced_Encryption_Standard](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
3. RSA: [https://en.wikipedia.org/wiki/RSA_(cryptosystem)](https://en.wikipedia.org/wiki/RSA_\(cryptosystem\))
4. jadx: [https://github.com/skylot/jadx](https://github.com/skylot/jadx)
5. Android obfuscation: [https://developer.android.com/studio/build/shrink-code](https://developer.android.com/studio/build/shrink-code)
6. Java Bytecode: [https://en.wikipedia.org/wiki/Java_bytecode](https://en.wikipedia.org/wiki/Java_bytecode)
