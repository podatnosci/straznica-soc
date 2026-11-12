# Historia zmian

## 2.9.0 2026-11-12

**AI w SOC.** AI proponuje, a deterministyczne testy i człowiek zatwierdzają. Model nie ma żadnych uprawnień do działania: nie zamyka alertów i nie zapisuje wyjątków ani reguł. Asystent AI jest domyślnie wyłączony. Działa z dowolnym punktem końcowym zgodnym z OpenAI (Ollama, LM Studio, vLLM lokalnie albo usługa chmurowa). Dane są domyślnie pseudonimizowane.

**Nowe**

- **Odporność na wstrzykiwanie poleceń (prompt injection).** Logi, wiadomości i pliki trafiają do modelu wyłącznie jako niezaufane dane:
  - dane są ogrodzone losowym znacznikiem, a znaczniki ról (`<|im_start|>`, `[INST]` itp.) są neutralizowane;
  - prompt systemowy zabrania wykonywania poleceń z danych;
  - odpowiedzi muszą mieć ścisły format JSON ze schematem, z jedną automatyczną próbą naprawy formatu.
  Dotyczy to także dotychczasowych zadań asystenta (podsumowanie, kroki, zgłoszenie CSIRT).
- **Reguła AI-01.** Wykrywa polecenia skierowane do modelu AI w polach zdarzeń: User-Agent, temat wiadomości, nazwa pliku, wiersz poleceń, URL itd., po polsku i po angielsku. Ważność 4, gdy polecenie próbuje zmienić ocenę alertu („mark as benign”, „oznacz jako bezpieczne”).
- **Ocena alertu przez AI.** Dla alertu AI podaje:
  - werdykt i pewność;
  - dowody za zagrożeniem i za fałszywym alarmem;
  - trzy kroki sprawdzenia.
  Gdy w danych jest próba wstrzyknięcia, ocena nie może zostać obniżona do „fałszywy alarm” — zostaje podniesiona do „niejasne”, z ostrzeżeniem. Analityk klika „zgadzam się” lub „nie zgadzam się”. Zgodność liczy się też ze statusu alertu (fałszywy alarm albo incydent) i trafia do raportu dla zarządu.
- **Doradca strojenia (Ustawienia → Test reguł).** AI proponuje wąskie wyjątki na podstawie najgłośniejszych reguł z 30 dni i alertów oznaczonych jako fałszywe.
  - Każda propozycja przechodzi walidację: konkretna reguła, konkretna wartość, zakres nie szerszy niż /16, uzasadnienie.
  - Następnie przechodzi test wykrywania z tą propozycją. Propozycja, która ukryłaby scenariusz ataku, jest blokowana i nie da się jej zatwierdzić.
  - Wyjątek powstaje dopiero po zatwierdzeniu przez człowieka, z adnotacją „Propozycja AI zatwierdzona przez …” i terminem ważności.
- **Reguła Sigma z opisu.** AI pisze regułę i przypadki testowe. Strażnica:
  - uruchamia przypadki (wymagany co najmniej jeden pozytywny i jeden negatywny);
  - liczy dopasowania w zdarzeniach z 30 dni;
  - zapisuje regułę jako WYŁĄCZONĄ, z tagiem `straznica.ai_generated`.
  Regułę włącza człowiek w Regułach.
- **Projekty zgłoszeń NIS2** (wczesne ostrzeżenie w 24 h, zgłoszenie w 72 h, raport końcowy) i **informacja dla zarządu**. Znajdziesz je w nowej zakładce incydentu „AI: NIS2 i pytania”. Pola nieustalone są oznaczane [DO UZUPEŁNIENIA].
- **Pytania do sprawy.** Odpowiedzi wyłącznie na podstawie danych incydentu (alerty, zdarzenia, notatki), z odnośnikami [A1], [Z3], [N2] rozwijanymi do źródeł. Odpowiedź bez poprawnych źródeł jest zastępowana przez „Nie wiem”.
- **Analiza phishingu.** Obejmuje polskie schematy: zmiana numeru rachunku, „prezes prosi o przelew”, dopłata do przesyłki, e-Urząd Skarbowy.
  - Wskaźniki IOC są ograniczone do tych, które dosłownie występują w wiadomości.
  - Wiadomość z poleceniem dla AI nie może zostać uznana za prawidłową.
  - Wskaźniki można dodać do listy IOC jednym kliknięciem.
