# Historia zmian

## 2.7.0 — 2026-10-23

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

## 2.6.1 — 2026-10-22

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
