# Instalacja Strażnicy SOC 2.8

## Serwer

1. Uruchom `StraznicaSOC-Serwer-Setup-2.8.2.exe` **jako administrator**. Jeśli Windows SmartScreen pokaże ostrzeżenie, wybierz „Więcej informacji” → „Uruchom mimo to”.
2. Na stronie „Ustawienia sieci” podaj porty (domyślnie 8443 dla konsoli i 514 dla syslog). Pozostaw zaznaczone dodanie certyfikatu do zaufanych i otwarcie portów w Zaporze Windows.
3. Instalator:
   - kopiuje program do `C:\Program Files\Straznica SOC`;
   - tworzy katalog danych `C:\ProgramData\StraznicaSOC` (baza, certyfikat, logi, `config.json`);
   - generuje certyfikat TLS i rejestruje usługę Windows „Strażnica SOC” z automatycznym startem i restartem po awarii.
4. Po około minucie (pierwszy start rozpakowuje i sprawdza pliki) otwórz na serwerze **https://localhost:8443** i załóż konto administratora (hasło min. 10 znaków). Pierwsze konto można założyć tylko z samego serwera.

Sprawdzenie usługi (PowerShell jako administrator):

```powershell
Get-Service StraznicaSOC
Get-Content C:\ProgramData\StraznicaSOC\logs\*.log -Tail 30
```

Instalacja cicha:

```
StraznicaSOC-Serwer-Setup-2.8.2.exe /S /PORT=8443 /SYSLOGPORT=514
```

**Aktualizacja** — uruchom nowy instalator na istniejącej instalacji. Dane zostają, baza aktualizuje się przy starcie. Przed aktualizacją skopiuj katalog `C:\ProgramData\StraznicaSOC`.

### Wymagania

| | Minimum | Do ok. 1 mln zdarzeń na dobę |
|---|---|---|
| System | Windows Server 2016–2025 lub Windows 10/11, 64-bit | jak obok |
| Procesor | 2 vCPU | 4–8 vCPU |
| Pamięć | 4 GB RAM | 8–16 GB RAM |
| Dysk | 40 GB | SSD |

Porty przychodzące: **8443/TCP** (konsola, agenci, serwer zapasowy), **514/UDP i TCP** (syslog). Konsoli nie publikuj w Internecie, udostępniaj ją w sieci wewnętrznej lub przez VPN.

## Licencja

Bez klucza serwer działa **30 dni w pełnej wersji** (do 25 hostów z agentem i 2 dodatkowych klientów). Klucz licencyjny wklej w **Ustawienia → Licencja** albo zapisz jako `C:\ProgramData\StraznicaSOC\licencja.txt`, serwer wczyta go przy starcie. W tej samej zakładce jest identyfikator instalacji, jeśli licencja ma być przypięta do jednego serwera.

Po wygaśnięciu licencji przez 14 dni działa okres karencji, potem konsola przechodzi w tryb tylko do odczytu. **Zbieranie logów, wykrywanie i powiadomienia działają zawsze.**

## Agent Windows

1. W konsoli: **Ustawienia → Agenci i syslog → Nowy token** (token jest pokazywany tylko raz).
2. Na komputerze uruchom `StraznicaAgent-Setup.exe` i podaj adres serwera (np. `https://soc.firma.local:8443`), token oraz odcisk certyfikatu. Gotowe polecenie instalacji cichej jest w konsoli:

```
StraznicaAgent-Setup.exe /S /SERVER=https://soc.firma.local:8443 /TOKEN=sa_xxxxxxxx /THUMBPRINT=EA640D9F…
```

Agent zbiera dzienniki Security, System, PowerShell, Sysmon i Defender i wysyła nowe zdarzenia co 30 sekund. Instalator włącza zalecaną politykę audytu (`/NOAUDIT` ją pomija). Najlepsze wyniki daje dodatkowo Sysmon.

Od 2.7 agent może też monitorować integralność plików i kluczy rejestru oraz pliki-kanarki — włączysz to w **Ustawienia → Integralność plików** (konfiguracja trafia do agentów automatycznie).

## Agent Linux

W **Ustawienia → Agenci i syslog** utwórz token i skopiuj polecenie z sekcji „Agent Linux”, np.:

```bash
curl -k --pinnedpubkey 'sha256//…' -o install-straznica-agent.sh https://soc.firma.local:8443/download/agent-linux.sh
sudo STRAZNICA_TOKEN='sa_…' bash install-straznica-agent.sh --server https://soc.firma.local:8443 --pin 'sha256//…'
```