- **„Co z dzisiejszych komunikatów nas dotyczy”.** AI porównuje komunikaty CERT i producentów oraz nowe wpisy CISA KEV z listą zasobów i otwartymi podatnościami. „Dotyczy nas” zostaje tylko wtedy, gdy zasób lub CVE rzeczywiście występuje w organizacji; w pozostałych przypadkach ocena jest obniżana do „może dotyczyć”.
- **Reguła ML-01 — nietypowy wiersz poleceń.** Lokalny model statystyczny (n-gramy znakowe), bez modelu językowego; dane nie opuszczają serwera.
  - Model jest uczony codziennie na 30 dniach zdarzeń procesów.
  - Wskazuje nowe lub rzadkie programy, rzadkie fragmenty, nietypową długość i bloki base64.
  - Nowy program uruchomiony z katalogu zapisywalnego przez użytkownika ma niższy próg.
  - Okno uczenia i próg ustawisz w Ustawienia → AI w SOC.
- **Dziennik AI.** Każde wywołanie modelu jest zapisywane: kto, kiedy, jakie zadanie, ile znaków wysłano, czy dane były pseudonimizowane, czy wykryto wstrzyknięcie, decyzja człowieka. Retencja 2 lata. Wywołania i decyzje trafiają też do dziennika audytu.
- **Ustawienia → AI w SOC.** Zakładka zawiera:
  - wskaźniki;
  - kartę kompetencji AI dla analityka (AI Act art. 4) z potwierdzeniem zapoznania;
  - phishing, komunikaty i regułę z opisu;
  - model ML-01;
  - dziennik AI.
  Każdy wynik jest oznaczony „Wygenerowane przez AI — sprawdź przed decyzją” (AI Act art. 50).
- **Raport kwartalny dla zarządu — sekcja „AI w SOC”.** Zawiera:
  - liczbę wywołań;
  - zgodność analityków z oceną AI;
  - propozycje strojenia (zatwierdzone, zablokowane);
  - próby wstrzyknięcia (AI-01) i nietypowe polecenia (ML-01);
  - zalecenia przy niskiej zgodności albo po próbach manipulacji.
- **Test wykrywania: scenariusz nr 17 „ai-inject”** — polecenie dla modelu AI ukryte w nagłówku User-Agent (T1027).

**Zmiany**

- Reguły Sigma: pola `Image` i `ParentImage` dla zdarzeń 4688 są odczytywane także z treści komunikatu (eksporty CSV/tekst bez pól strukturalnych).
- API: `/api/ai29/*` (triage, tune, rule, phish, digest, ask, ml, log). Zadania `nis2_early`, `nis2_notif`, `nis2_final` i `board` są dostępne w `/api/ai/run`.

## 2.8.2 2026-11-09

**Nowe**

- Raport kwartalny dla zarządu: sekcja „Strojenie a skuteczność wykrywania” z porównaniem do poprzedniego okresu — liczba zmian strojenia (wyjątki, progi, wyłączenia, reguły), uruchomione testy wykrywania (w tym automatyczne), odsetek testów z pełnym wykryciem, wynik na koniec okresu, alerty odfiltrowane strojeniem i ograniczenie szumu, wykryte regresje z medianą czasu naprawy, liczba świadomie zaakceptowanych luk.
- Zdanie do slajdu, budowane wyłącznie z danych, np. „SOC zmniejszył szum o 38% i nie stracił żadnego podstawowego wykrycia — po 47 zmianach strojenia wszystkie 16 podstawowych technik ataku nadal są wykrywane, a jedną regresję wyłapano i naprawiono w 6 min”. Gdy są zaakceptowane luki, otwarte regresje albo brak testów, zdanie mówi to wprost i nie twierdzi, że wykrywanie jest pełne. Widoczne także w Ustawienia → Zarząd z przyciskiem „Kopiuj”.
- Lista świadomie zaakceptowanych luk w wykrywaniu: scenariusz i technika ATT&CK, kto i kiedy zaakceptował, uzasadnienie, przyczyna oraz czy luka nadal istnieje. Rekomendacja dla zarządu, gdy luki pozostają lub regresje są otwarte.
- Rejestr regresji wykrywania: czas wykrycia, przyczyna, autor zmiany, sposób zamknięcia (cofnięcie z testu, późniejsza zmiana, akceptacja z uzasadnieniem) i czas do zamknięcia.
- Historia testów wykrywania w osobnej tabeli z retencją 2 lata (wcześniej 30 ostatnich przebiegów) — pozwala zestawiać kwartały. Historia z 2.8.1 jest przenoszona automatycznie.
- Dzienny licznik alertów odfiltrowanych wyjątkami (podstawa wskaźnika ograniczenia szumu; zliczany od 2.8.2 — dla wcześniejszych okresów zdanie do slajdu porównuje liczbę alertów kwartał do kwartału).

