# Umowa licencyjna użytkownika końcowego (EULA) Strażnica SOC

**Wersja 1.0 z dnia 14-10-2026**

## 1. Strony i definicje

1. **Licencjodawca** — Piotr Pęsiek kontakt: podatnosci@wp.pl.
2. **Licencjobiorca** — osoba fizyczna prowadząca działalność gospodarczą, osoba prawna lub jednostka organizacyjna, która instaluje lub używa Oprogramowania.
3. **Oprogramowanie** — program Strażnica SOC (serwer, agenci, konsola, konsola offline) wraz z dokumentacją i aktualizacjami.
4. **Wersja próbna** — Oprogramowanie używane bez klucza licencyjnego, przez okres określony w pkt 3.
5. **Klucz licencyjny** — ciąg znaków wydany przez Licencjodawcę, określający zakres licencji (liczba hostów, liczba klientów, okres ważności).
6. **Host** — komputer lub serwer, z którego dane przesyła agent Oprogramowania.

## 2. Akceptacja

Instalując lub używając Oprogramowania, Licencjobiorca akceptuje niniejszą Umowę. Jeśli jej nie akceptuje, nie może instalować ani używać Oprogramowania.

## 3. Wersja próbna

1. Licencjodawca udziela nieodpłatnej, niewyłącznej, nieprzenoszalnej licencji na używanie Oprogramowania przez **30 dni** od pierwszego uruchomienia serwera, w zakresie do 25 hostów i 2 dodatkowych klientów, wyłącznie w celu oceny Oprogramowania.
2. Wersja próbna jest dostarczana **„w stanie, w jakim jest”**, bez gwarancji i bez wsparcia technicznego. Licencjodawca może udzielić wsparcia według własnego uznania.
3. Wersja próbna nie powinna być jedynym środkiem ochrony systemów produkcyjnych ani jedynym narzędziem do spełnienia obowiązków prawnych (w tym wynikających z ustawy o krajowym systemie cyberbezpieczeństwa).
4. Po upływie okresu próbnego konsola przechodzi w tryb tylko do odczytu. Dalsze używanie wymaga zakupu licencji.

## 4. Licencja komercyjna

1. Po opłaceniu wynagrodzenia Licencjodawca udziela niewyłącznej, nieprzenoszalnej licencji na używanie Oprogramowania w zakresie określonym w Kluczu licencyjnym i zamówieniu, na terytorium [Rzeczypospolitej Polskiej / całego świata].
2. Pola eksploatacji: instalowanie i uruchamianie Oprogramowania na urządzeniach Licencjobiorcy, w liczbie hostów nieprzekraczającej limitu, oraz sporządzanie kopii zapasowej.
3. Licencja terminowa obowiązuje przez okres wskazany w Kluczu licencyjnym. Po jego upływie obowiązuje 14-dniowy okres karencji, a następnie konsola przechodzi w tryb tylko do odczytu; zbieranie logów i wykrywanie zagrożeń nie są wyłączane.
4. Licencja partnerska (NFR) służy wyłącznie do testów, demonstracji i szkoleń prowadzonych przez partnera Licencjodawcy.
5. Świadczenie usług monitoringu na rzecz osób trzecich (tryb wielu klientów) jest dozwolone wyłącznie na podstawie licencji obejmującej dodatkowych klientów.

## 5. Ograniczenia

Licencjobiorca nie może, chyba że bezwzględnie obowiązujące przepisy stanowią inaczej:

1. dekompilować, deasemblować ani w inny sposób odtwarzać kodu źródłowego Oprogramowania (z zastrzeżeniem art. 75 ust. 2 pkt 3 ustawy o prawie autorskim i prawach pokrewnych);
2. omijać, wyłączać ani modyfikować mechanizmów licencyjnych, w tym limitów hostów i okresu ważności;
3. udostępniać, odsprzedawać, wynajmować ani użyczać Oprogramowania lub Klucza licencyjnego osobom trzecim;
4. usuwać oznaczeń autorskich i informacji o licencjach komponentów.

## 6. Komponenty open source

Oprogramowanie zawiera komponenty open source (m.in. Node.js, licencja MIT, fonty IBM Plex, SIL OFL, bazy geolokalizacji i reguły Sigma na własnych licencjach). Ich licencje są wymienione w pliku `SKLADNIKI.txt` i mają pierwszeństwo przed niniejszą Umową w zakresie tych komponentów.

## 7. Dane i prywatność

1. Oprogramowanie działa w infrastrukturze Licencjobiorcy. Logi i dane o incydentach **nie są przesyłane do Licencjodawcy**.
2. Oprogramowanie łączy się z usługami zewnętrznymi (np. reputacja adresów IP, wskaźniki zagrożeń, chmura Microsoft, Google, AWS) wyłącznie wtedy, gdy Licencjobiorca je skonfiguruje.
3. Licencjobiorca jest administratorem danych osobowych przetwarzanych w Oprogramowaniu (np. loginy, adresy IP) i odpowiada za podstawę prawną ich przetwarzania oraz okres przechowywania.
4. Jeśli Licencjodawca świadczy usługi wymagające dostępu do danych Licencjobiorcy (wdrożenie, wsparcie, monitoring), strony zawrą odrębną umowę powierzenia przetwarzania danych.

## 8. Gwarancja i odpowiedzialność

1. Licencjodawca dokłada starań, aby Oprogramowanie działało zgodnie z dokumentacją, lecz **nie gwarantuje wykrycia każdego zagrożenia ani incydentu**. Wykrywanie i analiza przyczyn mają charakter pomocniczy i wymagają oceny przez człowieka.
2. Formularze zgłoszeń (NIS2/KSC, UODO, DORA) są pomocą w przygotowaniu zgłoszenia; za treść i terminowość zgłoszenia odpowiada Licencjobiorca.
3. Akcje reakcji (blokady, odcięcie kont, izolacja komputerów) są wykonywane po zatwierdzeniu przez użytkownika Licencjobiorcy, który odpowiada za ich skutki.
4. Odpowiedzialność Licencjodawcy jest ograniczona do szkody rzeczywistej i do kwoty wynagrodzenia zapłaconego za licencję w okresie 12 miesięcy poprzedzających zdarzenie. Licencjodawca nie odpowiada za utracone korzyści, utratę danych ani szkody pośrednie. Ograniczenia nie dotyczą szkód wyrządzonych umyślnie.
5. W zakresie dopuszczalnym przez prawo wyłącza się rękojmię za wady Oprogramowania.

## 9. Wsparcie i aktualizacje

Zakres wsparcia, czas reakcji i dostęp do aktualizacji określa zamówienie lub odrębna umowa. Data końca wsparcia jest zapisana w Kluczu licencyjnym.

## 10. Rozwiązanie

Licencjodawca może wypowiedzieć licencję ze skutkiem natychmiastowym w razie naruszenia pkt 5. Po rozwiązaniu Licencjobiorca zaprzestaje używania Oprogramowania; dane zebrane przez Oprogramowanie pozostają własnością Licencjobiorcy.

## 11. Postanowienia końcowe

1. Prawem właściwym jest prawo polskie. Spory rozstrzyga sąd właściwy dla siedziby Licencjodawcy.
2. Majątkowe prawa autorskie do Oprogramowania przysługują Licencjodawcy. Umowa nie przenosi tych praw.
3. Jeśli którekolwiek postanowienie okaże się nieważne, pozostałe pozostają w mocy.

© 2026 Piotr Pęsiek. Wszelkie prawa zastrzeżone.
