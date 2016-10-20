---
layout: post
title: Zarządzanie stanem w Tapestry5
date: 2010-10-30 18:31:20.000000000 +01:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Zacznę od małej uwagi: staram się by wpisy układały się w jeden spójny tutorial. Jednym z moich podstawowych założeń jest nie robienie zbyt dużych kroków i stopniowe dorzucanie kolejnych koncepcji. Może teraz to będzie działało na niekorzyść wpisów, ale liczę, że gdy będzie ich odpowiednio więcej, to ta metoda będzie pomagać kolejnym osobom je czytającym. Piszę o tym dlatego, że część z tego co dziś zrobię może się wydawać mocno idiotyczna :P</p>
<p>Skoro poznaliśmy już jak się nawiguje i wywołuje akcje, to czas pomyśleć o czymś co będzie cokolwiek robiło. Zacznijmy może od czegoś prostego, jako że nie jestem najlepszym programistą, to bardziej skomplikowane rzeczy mogą mnie przerosnąć. Zaimplementujemy grę w zgadywankę. Scenariusz jest prosty: losujemy liczbę z przedziału 1-10, po czym użytkownik musi zgadnąć jaką liczbę wylosowaliśmy. Nawet ja sobie z tym powinienem dać radę ;)</p>
<p>Zacznijmy od stworzenia nowej strony. W pakiecie pages dodajemy klasę GuessGame, będzie ona dostępna pod tą nazwą w URLu czyli np <em>localhost:8080/guessGame</em>. Definiujemy w niej następujące wartości</p>
<p>[sourcecode language="java"]<br />
private int numberToGuess;<br />
 private int item;<br />
 private String resultMessage;</p>
<p> public int getNumberToGuess() {<br />
 return numberToGuess;<br />
 }</p>
<p> public String getResultMessage() {<br />
 return resultMessage;<br />
 }</p>
<p> public void setItem(int item) {<br />
 this.item = item;<br />
 }</p>
<p> public int getItem() {<br />
 return item;<br />
 }<br />
[/sourcecode]</p>
<p>oraz uzupełniamy template mniej więcej następująco</p>
<pre><code>
		Number to guess: ${numberToGuess}&lt;br/&gt;
		Result: ${resultMessage}&lt;br/&gt;
		Choose a number:&lt;br/&gt;
		&lt;t:loop source="1..10" value="item"&gt;
      		&lt;t:actionlink t:id="guess" context="item" style="padding-right: 2px;"&gt;${item}&lt;/t:actionlink&gt;
    	&lt;/t:loop&gt;
