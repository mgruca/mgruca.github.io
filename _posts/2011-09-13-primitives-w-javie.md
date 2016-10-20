---
layout: post
title: Primitives w javie
date: 2011-09-13 18:25:32.000000000 +01:00
type: post
published: true
status: publish
categories:
- Tech
tags: []
---
<p>Trochę mnie nie było, ale i tematy się pozbierały. Jak pokonam na dobre lenia to opiszę wszystko co mi na wątrobie leży i w kajecie zanotowałem. Zaczynamy od tematu lekkostrawnego: primitives.</p>
<p>Od jakiegoś czasu, przeglądając zasoby sieci można wywnioskować, że jednym z większych niepowodzeń Javy, jest nie w pełni obiektowy model. Mianowicie Java posiada typy takie jak int, boolean, float, etc. Dalej można doczytać, że na szczęście nadchodzą języki, które uratują nas od tego przekleństwa (Scala, Groovy, Clojure i pewnie wiele innych).</p>
<p>Ale czy primitives (tak, nie wiem jak to się na PL tłumaczy) są aż tak złe?</p>
<p>Są trochę niebezpieczne, jako że Java je auto (un)boxuje (od 1.5), na podstawie czego Joshua Bloch stworzył kilka <a title="Joshua Bloch: Java puzzlers book" href="http://www.javapuzzlers.com/" target="_blank">Java Puzzlers</a>. Nie są też obiektami, przez co nie pasują do obiektowego modelu. Jednak mają przynajmniej dwie zalety z powodu których powstaje ten wpis i z których można korzystać do woli.</p>
<ol>
<li>Każdy primivitive ma wartość domyślną</li>
<li>Nigdy nie mogą być nullem</li>
</ol>
<p>Dlaczego jest to według mnie istotne? A szczególnie istotne w kontekście warstwy danych?</p>
<p>Weźmy najprostszą flagę Boolowską, powiedzmy, że oznacza ona czy email usera został zwalidowany (kliknął jakiś link w @ or sth). Korzystając z Hibernate Criteria API tworzycie zapytanie wykorzystując ją jako warunek. Zapytanie ma wyciągnąć wszystkie nieaktywne konta użytkowników. Wyglądało by to mniej więcej tak:  (Pseudokod pewnie idealnie się kompiluje, jeśli nie to proszę nie informujcie mnie o tym ;))</p>
<p>[sourcecode language="java"]<br />
public class Email {//nasza encja<br />
 ...//adnotacje i inne potrzebne pierdoły<br />
 private Boolean active;</p>
<p> public void setActive(Boolean active){<br />
 this.active = active;<br />
 }<br />
 public Boolean isActive(){<br />
 return active;<br />
 }<br />
 .... // pola inne i metody<br />
}<br />
public class JakiesDaoEmail {<br />
 public List&lt;Email&gt; mojaMetodaSzukajacaMaili(Boolean active){<br />
 Criteria inactiveEmails = createCriteria().addRestriction(Restriction.eq(&quot;active&quot;, active));<br />
 ...<br />
 }<br />
}<br />
[/sourcecode]</p>
<p>No i dobra, co jest tu nie tak i dlaczego primitive miałby nam zrobić dobrze, a przynajmniej trochę pomóc?</p>
<p>Zacznijmy od tego co jest źle a później dopiero o rozwiązaniach. W przykładzie, tak jak jest zdefiniowany ten wihajster, użytkownik może mieć maila aktywnego, nie aktywnego i ch..olera go wie jakiego. O tym co oznacza null pogadamy przy okazji kolejnego wpisu na temat null object pattern (ten się za bardzo rozrósł i musiałem dzielić). Zapytanie więc zwróci nam nie pełną listę nieaktywnych userów.</p>
<p>Oczywiście nie w ciemię bity  programista może zacząć zabezpieczać się przed nullem, w końcu wystarczy zainicjalizować pole w momencie deklaracji i w seterze sprawdzać czy nie dostaliśmy nulla. Korzystając z hiberneta można w restrykcjach na sztywno umieścić or by szukał też nulli, dołożyć jeszcze adnotację notnull i column z deklaracją default value dla kolumny i  ... o rly?</p>
<p>Jeśli macie team dość zdyscyplinowany to pewnie można. Jeśli nie, to łatwiej przekonać wszystkich by korzystali z primitives.  Jak wspomniałem każdy typ podstawowy (czy jak się je oficjalnie nazywa), posiada wartość domyślną. No tak, obiektowe wersję też ją mają, ale null nie jest kandydatem z moich snów i marzeń.</p>
<p>Korzystając z prymitywów zostanie nam wprawdzie jeden problem: unboxing, ale to już problem wyższej warstwy. W każdym bądź razie tworząc statystyki nie odkryjecie, że w systemie macie tak naprawdę jeszcze raz tyle userów o nie aktywowanych mailach ;)</p>
<p>I ... to jeszcze nie koniec :) Obiektowe odpowiedniki primitivów zazwyczaj widziałem <em>'czyszczone'</em> poprzez setActive(null). Powodzenia w debuggowaniu tak <em>wyczyszczonej</em> flagi. Mały bool może nie da 100% gwarancji, że NPE nie poleci (przekazanie Booleanu = null jako parametru w połączeniu z unboxem zrobią nam ała), ale znacząco utrudni psucie nam innych fragmentów kodu czy zaśmiecanie ich przez kod szukający nulli.</p>
<p>I tak, wiem, że opisuję haki na bad practices, ale gdzie bym nie pracował to się z tym spotykam. Praktyka wstawiania siedzi tak mocno w nas, że ostatnio i <a title="Koziołek o testach" href="http://koziolekweb.pl/2011/08/23/testy-dla-wielu-danych-w-testng/" target="_blank">Koziołek</a> prezentując testy posłużył się nullem miast wstawiać zera. Na dzonie przeglądając example kody tu i ówdzie nulla też się znajdzie. No i niby nic, bo większość wie o co chodzi, koncepcja przekazana. Tyle że młodzież co jeszcze NPE nie szukała, się szybko uczy. A później będzie, że jak Koziołki tak robią to i oni powinni.</p>
<p>Od dziś z powyższej przyczyny postaram się walczyć w moich pseudokodach z nullami. Amen. Rozejść się można, koniec wpisu (bosz z 2 tyg go produkowałem :/ Jeśli coś nie ma sensu to sorry, ale z 2x go dzieliłem. Feedback mile widziany :))</p>
