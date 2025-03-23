+++
title = "What Every Web Developer Should Know About HTTP by K. Scott Allen"
description = "A curated set of topics about HTTP and Computer Networking that is essential understanding for the everyday work of a software developer building for the web."
date = 2025-03-23T11:00:00+05:30
tags = ["networking", "internet", "http", "web", "cloud", "devops"]
+++

## Introduction

Much of the software today is being built on top of the internet. Business applications, office tools, AI chat, graphics programs, just to name a few.

Interacting with HTTP services, whether debugging network requests, inspecting and issuing them from API clients or browser scripts, is a daily ritual for software engineers.

Whenever needed, I fired up my API client (before Postman, now Bruno) or Chrome's Network DevTools, and pored over the data being passed between the server and the client, and got the job done.
Most of my understanding emerged from experiments and observations, and then intuition followed.

However, I felt a lack of grasp over fundamental HTTP concepts from time to time.
“If you are a farmer, and you cultivate food for a living, you must know the lay of the land.”

With that thought, I set out to look for good resources to better understand the internet and to make informed decisions.

The last time I did a course on computer networking was back in 2017 in the final semester of my BTech. I remember nothing.
But I did not want to open a fat academic book again and commit to months of deep studying arcane theory and practice, given my intention to be more productive at the moment.

Fortunately, I found a great book by K. Scott Allen titled “What Every Web Developer Should Know About HTTP”.
The author has curated the most essential topics about HTTP in under 100 pages of crisp and simple English.

I read the book recently with intent and attention, taking notes diligently. As a result, I consolidated a lot of scattered and empirical thoughts into structured mental models.

Resources, Messages, Connections, and Security — each topic has been explained beautifully and with the right level of practical detail.
He was also kind enough to publish the material in his blog, which one can read for free:
https://odetocode.com/Articles/741.aspx

If you supplement the above knowledge with “DNS name resolution” and “SSL/TLS”, you will have gained a significant amount of mastery for a lot of on-the-job duties.

And whenever I feel there is a need for more detail, the MDN documentation is an excellent and in-depth resource: https://developer.mozilla.org/en-US/docs/Web/HTTP

Now, when I issue an HTTP request in Bruno, I appreciate the complicated infrastructure that make the message exchange possible, much of which is hidden from the user.