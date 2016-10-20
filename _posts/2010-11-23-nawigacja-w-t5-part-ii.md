---
layout: post
title: Nawigacja w T5 part II
date: 2010-11-23 21:03:02.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Umiemy już <a href="http://mgruca.wordpress.com/2010/10/24/nawigacja-pomiedzy-stronami-w-tapestry5/" target="_blank">przemieszczać się pomiędzy stronami</a> oraz <a href="http://mgruca.wordpress.com/2010/10/30/zarzadzanie-stanem-w-tapestry5/" target="_blank">zatrzymywać ich stan</a>. Czas więc pomyśleć o przekazywaniu wartości pomiędzy różnymi stronami. Przykład? W każdej aplikacji się znajdzie, od wypełnienia przelewu po nasz: stronę z wynikiem naszej gry (BTW sam pomysł na guess game wziąłem z oficjalnego tutoriala Tapestry).</p>
<p>No to zdefiniujmy nasze nowe wymaganie. Grający po kliknięciu w link z liczbą ma zostać przeniesiony na stronę z wynikiem. Jeśli poprawnie wytypował liczbę to system ma go o tym powiadomić oraz umożliwić powrót na stronę z grą, by mógł zacząć grać od nowa. Jeśli wytypował niepoprawnie, to ma zostać o tym poinformowany oraz ma być mu wyświetlony link pozwalający grać dalej. Use case prosty. Zaczynamy kodzić :) Na początek stwórzmy stronę z wynikiem (kod tml-owy pomijam, jako że wszystko do javy wsadzę, a  jak linki się tworzy i wypisuje wartości to każdy już wie). Na stronie wyniku wstawiamy trzy property: int playerGuess, boolean correctAnswer; Pierwsza własność będzie nam potrzebna do wyświetlenia na ekranie wyboru osoby grającej, druga oznaczać będzie tylko czy to co user podał jest prawidłowe czy nie. Do tego mam 2 stringi do wyświetlenia graczowi odpowiedniego komunikatu i odpowiedniej wartości linka. Na pierwotnej stornie dodajemy następujące pole: @InjectPage private GuessResult resultPage;<br />
Zacznijmy od tego najmniej użytecznego sposobu ;) GuessGame.java modyfikujemy w następujący sposób.</p>
<p>[sourcecode language="java"]<br />
Object onActionFromGuess(int guess) {<br />
	final boolean isCorrectAnswer = numberToGuess == guess;<br />
	resultPage.setCorrectAnswer(isCorrectAnswer);<br />
	resultPage.setPlayerGuess(guess);<br />
	return resultPage;<br />
}<br />
void reset() {<br />
	numberToGuess = new Random().nextInt(10) + 1; //Przeniesiony z setupRender który teraz wywołuje tą metodę<br />
}<br />
[/sourcecode]</p>
<p>Na stronie wyników umieszczamy poza getterami i setterami następujący fragment:</p>
<p>[sourcecode language="java"]<br />
@InjectPage private GuessGame gamePage;</p>
<p>void onActivate(int playerGuess, boolean correctAnswer) {<br />
	this.playerGuess = playerGuess;<br />
	this.correctAnswer = correctAnswer;<br />
	resultMessage = correctAnswer ? &quot;That is correct answer&quot; : &quot;That is not correct answer&quot;;<br />
	linkMessage = correctAnswer ? &quot;Want to play again?&quot; : &quot;Please try again&quot;;<br />
}</p>
<p>Object[] onPassivate() {<br />
	return new Object[] { playerGuess, correctAnswer };<br />
}</p>
<p>Object onActionFromBackToGuessGame() {<br />
	if (correctAnswer) {<br />
		gamePage.reset();<br />
	}<br />
	return gamePage;<br />
}<br />
[/sourcecode]</p>
<p>W ten sposób mamy prostą i czystą nawigację, która jednak trzyma wartości w URLu, co nie bardzo mi pasuję do tej strony, jednak znakomicie mogło by się sprawdzić w wszelakiego rodzaju wizardach, które pozwalają bookmarkować kolejne kroki. Modyfikujemy więc stronę rezultatu dodając adnotację @Persist nad poszczególnymi polami, usuwamy metody onActivate i onPassivate i już mamy czysto w URLu. W zasadzie to prawie. Brak nam jeszcze miejsca, w którym będziemy nasze 2 stringi ustalać i proponuję je umieścić w znanym nam już setupRender(); Przy moich zastosowaniach jest to zawsze wygodne miejsce. Przyjrzyjmy się obu przypadkom trochę bliżej.<br />
Pierwszy oznacza pełną transparentność przekazywanych parametrów, pozwala bookmarkować stronę oraz odwoływać się do niej w prosty sposób z zewnętrznych systemów. Poprzez metodę activate można przyjąć wartości, które połączymy z odpowiednimi polami. Pola te mogą być adnotowane poprzez @Persist co pozwoli połączyć oba sposoby. W ten sposób wykonałem kiedyś dla klienta, prostą integrację z zewnętrznym systemem CRM.<br />
Drugi sposób jest bardzo wygody do przekazywania wartości pomiędzy stronami i określił bym go jako 'obiektowy'. Nie ma możliwości wstrzyknięcia z zewnątrz danych (przynajmniej ja nie kojarzę takiego sposobu) a Tapestry zajmie się za nas zarządzaniem pamięcią i nie pozwoli by dane przeciekały do kolejnych stron.</p>