Agent zbiera journald (SSH, sudo, konta, zapora), auditd i wskazane pliki logów, a od 2.7 — po włączeniu w konsoli — monitoruje integralność plików (FIM). Wymaga bash, curl, gzip i sha256sum.

## Agent macOS

Od 2.8. W **Ustawienia → Agenci i syslog** skopiuj polecenie z sekcji „Agent macOS” i uruchom je w Terminalu na Macu (macOS 12 lub nowszy, konto administratora):

```bash
curl -k --pinnedpubkey 'sha256//…' -o install-straznica-agent-macos.sh https://soc.firma.local:8443/download/agent-macos.sh
sudo STRAZNICA_TOKEN='sa_…' bash install-straznica-agent-macos.sh --server https://soc.firma.local:8443 --pin 'sha256//…'
```

Agent działa jako usługa systemowa launchd (`pl.straznica.agent`) i zbiera z dziennika ujednoliconego logowania SSH, sudo/su, okno logowania, udostępnianie ekranu, autoryzacje oraz zdarzenia XProtect i Gatekeeper; obsługuje też pliki logów, integralność plików i pliki-kanarki. Korzysta wyłącznie z narzędzi systemu (bez Homebrew). Dziennik agenta: `/Library/Logs/straznica-agent.log`. Odinstalowanie: `sudo bash install-straznica-agent-macos.sh --uninstall`. Przy zarządzaniu przez MDM możesz nadać `/bin/bash` pełny dostęp do dysku (profil PPPC), żeby monitorowanie plików obejmowało katalogi chronione przez macOS.

## Syslog i źródła chmurowe

- Firewalle, routery i przełączniki: skieruj syslog na adres serwera, port 514 (UDP lub TCP). Format rozpoznawany jest automatycznie (m.in. FortiGate, Palo Alto, Sophos, MikroTik, Cisco, pfSense, CEF/LEEF).
- Microsoft 365, Entra ID, Defender, Google Workspace, AWS, Azure: **Ustawienia → Chmura** (wymaga rejestracji aplikacji u dostawcy — opis przy każdym źródle).

## Konsola na telefonie

Konsolę serwera można zainstalować na telefonie jak aplikację (Chrome i Edge: „Zainstaluj aplikację”, Safari: „Do ekranu początkowego”) — przydaje się na dyżurze do potwierdzania alertów, przekazania zmiany i zatwierdzania akcji reakcji. Wymaga certyfikatu serwera zaufanego przez telefon; przy certyfikacie samopodpisanym konsola działa w przeglądarce, ale bez instalacji. Dane nie są przechowywane na telefonie.

## Polecenia serwisowe

```powershell
& "C:\Program Files\Straznica SOC\straznica-server.exe" --version
& "C:\Program Files\Straznica SOC\straznica-server.exe" --reset-admin admin "NoweHaslo123!"
Restart-Service StraznicaSOC
```

Kopia zapasowa: zatrzymaj usługę i skopiuj cały katalog `C:\ProgramData\StraznicaSOC`.

## Rozwiązywanie problemów

| Objaw | Co sprawdzić |
|---|---|
| `ERR_CONNECTION_REFUSED` na https://localhost:8443 | `Get-Service StraznicaSOC`, jeśli `Stopped`, zobacz dziennik w `C:\ProgramData\StraznicaSOC\logs`; port może być zajęty przez inny program |
| Ostrzeżenie przeglądarki o certyfikacie | certyfikat jest self signed, pobierz `https://SERWER:8443/download/server.crt` i dodaj do zaufanych (lub wdroż przez GPO) |
| Agent nie wysyła danych | `powershell -ExecutionPolicy Bypass -File "C:\Program Files\Straznica SOC Agent\StraznicaAgent.ps1" -Test` |
| „Limit hostów licencji” | nowe agenty ponad limit nie są przyjmowane, sprawdź **Ustawienia → Licencja** |

