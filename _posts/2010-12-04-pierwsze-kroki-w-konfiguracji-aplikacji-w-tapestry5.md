---
layout: post
title: Pierwsze kroki w konfiguracji aplikacji w Tapestry5
date: 2010-12-04 20:06:45.000000000 +00:00
type: post
published: true
status: publish
categories:
- Tech
tags:
- Tapestry5
---
<p>Dzisiejszy wpis będzie tylko wstępem do konfiguracji, jako że muszę uzupełnić w tym względzie własne braki :).<br />
No to dziś będzie nudno, bo będzie dużo xmla... hmm no może jednak nie. Tapestry należy do tych szkieletów aplikacyjnych, w których nie musimy pisać ni linijki w xmlu (pomijając web.xml i templaty, które są dokumentami XHTML czyli XMLami).<br />
Niektórzy stwierdzą w tym momencie, że bez sensu, nie da się programować w notatniku i na żywo podmieniać na produkcji kodu zmieniając wszystko. A ja powiem, że dzięki temu można dbać o jakość, testować, popełniać mniej błędów, korzystać z wsparcia IDE, refaktoryzować, etc.<br />
To co warto nadmienić przed przejściem dalej, to że T5 jest zbudowane na module IOC więc cała nasza konfiguracja będzie interakcją z modułem wstrzyknięć. Nadmieniam to tutaj, gdyż mówiąc na co dzień o IOC mam na myśli nasze klasy wstrzykiwane do innych naszych klas. Gdy piszę o konfiguracji, to mam na myśli interakcję z klasami dostarczanymi z Tapestry. Wiem, zagmatwałem :(</p>
<p>No ale do kodu (z którego komentarze pozwalam sobie wycinać).<br />
Specyfikacja JEE wymaga od nas byśmy zajrzeli na początek do web.xml, gdzie ponad standardową deklarację XMLa i web app (wymagane przez kontener servletów) znajdziemy następujący fragment</p>
<p>[sourcecode language="html"]<br />
    &lt;context-param&gt;<br />
        &lt;param-name&gt;tapestry.app-package&lt;/param-name&gt;<br />
        &lt;param-value&gt;pl.rits.blog.projektBlogowy&lt;/param-value&gt;<br />
    &lt;/context-param&gt;<br />
    &lt;filter&gt;<br />
        &lt;filter-name&gt;app&lt;/filter-name&gt;<br />
        &lt;filter-class&gt;org.apache.tapestry5.TapestryFilter&lt;/filter-class&gt;<br />
    &lt;/filter&gt;<br />
    &lt;filter-mapping&gt;<br />
        &lt;filter-name&gt;app&lt;/filter-name&gt;<br />
        &lt;url-pattern&gt;/*&lt;/url-pattern&gt;<br />
    &lt;/filter-mapping&gt;<br />
[/sourcecode]</p>
<p>Mam nadzieję, że na dziś się już XMLa naoglądaliście, bo więcej go w tym wpisie nie znajdziecie. Dewianci niech otworzą jakiś projekt Springowy/facowy/strursowy i wrócą za miesiąc, bo w najbliższej przyszłości nie przewiduję więcej XMLa na blogu (no .tml-e się pewnie pojawią, ale niewiele!).</p>
<p>Słowo wyjaśnienia się teraz należy co widać w zamieszczonym kodzie. Zdefiniowaliśmy root projektu, dzięki temu T5 będzie się w stanie znaleźć w naszej aplikacji (od podanego tu pakietu, szukane są pakiety pages, components, etc).</p>
<p>Druga istotna informacja to rejestracja filtra tapestry i określenie jego nazwy na app. Definiuje ona nazwę klasy w której będziemy dokonywali konfiguracji naszej aplikacji. Dla przykładu: jeśli pozostanie ona zdefiniowana jako app, to tapestry będzie szukać klasy <em>${rootProjektu}.services.<span style="text-decoration:underline;">App</span>Module.java</em>. Brak tej klasy nie jest błędem, ale uniemożliwia nadpisanie jakichkolwiek domyślnych ustawień tapestry. Wyłączy nam też możliwość definiowania nowych services więc de facto poza pół statycznymi aplikacjami, ta klasa będzie zawsze obecna.</p>
<p>Przejdźmy więc do niej i sprawdźmy co w niej znajdziemy. Niestety jest tego niewiele. Nie ma interfejsu, dziedziczenia ani, co gorsza, XMLa! :( Hańba!</p>
<p>Jest za to kilka metod. Przypomnę, że tapestry to Convention over Configuration więc korzysta z konwencji nazw by umożliwić nam konfigurację. Niestety zmiany w tej klasie będą wymagały restartu aplikacji, live class reloading tu nie działa, z doświadczenia za to wiem, że to nie jest uciążliwy problem. Po pierwszej konfiguracji jedyną zmianą jest dokładanie kolejnych services. Spójrzmy na 2 z metod znajdujących się domyślnie w klasie (tj znajdujących się w mavenowym archetypie):</p>
<p>[sourcecode language="java"]<br />
public static void bind(ServiceBinder binder) {<br />
// binder.bind(MyServiceInterface.class, MyServiceImpl.class);<br />
}<br />
public static void contributeApplicationDefaults(MappedConfiguration configuration) {<br />
configuration.add(SymbolConstants.SUPPORTED_LOCALES, &quot;en&quot;);<br />
configuration.add(SymbolConstants.PRODUCTION_MODE, &quot;false&quot;);<br />
configuration.add(SymbolConstants.APPLICATION_VERSION, &quot;0.0.1-SNAPSHOT&quot;);<br />
}<br />
[/sourcecode]</p>
<p>Pierwsza metoda służy do powiadomienia kontenera IOC tapestry o istnieniu naszego service (w springu zwanego beanem, co BTW jest dla mnie najbardziej denerwującą rzeczą w całym springu).<br />
Serwisy w tapestry standardowo składają się z interfejsu oraz jego implementacji, choć pierwsze może zostać pominięte (niezalecane). Nazwa powinna być znana użytkownikom HiveMind (który był stworzony na potrzeby poprzedniego tapestry).<br />
Powiązanie pomiędzy implementacją a interfejsem tworzone jest po .class, dzięki czemu możemy refaktoryzować nasz kod w dowolnym IDE, a i tak będzie nas ono wspierało. Powiązania są również wyszukiwalne, choć oba punkty stają się coraz mniej aktualne wraz z wzrastającym wsparciem  wszystkich IDE dla Springa. Wspominam o tym jednak, gdyż w dawnych czasach, zdarzyło mi się pomagać koledze utrzymującemu naszą aplikację, znaleźć błąd w mojej klasie. Po dłuższej chwili okazało się, że nastąpiły zmiany w klasach, które nie zostały odzwierciedlone w plikach XML.</p>
<p>Druga metoda dokłada się do domyślnych ustawień aplikacji, co to dokładnie oznacza wyjaśnię w jednym z przyszłych wpisów. Do metody tapestry dostarczy nam obiektu, który jest taką a'la mapą do której można tylko wkładać. Klucz odpowiada za nazwę opcji a wszystkie opcje można znaleźć w obiekcie SymbolConstatns. Opisy poszczególnych ustawień można znaleźć <a title="Tapestry user guide" href="http://tapestry.apache.org/configuration.html" target="_blank">na stronach tapestry</a>.<br />
My dodamy jedno od siebie <em>configuration.add(SymbolConstants.COMPRESS_WHITESPACE, "false");</em><br />
Dzięki temu łatwiej się developuje (przynajmniej mi) bo więcej widać. Co to zmienia? Przed zmianą podejrzyjcie dowolną stronę wygenerowaną przez T5, a następnie tę samą stronę po zmianie (w przeglądarce korzystamy z opcji source/pokaż źródła ctrl+u). </p>
<p>I wszystko jasne.  Tapestry kompresuje mnóstwo rzeczy by do klienta szło jak najmniej danych. Domyślnie tapestry Gzipuje niektóre dane (o czym za chwilę poniżej) oraz wycina zbędne białe znaki (co właśnie zmieniliśmy). Domyślny rozmiar danych by zostały skompresowane to 100 bajtów, ale da się limit ten zmienić w sposób analogiczny do kompresji białych znaków.</p>
<p>Ciekawostka. Opcja Application_Version jest przydatna :) Na czas developmentu opcję tę kasujemy, przywrócimy ją dopiero jak aplikacja pójdzie live. Dlaczego? Odpowiada ona za przygotowywanie tzw. Assetów (czyli wszystkiego co może zostać wysłane do przeglądarki). Przygotowanie polega na wkomponowaniu w adres URI zasobów numeru wersji. Dzięki temu przeglądarka może cachować po stronie klienta zasoby i pobierać je ponownie dopiero gdy zmieni się numer wersji. Gdy nie podamy tej opcji w ogóle to za każdym uruchomieniem generowany jest losowy numer, co pozwala nam uniknąć ręcznego czyszczenia cachu by zobaczyć wyniki naszych zmian.</p>
<p>To na dziś tyle. Może krótko, ale jak wspominałem sam mam braki w temacie. Jednak pomimo tego czułem potrzebę omówienia zagadnienia konfiguracji. Przepraszam również, że wpis ukazuje się tak późno. Był gotów w zeszłą niedzielę, ale po windows updatcie i pewnej dozie nerwów z tym związanych, postanowiłem go opublikować w poniedziałek. Naiwny, nie widziałem wtedy jeszcze, że czeka mnie piekielny tydzień.<br />
Postaram się nadrobić tekstem jutro :)<br />
Do przeczytania</p>