## 2.8.1 2026-11-09

**Nowe**

- Automatyczny test wykrywania: uruchamia się sam kilka sekund po każdej zmianie wyjątków alertów, progów, wyłączeń reguł, whitelisty, reguł własnych i Sigma (seria zmian daje jeden przebieg), a także raz na dobę. Bez zmian konfiguracji test się nie powtarza.
- Alert RT-01: scenariusz, który był wykrywany, a po zmianie przestał być wykrywany, tworzy alert (powiadomienia jak dla innych alertów) z przyczyną, listą zmian od poprzedniego testu i ich autorami z dziennika audytu. Ważność wysoka, krytyczna przy utracie wykrywania m.in. zrzutu LSASS, usunięcia kopii cieni, dodania do administratorów, czyszczenia dziennika. Alert zamyka się sam, gdy wykrywanie wróci.
- Cofnięcie zmiany jednym kliknięciem z zakładki Test reguł: wyłączenie wyjątku, ponowne włączenie reguły albo przywrócenie ustawień detekcji z ostatniego udanego testu (z listą zmienianych ustawień). Zmianę zamierzoną można zaakceptować z uzasadnieniem — obecny stan staje się punktem odniesienia.
- Nowe scenariusze testu (16 zamiast 13): dodanie konta do grupy Domain Admins (4728, wymagana ważność co najmniej wysoka), utworzenie konta w domenie (4720), blokada konta (4740).
- Historia testów (ostatnie 30 przebiegów: wyzwalacz, autor zmian, wynik, regresje, alert) i ustawienia testu automatycznego.

**Poprawki**

- Test wykrywania uwzględnia wyjątki alertów (2.8.0 je pomijał, więc wyjątek wyciszający całą regułę dawał nadal 100%). Przyczyna „ukryte wyjątkiem” wskazuje konkretny wyjątek; test nie zwiększa licznika trafień wyjątku.
- Alerty RT-01 nie podlegają wyjątkom i nie wpływają na ocenę ryzyka.

## 2.8.0 2026-11-08

**Nowe**

- Zarządzanie podatnościami (nowa zakładka „Podatności”): rejestr ustaleń z Nessus, OpenVAS i Nmap, katalog CISA KEV (podatności wykorzystywane w atakach, w tym w ransomware), opcjonalnie EPSS, priorytet 0–100, automatyczne oznaczanie usuniętych po ponownym skanie, akceptacja ryzyka z uzasadnieniem, eksport CSV. Alert VULN-02 dla hostów z podatnością z KEV; alerty dotyczące takich hostów mają podniesioną ważność.
- Rejestr dostawców ICT: łańcuch dostaw (NIS2 art. 21 ust. 2 lit. d) i rejestr informacji DORA (art. 28 ust. 3): krytyczność, dane osobowe, podwykonawcy, umowy, strategia wyjścia, przeglądy, kwestionariusz bezpieczeństwa (PDF), przypomnienia, eksport CSV.
- n6 CERT Polska: zdarzenia o zagrożeniach w sieci organizacji (zainfekowane hosty, C&C, phishing, skanowanie, podatne usługi), alert N6-01.
- Ocena ryzyka kont i hostów (nowa zakładka „Ryzyko”): wynik 0–100 z alertów, wygasanie w czasie, różnorodność reguł i etapów ATT&CK, krytyczność zasobu, podatności KEV; alert RISK-01 po przekroczeniu progu.
- Wyjątki alertów: reguła + pole + wartość (także zakres CIDR), powód i data wygaśnięcia, licznik ukrytych alertów, podgląd przed zapisaniem, „Wyjątek…” bezpośrednio z alertu.
- Test reguł: 13 scenariuszy technik ATT&CK sprawdzanych z bieżącą konfiguracją detekcji, z przyczyną niewykrycia (reguła wyłączona, próg, parser).
- Automatyzacje (KIEDY → WTEDY): incydent lub dołączenie do otwartego, przypisanie, potwierdzenie, ważność, notatka, powiadomienie, znacznik, wniosek o akcję reakcji (zawsze do zatwierdzenia przez człowieka); gotowe szablony, test na alertach z 7 dni, dziennik uruchomień.
- Nowe akcje reakcji: izolacja przez CrowdStrike Falcon i SentinelOne, usunięcie wiadomości ze wszystkich skrzynek Microsoft 365 (z cofnięciem), wymuszenie zmiany hasła w Entra ID i Active Directory.
- Monitorowanie domen i wycieków: nowe certyfikaty dla domen organizacji (Certificate Transparency, CT-01), domeny łudząco podobne (LOOK-01), konta w znanych wyciekach — Have I Been Pwned (LEAK-01).
- Przekazanie zmiany (nowa zakładka „Zmiana”): podsumowanie dyżuru z terminami NIS2 i SLA w ciągu doby, przekazanie z uwagami i potwierdzenie przejęcia, raport PDF.
- Raport kwartalny dla zarządu (PDF) z porównaniem okresów i rekomendacjami; ewidencja szkoleń członków zarządu (art. 20 ust. 2 NIS2).
- Mapowanie dowodów na ISO/IEC 27001:2022 (załącznik A) z raportem PDF.
- Retencja zdarzeń według źródła (np. przepływy 7 dni, logowania 365 dni).
- Agent macOS (dziennik ujednolicony, pliki logów, FIM i pliki-kanarki; usługa launchd).
- Serwer w kontenerze: Dockerfile i docker-compose.
- Konsola jako aplikacja na telefon (PWA) — wymaga zaufanego certyfikatu serwera.

