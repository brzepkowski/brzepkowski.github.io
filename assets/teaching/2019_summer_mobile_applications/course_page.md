---
layout: page
title: Aplikacje mobilne
---
Główny prowadzący kurs: dr inż. Marcin Zawada


### Zasady zaliczenia kursu:
- Ocena z laboratorium bierze pod uwagę umiejętności nabyte w trakcie kursu oraz terminowość oddawania zadań
- Rozwiązania zadań należy wysyłać w terminie na przydzielone konto SVN
- Rozwiązania zadań wysłane po danym terminie, ale do 1 tygodnia liczone są maks. za połowę punktów, po terminie 1 tygodnia liczone są za ZERO punktów
- Wysłane rozwiązania, należy oddać na laboratorium, bez straty punktów na pierwszych lub drugich zajęciach po terminie SVN, na trzecich zajęciach (bez usprawiedliwień) zadania liczone są maks. za połowę punktów na czwartych zajęciach zadania liczone są za ZERO punktów (nawet zadania wysłane w terminie na SVN)

### Projekt
- Ocena z projektu wystawiona na podstawie jakości rozwiązania problemu oraz przejrzystości kodu znajomości (wszystkich) tematów przedstawionych na wykładzie.

### Ocena z list zadań:
- ocena 5.5 >=50pt
- ocena 5.0 >=41pt <50pt
- ocena 4.5 >=36pt <41pt
- ocena 4.0 >=31pt <36pt
- ocena 3.5 >=26pt <31pt
- ocena 3.0 >=21pt <26pt

### Ocena końcowa:
- Ocena końcowa = 0.5 * ocena z list zadań + 0.5 * min(5.0,ocena z projektu), przy czym obie oceny cząstkowe (listy zadań oraz projekt) muszą być co najmniej dst!


# Wykłady

1. Wykład 1.03.2019
    - Historia systemów mobilnych
    - Tworzenie aplikacji pod system Android wykorzystując dostępne SDK
    - Tworzenie projektu Android Studio
    - Uruchomienie aplikacji na urządzeniu i/lub emulatorze
1. Wykład 8.03.2019
    - Podstawowe informacje o komponentach aplikacji: Activity, Intent oraz Service
    - Manifest aplikacji
    - Podstawowe zasoby i dostęp do zasobów
    - Interfejs użytkownika np. LinearLayout, TableLayout, Buttons, EditText
    - Budowanie prostej aplikacji
    - Projektowanie aplikacji dla różnych wielkości ekranu, wspieranie różnych wielkości ekranu
    - Kotlin Android Extensions - View Binding
1. Wykład 15.03.2019 kod źródłowy z wykładu
    - ListView, ListView (Kotlin), ListView (Java)
    - Zasoby
    - Log i Logcat
1. Wykład 22.03.2019 kod źródłowy z wykładu
    - Cykl życia aktywności
    - Przełączanie oraz przekazywanie danych między aktywnościami przez intencje
1. Wykład 29.03.2019 kod źródłowy z wykładu
    - Fragmenty link1 link2
1. Wykład 5.04.2019 kod źródłowy z wykładu
    - Layout Inflater
    - Picasso, Glide - biblioteka do pobierania i obróbki obrazów
    - Android View Animations - biblioteka do animacji widoków
    - Android Bootstrap - biblioteka dostarczająca widoki i style, stosuje się do specyfikacji Twitter Bootstrap Specification.
    - Ion - biblioteka ułatwiająca pobieranie plików z sieci
    - SwipeStack, Swipe Layout, ... - biblioteki przesuwania widoków
    - Lista różnych bibliotek dla Androida
1. Wykład 12.04.2019 kod źródłowy z wykładu
    - Szybki sposób na zachowanie stanu aktywności np. po rotacji ekranu
    - Odtwarzanie stanu aktywności
    - Przechowywanie małej kolekcji klucz-wartość: SharedPreferences
    - Przechowywanie danych w plikach na wewnętrznym lub zewnętrznym nośniku
    - Grafika 2D: View, runOnUiThread
1. Wykład 16.04.2019 kod źródłowy z wykładu
    - Grafika 2D: SurfaceView
1. Wykład 26.04.2019 kod źródłowy z wykładu
    - GSON - biblioteka do zamiany JSON na obiekty
    - Retrofit - biblioteka do zamiany REST API na metody
    - RESTful API
        - Chuck Norris, The Cat - proste przykłady
        - Twitter
        - Instagram
        - RapidAPI
1. Wykład 10.05.2019 kod źródłowy z wykładu
    - Relacyjne bazy danych
    - SQLite
    - Room, Room DAO
    - adb shell
