---
layout: post
title: Embedded Mongo and Windows Firewall alerts
date: 2014-01-29 00:28:29.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- MongoDB
- unit tests
- Windows firewall
---
<p>Long time no see :) I'll start slowly, so that the pace of writing helps me get back to writing.</p>
<p>Finally I had some time to work on code in the project, and found out most annoying thing ever. We're using MongoDB in production so for couple of integration tests we use <a href="https://github.com/flapdoodle-oss/de.flapdoodle.embed.mongo">embedded Mongo</a>. It's a simple tool that we are using the same way as HSQL or H2 DB. DB instance is created by tests that need it and after they are done DB is cleaned up automatically. During that short period when instance is available it may be accessed same way as any <em>normal</em> Mongo instance. We have few tests using it, just to prove that our DB layer is working as expected.  The annoyance I've mentioned in the beginning, comes when somebody miss-configure it and try to use under Windows with Firewall turned on. Below example of how to start it <strong>without mentioned issue.</strong></p>
<pre class="brush: java; gutter: true; first-line: 1; highlight: [3]">CustomMongoDBProperties dbConfig = new CustomMongoDBProperties();         
MongodStarter runtime = MongodStarter.getDefaultInstance();
Net netSettings = new Net("127.0.0.1", Network.getFreeServerPort(), Network.localhostIsIPv6());
_mongodExe = runtime.prepare(
	new MongodConfigBuilder().version(Version.Main.PRODUCTION).net(netSettings).build());
_mongod = _mongodExe.start();</pre>
<p>Looks simple? And it is, however if you're going to remove ip address, being first parameter of <em>net</em> constructor <em>"127.0.0.1"</em>, Windows Firewall will raise alarm every time you run those damned tests. Code compiles, tests are green, just FW pops up a lot!</p>
<p>I hope that it will help you to save some nerves. Of course the proper fix is to install Linux, however this one is good enough workaround for me ;)</p>
