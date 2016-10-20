---
layout: post
title: Geb goodness
date: 2014-02-09 18:20:52.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- geb
- testing
---
<p>Recently I've been playing around with Spock and a bit with Geb. I'm under big impression so today let's check out what <a title="Geb project home page" href="http://www.gebish.org/">Geb</a> is.</p>
<p>Version for managers -&gt; It's a technology that will make your developers test and allow you to cut costs. Now go send email to dev team that they should check it out, shoo.</p>
<p>Version for devs -&gt; It's a sexy framework with <strong>jQuery</strong> like DSL over <strong>Webdriver/Selenium</strong> allowing easy use of <strong>Page Objects</strong> and wiring it all up with <strong>Groovy</strong>. Those of you that had chance to work with Selenium and write traditional functional web tests in Java, most likely are now wondering what sexy can be in Webdriver.</p>
<p>Well, most importantly, Geb takes away all the boilerplate code and Java ceremony, leaving for you to fill only the important parts with simple syntax, that all of jQuery users know already (most of the examples are taken from Geb site).</p>
<pre class="brush: groovy; gutter: true; first-line: 1">$("div.some-class p:first[title='something']")
$("h1", 2, class: "heading")
$("p", text: contains("Geb"))
$("div").find(".b")
$("div").filter(".c").parents()
//Manipulating forms
$("form").name = "Geb"
assert $("form").name == "Geb"</pre>
<p>Well, for me it looks like a jQuery. When you get out of syntax and reach test context, it's even better. Regardless of what testing framework you're currently using, Geb will work out of the box with it. Personally I've been using it with Spock, but it has support for JUnit, TestNG, EasyB and Cucumber. So you don't have to be concerned about learning another tool.</p>
<p>Let's check out example below where it's used with Spock</p>
<pre class="brush: groovy; gutter: true; first-line: 1">class LoginSpec extends GebSpec {
    def "login to admin section"() {
        given:
        to LoginPage

        when:
        loginForm.with {
            username = "admin"
            password = "password"
        }

        and:
        loginButton.click()

        then:
        at AdminPage
    }
}</pre>
<p>As you can see, it's just a standard Specification. Only difference is that you use one of Geb spec classes.</p>
<p>Big win in Geb are Page Objects. I haven't used them in Selenium, however as I understand they are available there. What is a Page Object? As name suggests, it's an object representation of a page :) To be bit more specific: it's a class inheriting from geb.Page class which role is to encapsulate all the interaction with the HTML page.</p>
<pre class="brush: actionscript3; gutter: true; first-line: 1">class SignupPage extends Page {
    static url = "http://izawody.pl/signup"
    static at = { title == "Logowanie | iZawody" }
    static content = {
        loginField { $("input#login") }
        passwordField { $("input#password") }
        loginButton(to: DashboardPage) { $("input#loginButton") }
    }

    void login(String login. String password) {
        loginField.value login
		passwordField.value password
        loginButton.click()
    }
}

class DashboardPage extends Page {
    static at = { waitFor { title.endsWith("| iZawody dashboard") } }
}

//Test script
Browser.drive {
    to SignupPage
    login("admin", "admin")
    at DashboardPage
}</pre>
<p>It helps as you create a lot of reusable code that encapsulate a lot of moving parts, like text on pages, id's, HTML structure. So after updating page template you need to only update page object in order to fix dozens of failing unit tests! That's just a huge win! As a side bonus, all the elements specified in the content section, are reachable in test script, so each element may have an abstraction layer :)</p>
<p>As Geb is just an more readable abstraction layer over Webdriver, as a bonus you get a full support for all the browsers that selenium uses, so you may run your tests in FF, Chrome, Opera, IE and there is experimental support for mobile browsers. If you're running on a server there is a driver for headless browser, so all covered :)</p>
<p>Only place where Geb falls short is if you aim for job security. Even with code obfuscator you're going to fail. Code is just damn too simple :(  Other than that Happy coding!</p>