1. Wykład 17.05.2019 kod źródłowy z wykładu
    - Bazy NoSQL
    - Firebase
        - Add Firebase to your Android project
        - Sign in with pre-built UI
        - Authenticate with Firebase Using Email Link in Android
        - Real-time database (Android)
        - Cloud Firestore
1. Wykład 24.05.2019 kod źródłowy z wykładu
    - Services
    - Notification
1. Wykład 31.05.2019 kod źródłowy z wykładu
    - Google Maps
        - Google Maps for Android
        - Location Data
    - Biblioteka Qt
        - QObject, Q_OBJECT, QMetaObject, QApplication
        - Sygnały i sloty
        - Przykłady


### Projekt - Aplikacja w systemie mobilnym Android

- Od 1.04.2019, każdy wybiera projekt który będzie realizował
- Projekt jest realizowany w grupach >=4, <=6 osobowych (czyli 4 osoby, 5 osób lub 6 osób)
- Zakres projektu musi wykorzystywać w pełni materiał przedstawiony na co najmniej 5 wykładach (z wyjątkiem wykładu 1)
- Projekt musi zostać zatwierdzony przez osobę prowadzącą laboratorium
- Na każdym laboratorium po wybraniu projektu co najmniej jedna osoba z grupy przedstawia postępy projektu
- Projekt należy wybrać najpóźniej do 12.04.2019 (przy braku wyboru grupy nastąpi losowy przydział)
- Do zarządzania projektem najlepiej skorzystać z github, gitlab, bitbucket, ... i proszę wysłać prowadzącemu link do projektu grupy. Przydatny będzie pewnie także np. slack
- Na realizację projektu i wysłanie na SVN (każda osoba wysyła tylko końcową wersję projektu na przydzielone konto SVN, tak każda osoba z grupy wysyła ten sam projekt na swoje konto SVN) jest czas do 26.05.2019
- Przy oddawaniu projektu wszyscy z grupy znają cały projekt i potrafią odpowiedzieć na dowolne pytania z zakresu wykładów

# Laboratorium

### Lista 0 (Lab)

1. Zainstaluj język Kotlin oraz uruchom i przeanalizuj kilka przykładów ze strony link.
```
    $ sudo pacman -S kotlin
    $ cat << EOF >hello.kt
    fun main(args: Array<String>) {
        println("Hello, World!")
    }
    EOF
    $ kotlinc hello.kt -include-runtime -d hello.jar
    $ java -jar hello.jar

    # uruchomienie REPL
    $ kotlinc-jvm
    Welcome to Kotlin version 1.3.21 (JRE 1.8.0_202-b26)
    Type :help for help, :quit for quit
    >>> 2+2
    res0: kotlin.Int = 4
    >>>
```
1. Zainstaluj Android Studio. Uruchom przykładowe "Hello World" w emulatorze i/lub na urządzeniu z Androidem (telefon, tablet).

### Lista 1 (Lab) Termin wysłania na SVN do 10.03.2019 (nieobowiązkowe wysłanie na SVN)

Celem tej listy jest wstępne opanowanie pisania aplikacji dla systemu Android z wykorzystaniem Android Studio (język Kotlin)

1. (2pt) Wykorzystując pliki z klasami i zasobami utwórz nowy projekt oraz przekopiuj je do odpowiednich katalogów i uruchom przykładową grę napisaną na pierwszym wykładzie.
1. (3pt) W tym zadaniu jest duża możliwość wyboru. Proszę napisać dowolną aplikację w systemie Android z następującymi ograniczeniami:
    - aplikacja zawiera co najmniej cztery widgety na ekranie co najmniej dwóch rodzajów np. TextView, EditView
    - aplikacja powinna odpowiadać na co najmniej dwa zdarzenia np. dwa przyciski
    - aplikacja powinna zmieniać dowolny aspekt wyglądu np. textview ma większe i grubsze czcionki
    - aplikacja powinna dostosowywać się do wielkość ekranu (różne telefony i rozdzielczości)
    - nie może to być aplikacja z zadania 1
    Sugestie:
        - Zgadywanie liczby. Komputer losuje liczbę np. 1 do 100, a użytkownik próbuje zgadywać liczbę z podpowiedziami komputera, czy liczba jest większa czy mniejsza od aktualnie podanej.
        - Zgadywanie słowa. Komputer podaje słowo (na początku zasłonięte), a użytkownik próbuje zgadnąć jakie to słowo w najmniejszej liczbę odsłanianych liter.
        - Papier-kamień-nożyczki. Gra z komputerem (który wybiera losowo). Śledź rozgrywkę człowieka i komputera podając kto wygrał ile partii.

