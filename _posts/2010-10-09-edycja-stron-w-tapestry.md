---
layout: post
title: Edycja stron w Tapestry
date: 2010-10-09 19:00:38.000000000 +01:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>No to skoro mamy już pierwszy działający projekt  to czas coś zakodzić.</p>
<p>Zacznijmy od uruchomienia projektu i przejścia na stronę główną, jeśli wszystko działa to wracamy do edytora i otwieramy pliki Index.java i Index.tml Pierwszy pod lupę pójdzie plik .tml. Samo rozszerzenie pochodzi od Tapestry Markup Language, ale spokojnie, nie musimy poznawać kolejnego <a href="http://en.wikipedia.org/wiki/Domain_specific_language" target="_blank">DSLa</a>. Szablony (ang. Templates) Tapestry są dokumentami xhtml z drobnymi dodatkami. W przeciwieństwie np do jsp czy jsf nie musimy poznać nowego zestawu tagów by wykonać najprostszą tabelkę. Pozwala to podejrzeć większość stron T5 w zwykłym edytorze HTMLa czy podejrzeć w przeglądarce bez potrzeby kompilowania :) Znacząco ułatwia to pracę z osobami, które nie znają Tapestry czy javy w ogóle, a muszą edytować nasze szablony (np. osoby odpowiedzialne za grafikę). No, ale wracając do kodu, obecnie mamy</p>
<pre><code>
&lt;html t:type="layout" title="projektBlogowy Index"
      t:sidebarTitle="Current Time"
      xmlns:t="http://tapestry.apache.org/schema/tapestry_5_1_0.xsd"
      xmlns:p="tapestry:parameter"&gt;
        &lt;!-- Most of the page content, including &lt;head&gt;, &lt;body&gt;, etc. tags, comes from Layout.tml --&gt;

    &lt;p&gt;${message:greeting}&lt;/p&gt;

    &lt;p:sidebar&gt;

        &lt;p&gt;
            Just to prove this is live:
        &lt;/p&gt;

        &lt;p&gt;The current time is: ${currentTime}.&lt;/p&gt;

        &lt;p&gt;
            [&lt;t:pagelink page="Index"&gt;refresh&lt;/t:pagelink&gt;]
        &lt;/p&gt;
    &lt;/p:sidebar&gt;

&lt;/html&gt;
</code></pre>
<p>Na stronie po za kilkoma tagami htmlowymi widzimy <em>${currentTime}. </em>Jest to tak zwana ekspansja (ang. expansion). Domyślnie jest to odwołanie do własności (ang. property) strony, z wykorzystanie notacji java beans (czyli 2 metody: get/is i set) . Ale stop, strona ma własności? W Tapestry ma. Każdy template jest ściśle związany z plikiem java o tej samej nazwie znajdującym się w tym samym pakiecie. Tapestry jest silnie zorientowane na stronę a stroną jest w tym przypadku plik .java. To od jego lokalizacji zależy adres URL jaki wyświetli się w przeglądarce. Wszystkie strony muszą znajdować się w pakiecie pages bądź w jego podpakiecie. Jak już pewnie się domyśliliście wszystkie properties są dostępne z poziomu szablonu. Zmodyfikujmy więc metodę getCurrentTime by otrzymać następujący kod</p>
<pre><code>
	public String getCurrentTime() {
		return "Hello at "+new SimpleDateFormat().format(new Date());
	}
</code></pre>
<p>i po zmianie w przeglądarce przeładowujemy stronę. I voila! Na stronie mamy nową wartość. Jak ktoś nie podążał uważnie za wpisem to zmienia wartość zwracaną na np HelloWorld i przeładowuje stronę bez ponownego deployowania aplikacji czy reloadu. Jedną z przyjemniejszych cech T5 jest tzw. Live Class Reloading. Wszystkie zmiany dokonane w szablonie, na stronie czy w implementacji serwisu są odwzorowane od razu na stronie. Przyspiesza to pracę nad projektem i zwiększa produktywność. Można oczywiście kupić <a href="http://www.zeroturnaround.com/jrebel/" target="_blank">JRebela</a>, ale lepiej chyba mieć te 200$ skoro różnicy nie widać ;)</p>
<p>Wracając jednak do stron. Nie wszystkie wartości do jakich chcemy mieć dostęp mogą być zapisane w postaci property, np. <em>hashCode()</em>. Co wtedy? Najzwyczajniej na świecie wywołujemy metodę <em>${hashCode()}</em>. Proste, prawda?<br />
To chyba tyle na dziś. Wymyśliłem sobie, że będę starał się tworzyć krótkie wpisy.</p>
