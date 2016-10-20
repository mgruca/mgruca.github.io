---
layout: post
title: only private static ... methods?
date: 2011-06-14 22:23:18.000000000 +01:00
type: post
published: true
status: publish
categories:
- Tech
tags: []
---
<p>Kolejny temat który mi po 33degree został, powoli kończę zaległości :P</p>
<p>Wpis inspirowany przez <a title="Hamlet D'Arcy blog" href="http://hamletdarcy.blogspot.com/" target="_blank">Hamleta D'Arciego</a>. Na jednej z prezentacji (bodaj o inspekcjach w Intellij), wspomniał on o jednej z własnych praktyk programowania. Mianowicie uważa on, że wszystkie metody prywatne powinny być statyczne.</p>
<p>Dlaczego?</p>
<p>Odpowiedź jest całkiem prosta: w przypadku statycznych metod, java nie pozwoli nam odwoływać się do nie statycznych pól klasy. Proste a genialne :)</p>
<p>No, ale moment, co zyskujemy na tym, że nie możemy korzystać z pól?</p>
<ul>
<li>Wprowadzamy bardziej funkcyjne podejście do metod.</li>
<li>Można założyć, że dla 2 jednakowych inputów będziemy mieli taki sam output.</li>
<li>Problemy wielowątkowości nam odpadają (taaa, no powiedzmy). Nie próbujemy modyfikować w 2 wątkach tego samego pola.</li>
<li>Jest czyściej i łatwiej przeprowadzić ekstrakcję klasy, w końcu nasza metoda nie zależy bezpośrednio od klasy w której się znajduje.</li>
<li>Łatwiej jest zapewnić SRP (Single responsibility), metoda więc będzie robić raczej jedną rzecz a dobrze</li>
</ul>
<p>Jednak nie ma róży bez kolców. Osobiście nie wprowadzę takiego stylu w projektach w których uczestniczę ponieważ</p>
<ul>
<li>Tworzę czasem metody które, np opakowują geta z mapy, tak tylko żeby lepiej mi się czytało co to robi.</li>
<li>Dziwne by było dla mnie też, przekazywanie  np klasy DAO do statycznej metody, tylko po to by na przykład pobrać pierwszy obiekt z zwracanej listy (znów w sumie kryterium czytelności)</li>
<li>Mam uczulenie na słowo static. Ostatnio eksperymentuję nawet z stałymi które nie są statyczne (o czym może więcej kiedyś, jak uda mi się to do końca doprowadzić)</li>
<li>Bałbym się, że ktoś nie zrozumie moich zamiarów i następnego dnia znajdę w mojej klasie mnóstwo staticów :cold:</li>
</ul>
<p>Pomysł jednak ciekawy, zachowam go na przyszłość i jak podciągnę własny skill level/zmienię styl, to może zacznę używać. Póki co nie mogę powiedzieć, że moje wszystkie metody prywatne mogą być statyczne.</p>
<p>Co do screencastów, o których wspominałem jakiś czas temu to sobie odpuszczam. Próbowałem, kilka podejść zrobiłem i stwierdzam, że nie mam obecnie dość czasu (i chęci :( ), a dostarczenie czegoś mocno na kolanie skleconego nie zadowala mnie.</p>
<p>Za to piszę coś małego w t5. Jak uda mi się kilka pomysłów wsadzić w ten mini projekcik, to wrzucę kod na sieć i na nim poćwiczymy T5 wspólnie :) W sumie dawno T5 nie używałem więc i mi się ćwiczenie przyda :)</p>
<p>Do następnego</p>