Problem, którego tu nie ma? [Zgłoś go w Issues](https://github.com/podatnosci/straznica-soc/issues), dołącz wersję (`--version`) i ostatnie linie dziennika (bez danych firmy)

## Aktualizacja do wersji 2.8

Uruchom instalator 2.8.2 na istniejącym serwerze (także z 2.8.0 i 2.8.1) — dane, ustawienia i licencja zostają; aktualizacja obejmuje też zmiany z 2.7 (opis niżej). Warto wiedzieć:

- **Nowe funkcje nie wymagają innej licencji.** Jeśli wsparcie licencji skończyło się przed datą wydania 2.8.0 (8.11.2026), po aktualizacji masz 14 dni na odnowienie wsparcia.
- **Podatności:** wczytaj wynik skanera (Nessus, OpenVAS, Nmap) — rejestr zbuduje się sam. Katalog CISA KEV pobiera się raz na dobę (wymaga dostępu do cisa.gov); EPSS włączysz w zakładce Podatności.
- **n6 CERT Polska** i **monitorowanie domen i wycieków** są wyłączone, dopóki ich nie skonfigurujesz (klucz API n6 z portalu n6, domeny organizacji, opcjonalnie klucz Have I Been Pwned).
- **Ocena ryzyka** działa od razu na alertach z ostatnich 7 dni; próg alertu RISK-01 (domyślnie 60) zmienisz w zakładce Ryzyko.
- **Automatyzacje** nie robią nic, dopóki ich nie utworzysz (gotowe szablony w Ustawienia → Automatyzacje). Akcje reakcji z automatyzacji zawsze czekają na zatwierdzenie.
- **Nowe akcje reakcji** (CrowdStrike, SentinelOne, usuwanie wiadomości z Microsoft 365) włączysz w Ustawienia → Reakcja. Usuwanie wiadomości wymaga uprawnienia aplikacji Mail.ReadWrite w Microsoft Graph.
- Od 2.8.1 **test reguł uruchamia się sam** po każdej zmianie wyjątków, progów, wyłączeń i reguł oraz raz na dobę, i uwzględnia wyjątki alertów. Jeśli zmiana ukryje podstawowy atak, dostaniesz alert RT-01 z przyczyną, autorem zmiany i przyciskiem cofnięcia (**Ustawienia → Test reguł**).
- Od 2.8.2 **raport dla zarządu** (Ustawienia → Zarząd) ma sekcję „Strojenie a skuteczność wykrywania”: zmiany strojenia, testy, regresje z medianą czasu naprawy, ograniczenie szumu, świadomie zaakceptowane luki i zdanie do slajdu. Pełne porównanie kwartałów pojawi się po kwartale pracy testu automatycznego.

## Aktualizacja do wersji 2.7

Uruchom instalator 2.7.0 na istniejącym serwerze — dane, ustawienia i licencja zostają. Warto wiedzieć:

- **Nowe funkcje nie wymagają innej licencji.** Jeśli wsparcie licencji skończyło się przed datą wydania 2.7.0 (23.10.2026), po aktualizacji masz 14 dni na odnowienie wsparcia albo powrót do 2.6.1.
- **Asystent AI jest wyłączony**, dopóki administrator go nie skonfiguruje (Ustawienia → Asystent AI). Bez tego żadne dane nie są wysyłane do modeli językowych.
- **Adres-pułapka** `https://serwer:8443/c/<token>` jest dostępny bez logowania (tak ma działać). Jeśli konsola jest za zaporą, pułapki URL zadziałają tylko w sieci, z której serwer jest osiągalny.
- **Wykrywanie nowych urządzeń** zaczyna od 7-dniowego okresu nauki — w tym czasie zapamiętuje sieć bez alertów.
- **Agentów** zaktualizuj, jeśli chcesz używać integralności plików i plików-kanarków; starsze agenty działają dalej.
- Ustaw **kopię konfiguracji** (Ustawienia → Kopia konfiguracji) i zapisz jej hasło w sejfie haseł.

## Aktualizacja z wersji 2.5

Uruchom instalator 2.6.1 lub nowszy na istniejącym serwerze — dane, ustawienia i licencja zostają. Warto wiedzieć:

- **Licencja obejmuje wersje wydane do końca wsparcia.** Jeśli wsparcie Twojej licencji skończyło się przed datą wydania 2.6.1 (22.10.2026), po aktualizacji masz 14 dni na odnowienie wsparcia albo powrót do wersji 2.5 — potem konsola działa tylko do odczytu (zbieranie logów i wykrywanie działają nadal).
- **Logowanie SSO, LDAP i weryfikacja dwuetapowa** są wyłączone, dopóki ich nie skonfigurujesz w Ustawienia → Logowanie i SSO.
- **Metryki** (`/metrics`) są domyślnie wyłączone — włączysz je w Ustawienia → Metryki.
- **UEBA** zaczyna uczyć się zachowania kont od razu; pierwsze alerty pojawią się po okresie nauki (domyślnie 14 dni).
- Serwer zapasowy aktualizuj razem z głównym (synchronizuje m.in. klucze API i klucz szyfrowania weryfikacji dwuetapowej).
