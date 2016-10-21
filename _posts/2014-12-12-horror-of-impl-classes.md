---
layout: post
title: Horror of impl classes
date: 2014-12-12 21:10:50.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- craftsmanship
---
<p>If you've checked me on Twitter, you may have noticed <a href="https://twitter.com/michalgruca"><img class="alignright size-medium wp-image-501" src="{{ site.baseurl }}/assets/2014-12-12_18-22-55_TweetDeck-Mozilla-Firefox-300x151.png" alt="My Twitter profile" width="300" height="151" /></a>my profile (screenshoot on right) and tag (?) line "Problem solver and father of many impl classes (not to proud about it).</p>
<p>That <em>(not to proud about it)</em> part is the reason why I'm writing this blog post. I've received a very long question regarding that part: "Why?". In follow up discussion I've agreed to share why I hate xxxImpl classes so much, even thou I've had written so many of 'em in my life.</p>
<h2>Impl ==&gt; no idea of what I'm doing</h2>
<p>Let's start first from that Impl suffix implies that we have properly named interface (at least something ;)). Going to basics, what an interface is? By wiki definition</p>
<blockquote><p><strong>Interface</strong>: In object-oriented programming, a <b>protocol</b> or <b>interface</b> is a common means for unrelated objects to communicate with each other.</p></blockquote>
<p>In my own words, it's a contract between client and class that's realizing the interface, it's also a one foundation of OO which allows us to do a polymorphism and realize encapsulation. We can leave encapsulation and go straight to polymorphism, which is why we do Java, OO and everything else ;)</p>
<p>Interface is just a contract, way in which it's going to be realized is totally dependant on implementation and there may be more than one (and even should!). Having just one implementation is not necessary wrong, and may have sense, however the biggest fail of 'em all is if that one implementation is called sthImpl. Why? Because it does not tell us anything at all (!) about how contract is being realized. In order to check it, we need to dive into code and figure it out.</p>
<p>We could do so much better! Let's take for example my project, and something I've been doing a week ago (while waiting for plane on lovely Munich airport).</p>
<p>We're extracting part of system functionality into a set of new projects which will be deployed on many servers. We needed a configuration module, however we could not decide about how we should realize that in distributed environment.</p>
<p>For first implementation I've decided to go with property files, tied close to implementation (please don't judge, has nothing to do with this post). So step one was to create a contract</p>
<pre class="brush: java; gutter: true; first-line: 1">public interface Configuration {
    public Optional&lt;String&gt; get(String key);
}</pre>
<p>and follow it with first implementation</p>
<pre class="brush: java; gutter: true; first-line: 1">public class LocalPropertyFileConfiguration implements Configuration {

     //Constructor

     public Optional&lt;String&gt; get(String key){/*reading in .properties file*/};

}</pre>
<p>We already consider to use ZooKeeper or some other 3rd party service for config, and it will be as simple as adding additional class (<em>ZookeeperConfiguration</em> ?:)) implementing our configuration interface. We may leave first implementation so it can be used in tests or just discard it, does not matter. What matters is that it has proper name.</p>
<p>True, but what comes with <em>proper</em> name is fact that if I ever decide to add more functionality or modify existing one, the name of the class itself will tell me if the change I want to introduce really fits the class. And if I need to expand responsibilities, then it's super easy to rename the class. Additional bonus: if new name is too cumbersome to say, maybe it's a good time to split it into couple classes?</p>
<p>So in this example if I'd see anything related to networking inside LocalPropertyFileConfiguration class, then I would start looking what the heck is happening right away, even if I'd see the class for first time. Impl does not give you that benefit.</p>
<p>&nbsp;</p>
<p>There is also additional code smell coming from the fact we're missing proper name: no one knows what is exactly the responsibility of the implementation. From vague names often comes God Classes to life. If anyone seen any xxxDaoImpl or xxxManagerImpl then you know what I'm taking about. That is also somewhat related to bad name of interface and not clear enough contract, however Impl classes tend to make things worse.</p>
<p>Also please think about <em>Strategy pattern</em>. It's really simple to just avoid creating new classes and instead add conditional statements inside one and only Impl. Proper name encourages you to write more classes instead of expanding existing one.</p>
<h2>Takeaway</h2>
<p>Please always consider what your class is doing, and name it after something specific to the class, even if it's as stupid as <em>UserJpaDao</em> instead of <em>UserDaoImpl</em>. More importantly, please consider if you need interface at all, if there is going to be just that impl class. Extracting interfaces using IDE is really simple! It's around 6 keystrokes away at any point of time!</p>
<p>Also don't sweat too much over names. I've come up with names in this post while writing that (c's I don't remember what are actual names in code), and IMHO it's good enough. Remember, improving naming later on is usually super easy :)</p>
<p>So have a nice day and make life better for everyone from Monday with not putting impls in your code.</p>