</code></pre>
<p>Mając już cel możemy przystąpić do gry. No ale jak grać? Poznaliśmy ostatnio komponent ActionLink i z niego korzystamy. Dodałem też komponent loop, który służy do iterowania po kolekcjach, bądź potrafi policzyć od 1 do 10. Tym się jednak dziś nie zajmujemy, jest on tu tylko dlatego, że okazałem się zbyt leniwy by napisać 10 osobnych action linków ;) Zmienna item jest potrzebna tylko dla loopa, o działaniu którego innym razem.</p>
<p>Stronę możemy już podejrzeć i poklikać, choć dostaniemy teraz błędy :/ i numberToGuess wynosi zawsze 0. Hmm, zainicjalizujmy więc tę wartość i dodajmy obsługę actionLinków.</p>
<p>[sourcecode language="java"]<br />
void onActionFromGuess(int guess){<br />
	if(numberToGuess = guess){<br />
		resultMessage = &quot;Success&quot;;<br />
	}<br />
	else {<br />
		resultMessage = &quot;Try Again&quot;;<br />
	}<br />
}</p>
<p>void setupRender(){<br />
	if(numberToGuess==0){<br />
		numberToGuess = new Random().nextInt(10) + 1;<br />
	}<br />
	if(resultMessage==null&amp;amp;&amp;amp;resultMessage.isEmpty()){<br />
		resultMessage = &quot;Try U'r luck! ^^&quot;;<br />
	}<br />
}<br />
[/sourcecode]</p>
<p>Kolejny raz widzimy konwencję nazw Tapestry. Tym razem jest ona związana z cyklem życia strony... zaraz, zaraz. Cykl życia strony? Ano tak, przed wersją 5.2, wszystkie strony w Tapestry żyły sobie w pool-u, do którego po użyciu zostawały zwrócone. Same wartości były przypisywane w momencie aktywacji strony, każdy kto pracował z stateless EJB powinien wiedzieć jak to działa. Reszcie spieszę wyjaśnić, iż wprowadzone to zostało, by ograniczyć ilość pamięci potrzebną dla aplikacji. Dzięki ponownemu wykorzystaniu obiektów stron, framework nie musi tworzyć dla każdego klienta nowej instancji strony. To tak w dużym uproszczeniu, ale musi teraz wystarczyć. W związku z owym cyklem aktywacji i deaktywacji (strona posiada cykl życia i cykl aktywacji i deaktywacji. Użyłem na początku akapitu niewłaściwej, ale bardziej przykuwającej uwagę nazwy), strona musi mieć możliwość zainicjowania wartości oraz wykonania operacji by być gotową do użycia poprzez różnych klientów, pomimo jej nie zmieniającej się struktury. Pierwszą z metod w tym cyklu jest setup render i użyjemy jej, a co nam. Druga metoda (onAction...) obsługuje wszystkie eventy generowane przez nasze action linki. Warto zauważyć, że w tapestry możemy przekazywać do metod parametry. Nie trzeba kombinować nad jakimiś ukrytymi checkboxami, hidden fieldami czy innymi paskudztwami, chcecie przekazać parametr do metody, to po prostu to zróbcie. Więcej o <a href="http://tapestry.apache.org/tapestry5.2-dev/guide/lifecycle.html" target="_blank">cyklu życia</a> i cyklu <a href="http://tapestry.apache.org/tapestry5.2-dev/guide/rendering.html" target="_blank">wyświetlania strony</a> znajdziemy na stronach tapestry, a i ja może coś kiedyś skrobnę.</p>
<p>Wracając do naszej gry. To co możemy już zaobserwować, to to, że najzwyczajniej w świecie nie działa :( Dlaczego dostajemy cały czas wartość 0 i brakuje nam wiadomości na stronie? Związane jest to znów z cyklem aktywacji strony. Tapestry dba o to by nie trzymać w pamięci obiektów nam zbędnych, więc to na nas spada wskazanie, które properties strony mają być zapamiętane. Można to zrobić na kilka sposobów:</p>
<p>1. Dodanie metod aktywujących<br />
[sourcecode language="java"]<br />
	void onActivate(int numberToGuess, String message) {<br />
		this.numberToGuess = numberToGuess;<br />
		this.resultMessage = message;<br />
	}</p>
<p>	Object[] onPassivate() {<br />
		return new Object[] { numberToGuess, resultMessage };<br />
	}<br />
[/sourcecode]<br />
Obie metody bazują na konwencji nazw, a więc nazwa ma znaczenie ;) onActivate (liczba parametrów jest dowolna) jest wywoływana za każdym razem gdy strona ma zostać załadowana i należy ustalić wartości pól strony. Można napisać kilka takich metod, bardzo przydatne dla stron, które przyjmują różne ilości parametrów (osobiście korzystałem z tego w przypadku stron zawierających filtry). Metoda onPassivate jest wywoływana w dokładnie przeciwnym przypadku, tj gdy strona zwracana jest do page poola. Zwrócić może jeden parametr, bądź tablicę. Zwrócić? Ale dokąd, i jak przechowywane są te wartości? Obie metody korzystają z URLa. Wpisane przez nas wartości znajdą się w adresie, co uczyni stronę prostą do bookmarkowania (idealne dla wspomnianych filtrów). Niestety nie jest to dobry sposób na przechowywanie danych w grze w zgadywanie (jakby wypisanie na ekranie wyniku to było mało!). Sprawdźmy co tam jeszcze można zrobić by nasze properties przeżywały poszczególne requesty.</p>
<p>2. Oznaczenie pola adnotacją @Persist wskazuje, że jego wartość ma być zachowywana pomiędzy kolejnymi requestami. Znów większość osób zastanowi się jak to się dzieje i gdzie Tapestry trzyma nasze wartości. Odpowiedź jest tym razem trudniejsza i brzmi: to zależy. Pokrótce postaram się to objaśnić.<br />
Jest kilka możliwości, domyślnie stosowana jest strategia trzymania wartości w sesji, zwana Session (adnotacja przyjmuje Stringa jako parametr określający, która strategia ma być użyta. Stałe je reprezentujące znajdziemy  w obiekcie PersistenceConstants)<br />
Flash  - wartość pola wkładana jest wprawdzie do sesji, ale usuwana jest z niej w momencie pierwszego pobrania. Można wykorzystać do np wyświetlenia komunikatu błędu)<br />
Client - mimo iż jest, to odradzam korzystanie. W tym trybie tapestry wrzuci obiekt w URLa w sobie tylko znany sposób, bądź (w przypadku formularzy), skorzysta z ukrytych pól. Prosty sposób by narobić sobie problemów, ale jeśli będzie potrzeba, to taki mechanizm jest)</p>
<p>Z jednym z powyższych możemy zapisywać wartości na stronach, co jest nam niezbędne do tego by gra działała. Przetestować proszę kod, powinien działać. Jako, że długie się to zrobiło to kończę wpis :)</p>
<p>Przypomnę jeszcze tylko, że całość kodów źródłowych można znaleźć w moim repo <a href="https://bitbucket.org/mgruca/blogowe-skrawki" target="_blank">bit bucketa</a></p>
<p>-= Edit =-</p>
<p>Długo szukałem nazwy dla tego posta, niestety właściwa przyszła dopiero po publikacji. Ta zmiana wyjdzie na zdrowie tej notce, bo o utrwalaniu danych będzie więcej w przyszłości</p>