### Lista 2 (Lab) Termin wysłania na SVN do 24.03.2019

Celem tej listy jest opanowanie bardziej zaawansowanych układów graficznych (niż te z poprzedniej listy) oraz wykorzystanie większej liczby widgetów oraz widoków w interfejsie graficznym użytkownika.

1. (3pt lub 4pt) Napisz grę w kółko i krzyżyk, gdzie dwóch graczy na zmianę naciska przyciski w siatce 5x5 oznaczone odpowiednio przez "X" lub "O". Jeśli, któryś z graczy ułoży swoje litery pionowo, poziomo lub po przekątnej to wygrywa. Wykorzystaj odpowiedni Layout do rozłożenia przycisków oraz zrób tak aby przyciski wypełniały odpowiednio ekran i czcionka była odpowiednio duża (możesz to przetestować na emulatorze dla różnych rozdzielczości ekranów). Prostsza wersja, gry jest taka, że gracze są ludźmi i grają na zmianę (3pt). W bardziej zaawansowanej wersji zrób, aby można było grać z komputerem np. wymyślając jakąś strategie która będzie naśladowała zachowanie drugiego gracza (4pt).
1. (3pt) Napisz aplikację "Wisielec", która wyświetla po kolei obrazek wisielca oraz słowo które gracz próbuje zgadnąć. Słowo wybierane jest losowo z dostępnego słownika. Oczywiście cały czas wyświetlane jest słowo z prawidłowo zgadniętymi literami np. dla słowa komputer jeśli gracz zgadł prawidłowo litery o, m i e, słowo będzie wyglądało mniej więcej tak ?om???e?. Do utworzenia obrazków wykorzystaj np. program GIMP nazwij je odpowiednio wisielec0.png, wisielec1.png i tak dalej. Do wyświetlania obrazków wykorzystaj ImageView. Do przechowywania słownika wykorzystaj plik strings.xml.

```
    <string-array name="words">
        <item>komputer</item>
        <item>zgadywanka</item>
        ...
    </string-array>
```

Jak pobierać zasoby zobacz link. Słownik możesz wypełnić dowolnymi słowami np. ściągniętymi z internetu przez prosty skrypt.

### Lista 3 (Lab) Termin wysłania na SVN do 7.04.2019

1. (5-10pt) Napisz prostą aplikacje "To-Do", czyli listę zadań do zrobienia. Aplikacja zawiera ListView z potencjalną listą zadań. Na początku lista jest pusta, ale na dole aktywności znajduje się np. EditText oraz Button (Dodaj), gdzie użytkownik może wprowadzić opis nowego zadania. Dalej mamy możliwość wyboru ikonki zadania (obrazek) oraz czas wykonania zadania. Zaprojektuj odpowiedni layout pojedynczej pozycji ListView, który zawiera co najmniej obrazek, treść zadania oraz czas wykonania. Aplikacja powinna też mieć możliwość usuwania zadań z listy, przez dołączenie słuchacza do listy, który usuwa wybraną pozycję. Najlepiej wykorzystać do tego long click (metoda setOnItemLongClickListener). Zrób też tak, aby aplikacja pamiętała listę zadań po rotacji ekranu, czyli po zmianie z portrait na landscape i odwrotnie.

**Uwaga:** W tym zadaniu aplikacja powinna być w pełni funkcjonalna. Uwzględniana będzie też wygoda i użyteczność aplikacji. Możliwe są do wprowadzenia dodatkowe funkcje, za które można otrzymać dodatkowe punkty np.

- sortowanie zadań po czasie
- ustalenie priorytetów zadań
- sortowanie po ikonce (typ zadania)
- ...

### Lista 4 (Lab) Termin wysłania na SVN do 23.04.2019

Celem tej listy jest napisanie aplikacji, która ma wiele aktywności, prawidłowo obsługuje stany aplikacji onPause, onStop, itp. przechowuje dane i poprawnie wykorzystuje intencje (intent).