**Zmiany**

- Alert PHISH-01 zawiera nagłówek Message-ID wiadomości (podstawa usunięcia jej z innych skrzynek).
- Gotowość NIS2: nowe dowody automatyczne (dostawcy, KEV, test wykrywania, szkolenia zarządu) — dochodzą tylko wtedy, gdy dany rejestr jest używany.
- Nagłówek konsoli nie wychodzi poza ekran na telefonie.
- Nowe funkcje nie wymagają dodatkowych modułów licencji.

## 2.7.0 2026-10-23

**Nowe**
- Własne parsery logów bez programowania: wyrażenie regularne z grupami nazwanymi, CSV/TSV albo JSON, mapowanie pól, format czasu, reguły rodzaju zdarzenia i test na próbce; także dla syslogu na żywo i w konsoli offline.
- Pułapki (honeytokeny): konto AD, plik-przynęta, klucz AWS, nazwa DNS, adres URL/niewidoczny obrazek, hasło, e-mail, udział sieciowy; gotowe przynęty do pobrania. Każde użycie to alert krytyczny HONEY-01.
- Wczesne wykrywanie ransomware (RANSOM-01): kilka sygnałów przygotowania do szyfrowania na jednym hoście w krótkim czasie; pliki-kanarki w udziałach (RANSOM-02).
- Integralność plików i rejestru (FIM) w agentach Windows i Linux (FIM-01): pliki systemowe, autostart, usługi, sudoers, klucze SSH.
- Reguły YARA (silnik wbudowany, także offline): skan wczytanych plików, załączników poczty i incydentów oraz próbek z agentów (YARA-01).
- Wykrywanie nowych urządzeń w sieci z DHCP, skanów, przepływów i agentów (ASSET-01), z producentem z adresu MAC.
- Łańcuch dowodowy w incydencie i pakiet dowodowy ZIP z manifestem SHA-256 podpisanym kluczem Ed25519 instalacji.
- Portal klienta dla usług SOC (rola „Klient”): podsumowanie bezpieczeństwa i raport PDF bez dostępu do szczegółów technicznych.
- Ćwiczenia tabletop: trzy scenariusze (ransomware, przejęcie skrzynki zarządu, nieuczciwy pracownik), ocena czasu reakcji zespołu, raport, automatyczne sprzątanie.
- Powiadomienia przez Telegram i Signal.
- Asystent AI (domyślnie wyłączony): podsumowanie incydentu, proponowane kroki, szkic zgłoszenia do CSIRT, wyjaśnienie alertu, pytania w języku naturalnym; model lokalny lub chmurowy zgodny z OpenAI, pseudonimizacja danych, podgląd wysyłanych danych, wpis w dzienniku audytu.
- Archiwum dzienników kopiowane poza serwer: Amazon S3 i zgodne (MinIO, Wasabi, Backblaze B2), Azure Blob, udział sieciowy.
- Zaszyfrowana kopia całej konfiguracji (AES-256-GCM) z harmonogramem i przywracaniem.
- Raport gotowości NIS2 (art. 21 ust. 2 lit. a–j, art. 23) z dowodami automatycznymi i deklaracjami.
- Własne pulpity z kafelkami (liczniki z progami, wykresy, najczęstsze wartości, alerty, terminy NIS2, pułapki), także offline.

**Zmiany**
- Agenci 2.7.0 (Windows, Linux) obsługują FIM i pliki-kanarki; starsze wersje agentów działają bez zmian.
- Nowe funkcje nie wymagają dodatkowych modułów licencji.

## 2.6.1 2026-10-22

