---
layout: post
title: Event sourcing on Android
date: 2017-04-04 20:00:00.000000000 +00:00
type: post
published: true
status: publish
categories:
- Other
tags: [dsp]
---

Today let's talk about storing data and [event sourcing](https://martinfowler.com/eaaDev/EventSourcing.html). When we were starting work on [Friendly Plans](https://github.com/autyzm-pg/friendly-plans), during first meeting, we had a long discussion about how to handle storage.

![https://commons.wikimedia.org/wiki/File:USPS_Post_office_boxes_1.jpg](https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/USPS_Post_office_boxes_1.jpg/640px-USPS_Post_office_boxes_1.jpg)
<i class="fa-creative-commons fa"></i> *[CC SA by Howcheng](https://commons.wikimedia.org/wiki/File:USPS_Post_office_boxes_1.jpg)*

## Requirement

I won't list all of assumptions made about the project, however some stuff is important for the discussion.

The app is being re-written as it's a standard app, not connected with _THE cloud_. Problem behind it, is that there  is no way to allow therapist sharing a plan for child in public catalogue, nor copying easily plans between two tablets. Feature that could also be interesting is reviewing plan completion by a child.

So we sat down and having all of those assumptions discussed data model. The proposed one, is standard normalized [data model](https://github.com/autyzm-pg/friendly-plans/blob/master/doc/database-description.md) which for me is bit noisy and redundant. So I've found an ally in [Elias](https://github.com/wingsofovnia) and lead a rebellion to propose simpler data model for entities combined with event log for tracking execution [Event log version](https://github.com/autyzm-pg/friendly-plans/issues/84).


## Event sourcing on mobiles
What hit me hard here is that this concept did not resemble anything to anyone. Some liked it, some did not. So I've started to think more about it, and come back to my old feeling: DDD and most of it concepts (like CQRS and EventSourcing) are popular in .net space, while there is little recognition of it in Java space and none on Android. We're going to come back to this in future post, today we stick to the app.

I've event googled about event sourcing on Android and mobiles and found 0 examples ([except for one in C#](https://github.com/sgmunn/Mobile.CQRS)). I admit it's not the best environment for usage of this pattern, as my feeling is that apps have usually different focus. However 0 relevant results made me wonder, maybe hardware limitations or software ones are the reason?  Mobiles are big enough thou to have someone interested in the pattern, enough to check it and write about it.

## Request
And here we come with my request to you. Please let me know in comment what you think about both approaches. Can the SQL lite serve as event store?

Does it even make sense to build it in such a way with little information that you have?

Also feel free to comment on GH