1. (5pt) Napisz aplikacje (galeria) przechowującą zdjęcia np. ludzi, krajobrazów, zwierząt, ... i każde zdjęcie dodatkowo zawiera krótki opis. Po uruchomieniu aplikacji, na początku pokazuje ona dostępne zdjęcia. Użytkownik może wybrać dowolną pozycję, aby zobaczyć większe zdjęcie i opis. Na ekranie dodatkowo, mamy możliwość ocenienia zdjęcia przez np. "gwiazdki" (zobacz RatingBar ). Proszę pamiętać, że na tym etapie poznania Androida nie ma to być w pełni funkcjonalna aplikacja np. nie potrzeba tworzyć kont dla użytkowników lub nie potrzeba przechowywać nowych zdjęć. Aplikacja powinna natomiast obsługiwać:
    - co najmniej dwie aktywności
    - przekazywać informacje z jednej aktywności do drugiej wykorzystując intencje
    - druga aktywność powinna wracać informacje do pierwszej o liczbie gwiazdek, po czym w pierwszej aktywności obrazki zostają odpowiednio posortowane po liczbie gwiazdek
    - poprawnie obsługiwać cykl życia aktywności tzn. onCreate, onStart, onResume, onPause, onStop, onDestroy, ... (te które są potrzebne)
    - wykorzystywać fragmenty przy zmianie orientacji ekranu
    - zapamiętywać swój stan po zmianie orientacji ekranu
1. \*(5pt) Uzupełnij poprzednie zadanie o możliwość robienia zdjęć (obsługa Camera ) i dodawania do kolekcji.

### Lista 5 (Lab) Termin wysłania na SVN do 5.05.2019

Celem tej listy jest napisanie aplikacji, która umożliwia prostą animacje oraz na stałe przechowuje dane.

1. (5pt) Napisz prostą dwuwymiarową grę w tenisa Pong lub Arkanoid. Wykorzystaj własne View które będzie odpowiedzialne za odświeżanie gry (rysowanie kolejnych klatek animacji gry w onDraw). Do pobierania zdarzeń wykorzystaj metodę np. onTouchEvent. Wszystkie informacje o stanie gry mają być przechowywane w SharedPreferences.

### Lista 6 (Lab) Termin wysłania na SVN do 19.05.2019

1. (5pt) Napisz aplikacje wykorzystującą REST API do prostych obliczeń matematycznych Newton API. W implementacji wykorzystaj bibliotekę Retrofit. Napisz prosty interfejs do wprowadzania podstawowych funkcji np. w EditText wprowadzamy wyrażenie i na dole mamy Buttony które odpowiadają wszystkim dostępnym operacją simplify, factor, derive, ... po czym dostajemy wynik obliczeń.

```
    # Lokalna instalacja Newton API (jeśli chcemy pracować i/lub testować lokalnie)

    $ sudo pacman -S nodejs npm
    $ git clone https://github.com/aunyks/newton-api.git
    $ cd newton-api
    $ npm install --no-audit
    $ node app.js

    # Mamy serwer na http://localhost:3000
```

### Lista 7 (Lab) Termin wysłania na SVN do 2.06.2019

1. (3pt) Dopisz funkcjonalność przechowywania danych do aplikacji z Listy 3 (To-Do). Do przechowywania danych wykorzystaj SQLite oraz Room. Aplikacja powinna mieć podstawowe możliwości wyszukiwania, dodawania oraz usuwania zadań.
1. (5pt) Dopisz funkcjonalność "gry przez sieć" do aplikacji z Listy 2 - kółko i krzyżyk. Wykorzystaj funkcjonalność bazy danych czasu rzeczywistego Firebase. Na początku mamy możliwość rejestracji lub jeśli mamy już konto to logowania (również z wykorzystaniem Firebase). Po czym możemy wybrać osobę do gry (która jest też zalogowana do systemu). Następnie prowadzimy grę na zmianę przez sieć. Wystarczy tutaj klasyczna rozgrywka na planszy 3x3.

### Lista dodatkowa (Lab) Termin wysłania na SVN oraz oddania do 18.06.2019

1. (5pt) Dopisz funkcjonalność powiadamiania o nadchodzącym zadaniu w aplikacji z Listy 7 (To-Do z bazą SQLite w Room). Powiadomienia mają się pojawiać nawet po zamknięciu aplikacji (swipe) z menu recent apps (czyli nawet po onDestroy).
1. (5pt) Napisz prostą aplikację wykorzystującą serwis Google Maps. Aplikacja powinna zawierać zdefiniowane w bazie danych Firebase nazwy miast razem ze współrzędnymi geograficznymi. Dodaj menu (np. Spinner), gdzie użytkownik może wybierać miasta po nazwach i kamera przesunie się na odpowiednią pozycję. Dodaj możliwość dodawania miast do bazy. Wtedy dodane miasta widzą wszyscy użytkownicy aplikacji (nie potrzeba logowania). Dodatkowo zrób, tak aby użytkownik mógł rysować linie między miastami i kiedy wciśnie przycisk "Kasuj", to ścieżka zostanie wykasowana.
