# Instalacja Strażnicy SOC 2.5

## Serwer

1. Uruchom `StraznicaSOC-Serwer-Setup-2.5.0.exe` **jako administrator**. Jeśli Windows SmartScreen pokaże ostrzeżenie, wybierz „Więcej informacji” → „Uruchom mimo to”.
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
StraznicaSOC-Serwer-Setup-2.5.0.exe /S /PORT=8443 /SYSLOGPORT=514
```

**Aktualizacja** — uruchom nowy instalator na istniejącej instalacji. Dane zostają, baza aktualizuje się przy starcie. Przed aktualizacją skopiuj katalog `C:\ProgramData\StraznicaSOC`.

### Wymagania

| | Minimum | Do ok. 1 mln zdarzeń na dobę |
|---|---|---|
| System | Windows Server 2016–2025 lub Windows 10/11, 64-bit | jak obok |
| Procesor | 2 vCPU | 4–8 vCPU |
| Pamięć | 4 GB RAM | 8–16 GB RAM |
| Dysk | 40 GB | SSD |

Porty przychodzące: **8443/TCP** (konsola, agenci, serwer zapasowy), **514/UDP i TCP** (syslog). Konsoli nie publikuj w Internecie — udostępniaj ją w sieci wewnętrznej lub przez VPN.

## Licencja

Bez klucza serwer działa **30 dni w pełnej wersji** (do 25 hostów z agentem i 2 dodatkowych klientów). Klucz licencyjny wklej w **Ustawienia → Licencja** albo zapisz jako `C:\ProgramData\StraznicaSOC\licencja.txt` — serwer wczyta go przy starcie. W tej samej zakładce jest identyfikator instalacji, jeśli licencja ma być przypięta do jednego serwera.

Po wygaśnięciu licencji przez 14 dni działa okres karencji, potem konsola przechodzi w tryb tylko do odczytu. **Zbieranie logów, wykrywanie i powiadomienia działają zawsze.**

## Agent Windows

1. W konsoli: **Ustawienia → Agenci i syslog → Nowy token** (token jest pokazywany tylko raz).
2. Na komputerze uruchom `StraznicaAgent-Setup.exe` i podaj adres serwera (np. `https://soc.firma.local:8443`), token oraz odcisk certyfikatu. Gotowe polecenie instalacji cichej jest w konsoli:

```
StraznicaAgent-Setup.exe /S /SERVER=https://soc.firma.local:8443 /TOKEN=sa_xxxxxxxx /THUMBPRINT=EA640D9F…
```

Agent zbiera dzienniki Security, System, PowerShell, Sysmon i Defender i wysyła nowe zdarzenia co 30 sekund. Instalator włącza zalecaną politykę audytu (`/NOAUDIT` ją pomija). Najlepsze wyniki daje dodatkowo Sysmon.

## Agent Linux

W **Ustawienia → Agenci i syslog** utwórz token i skopiuj polecenie z sekcji „Agent Linux”, np.:

```bash
curl -k --pinnedpubkey 'sha256//…' -o install-straznica-agent.sh https://soc.firma.local:8443/download/agent-linux.sh
sudo STRAZNICA_TOKEN='sa_…' bash install-straznica-agent.sh --server https://soc.firma.local:8443 --pin 'sha256//…'
```

Agent zbiera journald (SSH, sudo, konta, zapora), auditd i wskazane pliki logów. Wymaga bash, curl i gzip.

## Syslog i źródła chmurowe

- Firewalle, routery i przełączniki: skieruj syslog na adres serwera, port 514 (UDP lub TCP). Format rozpoznawany jest automatycznie (m.in. FortiGate, Palo Alto, Sophos, MikroTik, Cisco, pfSense, CEF/LEEF).
- Microsoft 365, Entra ID, Defender, Google Workspace, AWS, Azure: **Ustawienia → Chmura** (wymaga rejestracji aplikacji u dostawcy — opis przy każdym źródle).

## Konsola analityka

`StraznicaSOC-Konsola-Setup-2.5.0.exe` instaluje się bez uprawnień administratora. Podaj adres serwera, aby pracować na serwerze; puste pole uruchamia **tryb offline** do analizy plików logów na komputerze (bez wysyłania danych).

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
