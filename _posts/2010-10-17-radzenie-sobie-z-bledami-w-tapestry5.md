---
layout: post
title: Radzenie sobie z błędami w Tapestry5
date: 2010-10-17 11:03:51.000000000 +01:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Dziś przyjrzymy się stronom błędu jakie generuje nam Tapestry, i wcale nie będzie to negatywny temat. Tapestry 5 stara się poprawić produktywność developera również w tym aspekcie i dostarczając jak najwięcej informacji. Jeśli się spodziewacie, że za chwilę wkleję wam stack trace na 6 ekranów to grubo się zawiedziecie. No, ale po kolei. Zacznijmy od kodu. Na początek dodajmy w klasie strony 3 gettery i w templacie strony dodajemy wywołanie wszystkich 3</p>
<pre><code>
	public String getHello(){
		return "Hello";
	}
	public String getHi(){
		return "Hi";
	}
	public String getWorld(){
		return "World";
	}

	${getHi()}&lt;br/&gt;
	${HelLo}&lt;br/&gt;
	${word}&lt;br/&gt;
</code></pre>
<p>Naszym oczom ukaże się kolorowa, pozytywnie nastawiająca, strona (choć mnie te kolory drażnią ;))</p>
<p><img class="alignnone" title="Tapestry 5 error page" src="{{ site.baseurl }}/assets/5084750406_2c66ef2681_b.jpg" alt="" width="1024" height="666" /></p>
<p>Na stronie znajdują się informacje uszeregowane według wartości jaką przedstawiają dla developera. Wpierw dowiadujemy się co się nie udało (i nie trzeba było tego z stacku wyczytać). Następnie Tapestry zaczyna uściślać informację i podawać rozwiązania możliwe. W przypadku gdy dostajemy <em>unknown value exception, </em>framework domyśla się, że mogliśmy popełnić literówkę, bądź nas IDE nie wspiera i nie podpowiada co możemy w templacie wstawić. W takim przypadku Tapestry listuje wszystkie dostępne properties do jakich możemy się odwołać na stronie. Przy kilku innych błędach również dostaniemy przydatne wskazówki. Ponadto na stronie znajduje się stack trace, w którym można usunąć fragmenty bez znaczenia dla użytkownika. Za nim dostaniemy szereg informacji o środowisku, w którym przyszło naszej aplikacji działać.</p>
<p>Jako, że głupio zostawić tak stronę z błędem, to zmieniamy word na world, wciskamy F5 w przeglądarce (tylko żadnego redeploya mi tam nie robić! Produktywność zwiększamy!) i naszym oczom ukazuje się to czego oczekiwaliśmy.  Przy okazji zwracam uwagę , że Tapestry jest case insensitive. Możemy pisać słitaśnym casem (jak na forum internetowym), a Tapestry uzna, że jesteśmy upośledzeniu i w ramach wyrównywania szans zignoruje nasze wiElbLaDY.</p>
<p>Ogółem cała strona to jedna z małych rzeczy, a cieszy.</p>
<p>Z ciekawostek: strona nie pokazuje się gdy ustawimy aplikację w producation mode. Przed T5.2 elementy były chyba w innych miejscach</p>