**Nowe**
- Ok. 100 nowych formatów plików i logów, w konsoli offline i na serwerze (także syslog na żywo): antywirusy i EDR (ESET PROTECT, Kaspersky Security Center, Bitdefender GravityZone, CrowdStrike Falcon, Sophos Central, Arcabit, mks_vir), zapory i VPN (Stormshield, Check Point, Juniper SRX, Meraki, Aruba, UniFi, NPS/RADIUS, OpenVPN, strongSwan, WireGuard), BIND, Unbound, ModSecurity, fail2ban, FTP, VMware ESXi i vCenter, Proxmox, Synology, QNAP, Veeam, MSSQL, MySQL/MariaDB, PostgreSQL, AD FS, Kubernetes, Docker, GitHub/GitLab, AWS ALB/S3/CloudFront/GuardDuty, Elastic ECS, OCSF, Hayabusa, Chainsaw, Velociraptor, EZTools, historia PowerShell/bash, zadania harmonogramu, wtmp/btmp, .xlsx, .nessus, Nmap, mbox, gałęzie rejestru Windows, historia Chrome/Edge/Firefox, Prefetch, SRUM, .pst/.ost, NetFlow v5/v9/IPFIX; archiwa .7z, .xz, .bz2, .zst.
- 14 nowych reguł wykrywania (m.in. wykrycia AV/EDR, ataki na WWW, masowe usuwanie plików, wynik narzędzi DFIR, publicznie wystawione usługi).
- Logowanie SSO: OpenID Connect (Microsoft Entra ID, Google, Okta, Keycloak) oraz LDAP/Active Directory, role nadawane z grup.
- Weryfikacja dwuetapowa (TOTP) z kodami odzyskiwania; administrator może ją wymagać od kierowników i administratorów albo od wszystkich.
- Klucze API dla skryptów i integracji (rola, ograniczenie do sieci, data wygaśnięcia).
- Metryki Prometheus/OpenMetrics (`/metrics`).
- UEBA: profile zachowania kont i alerty UEBA-01 (nowy host), UEBA-02 (nowy kraj), UEBA-03 (nietypowa pora).
- Licencje: edycje Standard, Pro i Enterprise, żądanie aktywacji i raport użycia z konsoli, lista unieważnień, przypomnienia o wygasaniu.

**Zmiany**
- Licencja obejmuje wersje wydane do końca wsparcia; nowsza wersja działa 14 dni, potem konsola przechodzi w tryb tylko do odczytu (zbieranie logów i wykrywanie działają zawsze).
- Wykrycie malware w zdarzeniach CEF od producentów AV jest teraz zdarzeniem antywirusa (reguła EDR-02).


## 2.5.0 2026-10-14

**Nowe**
- Licencja i 30-dniowa wersja próbna (Ustawienia → Licencja): limit hostów z agentem i klientów, okres karencji 14 dni, zbieranie logów i wykrywanie działają zawsze.
- Dziennik audytu chroniony łańcuchem skrótów HMAC-SHA256 z weryfikacją spójności, filtrami i eksportem CSV; rejestrowane są także wyszukiwania analityków, pobrania załączników i eksporty.
- NIS2/KSC: raport pośredni na żądanie CSIRT i sprawozdanie z postępu dla incydentów trwających w terminie sprawozdania końcowego.

**Zmiany**
- Detekcja ciągła dociąga tylko nowe zdarzenia zamiast odczytywać całe okno, przy ok. 1 mln zdarzeń na dobę, jeden przebieg trwa ok. 3 razy krócej.

**Poprawki**
- Termin sprawozdania końcowego NIS2 liczony od zgłoszenia incydentu (wcześniej od wykrycia).
- Usługa Windows nie uruchamiała się po instalacji (błędna ścieżka w konfiguracji opakowania usługi).

## 2.4.0

- Ok. 30 nowych formatów logów: archiwa ZIP/GZ/TAR, UTF-16 i Windows-1250, CEF/LEEF, Graylog, GELF, SentinelOne (Threats, Deep Visibility, Activities), Windows (zapora, DHCP, USB, Defender, CBS), Squid, Exchange, Postfix, Palo Alto, Sophos, MikroTik, Cisco IOS, Okta, Cloudflare, AWS VPC i Azure NSG Flow Logs, Wazuh, osquery, Snort, Nessus, OpenVAS, Outlook .msg.
- Logi Graylog, Fluent Bit i WindowsUpdate.log z regułami utraty logów i błędów aktualizacji.
- Automatyczna analiza przyczyny (RCA) przy każdym alercie.
- Podpisane cyfrowo pliki wykonywalne i instalatory.
