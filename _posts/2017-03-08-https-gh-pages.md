---
layout: post
title: HTTPS for GitHub pages
date: 2017-03-08 20:00:00.000000000 +00:00
type: post
published: true
status: publish
categories:
- Other
tags: [dsp]
---
So I'm still getting used to DSP. First push has been made, I've discovered my _dev muscles_ rusted. Well, that's why I'm doing that in a first place, but in between I'm finishing setup of the blog :)
Thanks to [Michał](http://www.michalgellert.pl) (yet again :) Thank you!), this blog setup is being polished. This time let's look at HTTPS.

![Tweet by Michal](/assets/201703-https-tweet.png){:class="img-responsive"}

# What
First and foremost, why https on the blog? Because I can ;)
But seriously, security matters, but common sense comes first, always. There is no need for https for this blog, and I'm setting it up, only because it does not cost me a thing and it make life easier for people with extension like _HTTPS everywhere_ installed. 

So let's look first where you are and what is our context. This blog is currently hosted out of [github pages](https://pages.github.com). Idea is simple, you have a git repo where you push .md files, which Jekyll automatically picks up and turns into HTML which are later served to you. There is zero server side logic here and interaction with you, so unless you use same password in Disqus, then you're _safe_. It's hard for me to imagine different scenario. GHPages allow to attach custom domain, which requires simple config on register side. GH now also enables HTTPS for all pages, but it does not allow to provide custom certificates for custom domains, and here is where problem started.

# How
Okay, but you still want to have HTTPS. What to do?!

So I've registered domain in OVH, they do not allow to do stuff like putting ssl cert in the middle (or at least not for free), am I doomed?
Have no fear, [Cloud Flare](https://www.cloudflare.com) is here! Yes it bled, but more about it later :)
Cloud Flare is a CDN that can work as a reverse proxy and DNS. In other words, it sits between client computer and server, routing whole traffic between them, and distributing load to make it much faster. 
Basic plan is for free, and step by step guide is intuitive. You don't even need to buy a certificate, they do everything for you. After registration, just provide your website URL, and wait for magic to happen. After they are done checking on current DNS provider and register, you will get 2 addresses: DNS and NS, which then easily you copy paste (skill that every senior developer in age of stack overflow has mastered), to OVH panel.
OVH knows it may be insecure, so by default it's blocked, you need to confirm that you know what you do.
At the end it should look like on the screenshot. 
![OVH panel](/assets/201703-https-ovh.png){:class="img-responsive"}


# Drawback / Issues

Now about the drawbacks. Yes, you heard correct, CF is responsible for one of [bigger fc*ups](https://www.theregister.co.uk/2017/02/24/cloudbleed_buffer_overflow_bug_spaffs_personal_data/) in the internetz recently. By introducing 3rd party service, you compromise your security, **always**. No one knows what happens with your internetz when they go to CF. Most probably nothing, but you can't really tell, it's a matter of trust :)
Earlier I mentioned that CF takes care of everything. That is super easy, fast and convenient however totally unsecure. If you control encryption keys, you can also decrypt messages (duh). 
There is also interesting setting on CF side about SSL encryption. As it's a reverse proxy, it sits between server and client. What it allows to do, is to encrypt with different key both communication and change key on the go. It can also verify keys on the server or ignore certificate issues.

So that's it, go and be secure. Remember to always consider how secure you need to be and what risks you can take :)