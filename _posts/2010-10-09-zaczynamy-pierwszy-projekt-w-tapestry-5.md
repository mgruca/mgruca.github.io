---
layout: post
title: Zaczynamy pierwszy projekt w Tapestry 5
date: 2010-10-09 13:24:47.000000000 +01:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Tak jak obiecałem w poprzednim wpisie: czas zacząć pracę. Projekt stworzymy z wykorzystaniem mavena. W Eclipsie z wtyczką m2 New -&gt; Maven -&gt; Maven Project, na ekranie wyboru archetypu wybieramy która wersja nas interesuje. Obecnie wybieramy pomiędzy Tapestry 5.1.0.5 a 5.2.1-snapshot. Pierwszy to stabilna wersja, drugi jest jeszcze rozwijany, ale w wpisach będę się odwoływać do zawartych w 5.2 nowościach. Zmiany nie są poważne, ale podążają przyjętą w Tapestry ścieżką stabilnego rozwoju, co związane jest z burzliwą historią frameworku, ale o tym innym razem.  <a href="http://tapestry.apache.org/tapestry5.2-dev/quickstart/" target="_blank">Jeszcze referencja </a>do tutoriala pokazującego jak rozpocząć projekt korzystając z konsoli Mavena.  W obu przypadkach otrzymujemy projekt podobny z tym przedstawionym na zrzucie ekranowym. Sam obrazek jest odnośnikiem do dokumentacji opisującej strukturę projektów.<a href="http://tapestry.apache.org/tapestry5.1/guide/project-layout.html"><img class="alignleft" title="Układ projektu Tapestry" src="{{ site.baseurl }}/assets/projectlayout.png" alt="tapestry-project-layout" width="347" height="579" /></a></p>
<p>O ile w wielu szkieletach aplikacyjnych układ pakietów nie ma najmniejszego znaczenia, o tyle w Tapestry jest to podstawa konfiguracji. Już wyjaśniam, T5 kieruje się zasadą <em>convention over configuration</em>, której jednym z przejawów jest układ katalogów.  Podobne podejście znać mogą użytkownicy np grailsów. Pomijając strukturę mavenową, istotne jest umiejscowienie kilku pakietów. Tapestry skanuje nasz projekt od głównego korzenia  (<em>package root</em> - w tym przypadku <em>com.example.myapp</em>) w poszukiwaniu pakietów o nazwach:</p>
<ul>
<li>services</li>
<li>pages</li>
<li>components</li>
<li>mixins</li>
</ul>
<p>Dodatkowe moduły (np integracja z hibernatem) mogą dodać kolejne domyślne lokalizacje, ale tylko wymienione trzeba znać, a spokojnie można pominąć nawet mixins.</p>
<p>Mając już strukturę podobną do tej widocznej obok, możemy uruchomić to co powstało poprzez wtyczkę run-jetty-run / wtyczkę/konsolę mavena / spakowanie całości do pliku war i uruchomienie w kontenerze aplikacyjnym (jetty czy tomcat wystarczy).</p>
<p>W przypadku run-jetty-run niezbędne jest podanie dodatkowo jako vm argumentu <em> -Dorg.mortbay.jetty.webapp.parentLoaderPriority=true</em> Znalazłem dziś działającego następce run-jetty-run. Można go znaleźć na <a href="http://code.google.com/p/itguides/" target="_blank">google code</a> Działa tak samo, ale pozwala nam korzystać z nowszej wersji Jetty i własnej konfiguracji serwera.</p>
<p>Uruchamiając mavenem jako goal podajemy <em>jetty:run</em></p>
<p>Informacje jak skonfigurować inne IDE możemy znaleźć na <a href="http://wiki.apache.org/tapestry/Tapestry5HowTos" target="_blank">Tapestry Wiki</a> w sekcji <em>Preparing your development environment</em></p>
<p>A następnym razem zaczniemy kodować i pozmieniamy trochę strony.</p>
<p>&nbsp;</p>
<p>BTW mam skłonności do offtopicowania, upominajcie mnie proszę jak zacznę za bardzo od tematu odbiegać :)</p>
<p><a href="http://tapestry.apache.org/tapestry5.2-dev/quickstart/" target="_blank"><br />
</a></p>
