---
layout: post
title: Metaprogramming zamiast konwencji nazw
date: 2010-12-08 21:43:45.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Tak chwalę wam i chwalę konwencję nazw i jak to fajnie jest ją mieć, a każdy chyba wymieni mi przykłady, że czasem nie jest to najlepszy pomysł by tylko na niej polegać. I co jak ktoś chce mieć własne nazwy metod lub co gorsza, wykonuje legendarną zmianę wszystkich frameworków bo ma pojo klasy. Czy taka osoba jest skazana na zmienianie nazw po wsze czasy?<br />
Nie. Nasz dzielny bohater nie jest stracony, gdyż dziś pozna tutaj inne sposoby informowania na oznaczanie ważnych metod w cyklu życia strony/requestu.</p>
<p>Nasi dzisiejsi bohaterowie to adnotacje i zostali przedstawieni javie w jej 5tym wydaniu. Są już dość długo na rynku, ale do dziś wielu nie może ich zaadoptować. Natomiast tapestry z racji bycia nowym frameworkiem rozpoczęło swój żywot przyjmując w pełni ich dobrodziejstwo.</p>
<p>Adnotować możemy wiele rzeczy, pola, metody, konstruktory itd. Kilka adnotacji nawet już się na blogu pojawiło, mimo iż starałem się ich za wcześnie nie pokazywać. Streśćmy więc może co już znamy i co jeszcze przyjdzie nam poznać lepiej.</p>
<p><strong><span style="text-decoration:underline;">@Persist </span></strong>- tę adnotację już znamy. Umieszczona nad polem powoduje, że strona zachowuje się jakby posiadała stan (a teoria mówi, że HTTP jest bezstanowe). Zapisywane wartości pakowane są do mapy i wsadzane do sesji użytkownika. Każda z wartości jest związana dzięki temu z danym użytkownikiem, stroną i konkretnym polem a Tapestry troszczy się o to by nie pojawiły się te informacje nigdzie indziej. Poprzez atrybut value adnotacji można doprecyzować zachowanie podczas zapisu.</p>
<p><strong><span style="text-decoration:underline;">@InjectPage</span></strong> - również znamy, umieszczone nad polem wstrzykuje obiekt strony o klasie tej samej co pole. Pozwala to nam przekazywać wartości i nawigować pomiędzy stronami. Adnotacja posiada opcjonalny atrybut, informujący o konkretnej instancji strony do wstrzyknięcia. Do użycia w przypadku gdy klasa pola jest interfejsem implementowanym przez stronę. Dość to naciągane, ale i taką opcję mamy.</p>
<p>To teraz nowe zabawki. Poznaliśmy już metody  onActivate i onPassivate. Działają dobrze, ale strasznie upierdliwe są i dużo pisać trzeba przy nich. No więc dla leniwych (jak ja) mamy adnotację</p>
<p><span style="text-decoration:underline;"><strong>@ActivationRequestParameter</strong></span> - Można nią adnotować jedno pole jedynie, ale dla większości będzie to wystarczająco. Jeśli jest to własny obiekt (nie string, bool etc) to potrzeba będzie dostarczyć translatora (tak jak w przypadku onActivate i onPassivate). Wyjątek stanowią encje hibernata, dla których tapestry samo tworzy translatory (o ile mamy bibliotekę integrującą tapestry z hibernatem w naszym projekcie)</p>
<p>Zmniejszając ilość kodu w naszej aplikacji trudno jest nie zauważyć wszystkich metod get i set jakie  powstają by wspierać templaty .tml. No więc czas i to usunąć przy pomocy adnotacji <span style="text-decoration:underline;"><strong>@Property</strong></span>. Generuje ona dla prywatnego pola gettery i settery. Dodatkowo, korzystając z atrybutów adnotacji, możemy ustawić by tapestry generowało tylko getter/setter w przypadku gdy drugą metodę stworzymy sami bądź gdy nie chcemy by takowa była dostępna w aplikacji.</p>
<p>Pamiętacie naszą metodę <em>setupRender(){}</em> ? Otóż okazuje się, że dobrze by było gdyby się nazywała init(){} a nad nią dało się umieścić adnotację <strong><em>@SetupRender</em></strong>. I wiecie co? Da się :) Każda z metod cyklu życia strony ma swój odpowiednik w adnotacji.</p>
<p>Programując w tapestry natkniemy się na o wiele więcej adnotacji, lecz pozwólcie mi zostawić kilka na przyszłe wpisy, gdy będę zajmować się związanymi z nimi pojęciami. Poza tym wpis mi się robi długi ;)</p>
<p>Do następnego</p>
<p>&nbsp;</p>
<p>PS nie twierdzę, że konwencja nazw jest zła. Wręcz przeciwnie, lubię ją, ale nie zawsze jest ona najlepszym wyjściem. Do pisania na szybko preferuję nazywać moje metody zgodnie z konwencją, gdyż tak mi jest łatwiej (tak, tak onActionFrom też któregoś dnia zamienimy na adnotację). Później natomiast to już różnie bywa. Moja rada: dogadajcie się w teamie jak wam bardziej pasuje programować. Tapestry się do was dostosuje :)</p>
