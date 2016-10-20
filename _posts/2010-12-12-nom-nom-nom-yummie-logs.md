---
layout: post
title: Nom, nom, nom. Yummie logs
date: 2010-12-12 14:07:26.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Dziś lightowo będzie. Ostatnio nie miałem czasu pisać to chcę nadrobić kilkoma krótszymi wpisami.</p>
<p>Większość z nas wie, że logowanie jest dobre. Wprawdzie są osoby, które uważają, że każda linia kodu do tworzenia logów to linia stracona, ale ja się z tym nie mogę zgodzić. By to wyjaśnić zacznijmy od mojej historii, by łatwiej było zrozumieć dlaczego dla mnie ten temat jest istotny.</p>
<p>Pan Michał uczył się programować w Javie na studiach, głównie z książki Core Java I i II oraz korzystając z pomocy <a href="https://pe.ug.edu.pl/nauczyciel/631" target="_blank">Jakuba Neumana</a> (z którym miałem laborki). Pojęcie debuggera było mi całkowicie obce, ba pojęcie IDE było obce. Do dziś pamiętam jak dziwiłem się po co w eclipsie trzeba tworzyć projekty. Przecież wystarczy wszystko skompilować i działa :P</p>
<p>Niestety, ale wtedy nie cały mój kod był tak doskonały jak jest teraz ;) Zdarzał się błędy :( Na szczęście to już za mną :E Wtedy trzeba było jednak znaleźć miejsce w którym aplikacja postąpiła źle (no bo wiadomo, że to nie ja się pomyliłem). Naturalnym rozwiązaniem było System.out.println(); najlepiej co drugą linijkę by zbierać informacje na temat stanu obiektu i możliwych przyczyn błędu. W pracy zacząłem szybko sam zauważać wady takiego podejścia i pojawiła się potrzeba posiadania jakiegoś lepszego medium do logowania. Wpierw próby z przekierowaniem System.out do pliku logów, następnie żmudna nauka log4j i kolejnych frameworków wspierających mnie w tym zadaniu. W moim kodzie znaleźć można przeważnie dużo log.trace/.debug , ponieważ nie jestem nauczony korzystać z debbugera. Walczę z sobą by korzystać z niego częściej, ale logi są przydatne i dla mnie wygodnie się z nimi pracuje. Ponadto są niemal nic nie kosztują przy odpowiednim zastosowaniu.</p>
<p>Log4j znać winien każdy, slf4j powinno się znać, jak nie znamy to polecam nadrobić, gdyż jest to świetna biblioteka, znacząco ułatwiająca logowanie. Tworząc ten wpis zakładam, że rozumiesz drogi czytelniku, po co tworzy się logi.</p>
<p>A gdzie w tym wszystkim Tapestry? Ano dziś pokażę wsparcie dla logowania. Przed, bądź po lekturze tego tekstu polecam wszystkim, którzy nie czytali, zapoznać się z serią wpisów  <a title="Clean code, Clean logs" href="http://nurkiewicz.blogspot.com/search/label/logging">Tomasza Nurkiewicza</a> na temat tworzenia logów. Teksty te wybiegają daleko ponad to co ja pokażę, tu wystarczy nam wiedzieć czym i po co są logi</p>
<p>No to zaczynajmy, spójrzmy na nasz projekt. Na stronie guessGame wyświetlamy aktualnie poprawny wynik. Nie jest to szczególnie mądre, gdybyśmy teraz wprowadzili opłaty i wygraną za poprawne trafienie, to szybko zostalibyśmy bankrutami. No, ale trzeb przecież sprawdzić czy aplikacja działa i co wylosowała, inaczej skąd mamy wiedzieć, że to w ogóle działa.</p>
<p>Po pierwsze wstrzykujemy instancję <em>org.slf4j.Logger</em> do naszej klasy</p>
<p>[sourcecode language="java"]<br />
@Inject<br />
private Logger log;<br />
[/sourcecode]</p>
<p>I tyle. Tapestry samo zadba o zainicjalizowanie loggera, jako że slf4j jest natywnie wspierany przez framework. Podczas poprawiania nawigacji, wyekstrahowaliśmy z metody setupRender metodę restet(). Wypada sprawdzić co zostało wylosowane</p>
<p>[sourcecode language="java"]<br />
void reset() {<br />
	numberToGuess = new Random().nextInt(10) + 1; // moved from setupRender<br />
	log.debug(&quot;{} is drawed number&quot;, numberToGuess);<br />
}<br />
[/sourcecode]</p>
<p>Wynik jest przewidywalny: <em>[DEBUG] pages.GuessGame 6 is drawed number</em></p>
<p>No, ale mamy też bardziej typowe potrzeby logujące, np potrzebujemy sprawdzić co użytkownik kliknął by wiedzieć czy dobra wartość jest przekazywana do kolejnej strony.</p>
<p>[sourcecode language="java"]<br />
Object onActionFromGuess(int guess) {<br />
	log.debug(&quot;[Enter] onActionFromGuess with param '{}'&quot;, guess);<br />
	final boolean isCorrectAnswer = numberToGuess == guess;<br />
	resultPage.setCorrectAnswer(isCorrectAnswer);<br />
	resultPage.setPlayerGuess(guess);<br />
	log.debug(&quot;[Exit] onActionFromGuess to page '{}'&quot;, resultPage);<br />
	return resultPage;<br />
}<br />
[/sourcecode]</p>
<p>Wynik znów przewidywalny (kliknięty link nr 7)<br />
<em>[DEBUG] pages.GuessGame [Enter] onActionFromGuess with param '7'</em><br />
<em> [DEBUG] pages.GuessGame [Exit] onActionFromGuess to page 'pl.rits.blog.projektBlogowy.pages.GuessResult@b52f79'</em></p>
<p>Problemem jest to, że  takich linii Enter - Exit w kodzie może się zrobić dużo. Do tego wyglądają niemal identycznie i idealnie zaśmiecają nam kod. W tym miejscu przychodzi nam z pomocą mała adnotacja <em>@Log</em></p>
<p>Usuwamy obie linijki log.debug(...); z metody onActionFromGuess i wstawiamy nad nią adnotację @Log. Otrzymamy takie coś</p>
<p>[sourcecode language="java" highlight="1"]<br />
@Log<br />
Object onActionFromGuess(int guess) {<br />
	final boolean isCorrectAnswer = numberToGuess == guess;<br />
	resultPage.setCorrectAnswer(isCorrectAnswer);<br />
	resultPage.setPlayerGuess(guess);<br />
	return resultPage;<br />
}<br />
[/sourcecode]</p>
<p>A wynikiem dla kliknięcia w 7 będzie<br />
<em>[DEBUG] pages.GuessGame [ENTER] onActionFromGuess(7)</em><br />
<em> [DEBUG] pages.GuessGame [ EXIT] onActionFromGuess [pl.rits.blog.projektBlogowy.pages.GuessResult@13d99c]</em></p>
<p>Czyli odrobinę inaczej niż ja sam napisałem, ale daje tyle samo informacji.</p>
<p>Adnotować można wszystkie metody na stronach, bez względu na ich widoczność. Adnotacja w pełni respektuje nasze ustawienia dla loggera więc gdy przenosimy się na produkcję, to nic nie będzie wyświetlane. Poziom logowania jest domyślnie ustawiony na Debug, ale można to zmienić.</p>
<p>To przyjemnego logowania i do następnego razu.</p>
