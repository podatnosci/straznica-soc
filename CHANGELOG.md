# Historia zmian

## 2.5.0 — 2026-10-14

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
