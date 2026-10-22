# Historia zmian

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
