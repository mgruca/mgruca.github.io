---
layout: post
title: End of the world post mortem
date: 2012-12-28 12:36:27.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Date
- java
- Mutability
- testing
---
<p>As we all know, on last Friday world was supposed to end.</p>
<div>
<p>Luckily your friendly neighbor Java programmer found in the internet a piece of code responsible for running that, left by Evil Mayans, which looked like this:</p>
<div></div>
<div>
<pre class="brush: java; gutter: true; first-line: 1">public class World {

	private WorldDestroyer worldDestroyer;

	public void end(){
		Date endOfTheWorldDate = worldDestroyer.getApocalypseDate();
		if(endOfTheWorldDate.equals(new Date())){
			worldDestroyer.run();
		}//no need for ending bracket as the world ends :D But Java compiler does not know that :( Feathered Snake damn you holy Sun!
	}
}</pre>
</div>
</div>
<div></div>
<p>So what our hero could do?</p>
<p>First of all he could delete the <em>if</em> check or even remove whole World class, however that would most likely fail some tests and would result in alarm raised by Evil Mayan CI server. They would know that something is wrong,  fix the code, hence the world would end!</p>
<p>So he had to be smarter. Our hero sat and thought for some time, but not for so long, remember The End Of The World is coming! Using all his Java knowledge and team work experience he did the change!</p>
<div>
<pre class="brush: java; gutter: true; first-line: 1; highlight: [3]">public void end(){
		Date endOfTheWorldDate = worldDestroyer.getApocalypseDate();
		endOfTheWorldDate.setYear(20);
		if(endOfTheWorldDate.equals(new Date())){
			worldDestroyer.run();
		}
	}</pre>
</div>
<div></div>
<p>So what he did? He used one of biggest flaws of <em>java.util.Date </em><strong>Mutability</strong><em>. </em>Yes, you may change internal state of Date using one of it's method. No need for some sophisticated techniques.</p>
<p>I'm doing all of Java interviews in <a title="dc homepage" href="http://dc.com.pl">DC</a>, and it astonishes me how many people don't know that this class is <strong>Mutable</strong>. Yes, after instantiating it, you may change it internal value freely, which may lead to a lot of problems.</p>
<div></div>
<p>It surprises me how many people does not know about it. During last interview, right before Christmas, Senior Java Dev (with &gt;5yrs of experience) tried to convince me that the class is immutable and that may be one of it flaws.</p>
<p>And what's the big deal?</p>
<p><strong>You should never trust in Date object, you've shared or received from external World </strong>(in OO terms -&gt; outside your class).</p>
<p>It is possible to build anything on top of java.util.Date. In most cases, no one will try to harm you, but I would not build large application / framework on top of that assumption. So while working with Date, you're going to learn everything about defense programming. Quick enough you'll understand that it's not enough to just copy input parameters, but you also have to return defense copies while returning values.</p>
<p>But okay, let's stop bashing Date class for a moment. If you want to learn more about techniques that may be useful to work with it, please check the book that <strong>EVERY</strong> Java programmer should read:<a title="Amazon link to Effective Java" href="http://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683"> Effective Java</a> by Joshua Bloch. It may not have anything about Date class, but it for sure shows how to achieve immutability and how to work with mutable code.</p>
<p>Other good solution is to use 3rd party library, like e.g. <a title="Joda Time home page" href="http://joda-time.sourceforge.net/">JodaTime</a> It will solve a lot of your problems. It's also quite popular, so most of the frameworks will provide support for it's classes.</p>
<p>Ok, let's investigate second flaw of this code. <strong>It's not testable</strong>.</p>
<p>You just can't test code that is dependent on time. It has to fail from time to time, raising false alarm. If you assert based on year, it may fail at midnight every 5-8 years. If you assert on month, it may fail before payday, etc. But don't worry, the battle is not lost yet. The solution is trivial: create an external class called <em>Clock / TimeService / TimeManager / DateFactory </em>or something along the line, consistent with you'r project naming convention, and just move instantiation of Date there. What we achieve this way? You can switch implementation of that class for test purpose and you won't get broken CI builds from time to time :)</p>
<p>So how it should look like in the end? (at least this variant of code)</p>
<div></div>
<div>
<pre class="brush: java; gutter: true; first-line: 1">public class World {

	private WorldDestroyer worldDestroyer;
	private Clock clock;

	public void end(){
		Date endOfTheWorldDate = worldDestroyer.getApocalypseDate();
		if(endOfTheWorldDate.equals(clock.now())){
			worldDestroyer.run();
		}
	}
}</pre>
</div>
<div></div>
<div>Now much better. Good that evil Mayans did not know about it :)</div>
