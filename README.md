# scout – Immobilien-Akquise mit Claude Code

Drei Skills für alle, die in der Immobilienwirtschaft **Objekte, Makler und Marktteilnehmer** finden wollen – Investoren, Bauträger, Makler auf Mandatssuche, Asset Manager.

| Befehl | Was er macht |
|---|---|
| `/scout:setup` | Legt dein Suchprofil an: Rolle, Region, Assetklassen, Ticket, Kennzahlen, Sonderlagen, Ausschlüsse |
| `/scout:search` | Findet Angebote, Off-Market-Signale (Zwangsversteigerungen, Insolvenzen, Bauland), Makler und weitere Marktteilnehmer und bewertet sie mit einem Score von 0 bis 100 |
| `/scout:fetch` | Vertieft einen Lead: Exposé oder Firmenseite auslesen, Kaufpreisfaktor, Rendite und €/m² berechnen, Red Flags, Impressum, nächster Schritt |

**Du musst kein Entwickler sein.** Diese Anleitung führt dich Schritt für Schritt von „noch nichts installiert“ bis zur ersten Lead-Liste. Rechne mit etwa 15 Minuten. Entwickelt und betreut von [Jan Sommershoff](#8-wer-steckt-dahinter), Dein Automatisierungsberater.

## Inhalt

1. [Bevor du startest](#1-bevor-du-startest)
2. [Claude Code installieren](#2-claude-code-installieren)
3. [scout installieren](#3-scout-installieren)
4. [Dein erster Lauf in 5 Minuten](#4-dein-erster-lauf-in-5-minuten)
5. [Alle Befehle und Suchmodi](#5-alle-befehle-und-suchmodi)
6. [Fehlerbehebung](#6-fehlerbehebung)
7. [Spielregeln (bitte lesen)](#7-spielregeln-bitte-lesen)
8. [Wer steckt dahinter?](#8-wer-steckt-dahinter)

---

## 1. Bevor du startest

Du brauchst vier Dinge. Stand: 07.10.2026, geprüft gegen die offizielle Anthropic-Dokumentation.

**1. Ein Claude-Konto mit Claude-Code-Zugang.**
Claude Code gehört zu den kostenpflichtigen Claude-Abos (Pro, Max, Team oder Enterprise). Alternativ geht ein Konto in der Claude Console mit API-Guthaben. Der kostenlose Plan von claude.ai enthält **keinen** Claude-Code-Zugang. Aktuelle Abos findest du auf [claude.com/pricing](https://claude.com/pricing). Das Konto legst du beim ersten Start in Schritt 2 an oder verwendest dein bestehendes. scout selbst ist kostenlos (MIT-Lizenz).

**2. Einen Computer mit einem dieser Systeme:**

| System | Mindestens |
|---|---|
| macOS | Version 13 |
| Windows | Windows 10, Version 1809 |
| Linux | Ubuntu 20.04, Debian 10 oder Alpine 3.19 |

Dazu 4 GB Arbeitsspeicher, eine Internetverbindung und ein Standort in einem [von Anthropic unterstützten Land](https://www.anthropic.com/supported-countries).

**3. Nur unter Windows: Git.**
scout wird von GitHub geladen, und dafür braucht Claude Code das kostenlose Programm Git. Lade es von [git-scm.com/downloads/win](https://git-scm.com/downloads/win), starte den Installer und klicke auf jeder Seite „Next“. Du musst nichts ändern. Auf dem Mac fragt das System bei Bedarf selbst nach den Entwicklertools: bestätige das einfach.

**4. Etwa 15 Minuten Zeit.**

Offizielle Quellen, falls du nachlesen willst: [claude.com/claude-code](https://claude.com/claude-code) und die [Dokumentation](https://code.claude.com/docs/en/overview).

> **Optional:** Wenn du schon Scraping- oder CRM-Connectors nutzt, erkennt `/scout:setup` sie und trägt sie ins Profil ein. Für den Start brauchst du sie nicht. Wichtig ist nur, dass Claude Code die Websuche (`WebSearch`/`WebFetch`) nutzen kann.

---

## 2. Claude Code installieren

Es gibt zwei Wege. **Du brauchst nur einen.**

> **Mein Rat für Einsteiger:** Nimm **Weg B (Terminal)**. Es ist eine einzige Zeile zum Kopieren, und die zwei scout-Befehle in Schritt 3 tippst du ohnehin im Terminal. Weg A (Desktop-App) ist bequemer zum Arbeiten, aber für den einmaligen scout-Einbau brauchst du trotzdem kurz das Terminal (das steht bei Schritt 3).

### Weg A: Desktop-App (ohne Terminal)

1. Lade die Claude-App von [claude.com/download](https://claude.com/download) und installiere sie (öffne die heruntergeladene Datei und folge den Anweisungen).
2. Starte Claude (Mac: Ordner „Programme“, Windows: Startmenü) und melde dich mit deinem Claude-Konto an.
3. Klicke oben in der Mitte auf den Reiter **Code**. Erscheint dort eine Aufforderung zum Upgrade, brauchst du erst ein bezahltes Abo (siehe Schritt 1). Verlangt die App eine Anmeldung im Browser, schließe sie ab und starte die App neu.
4. Wähle **Local** und klicke auf **Select folder**, um einen Arbeitsordner auszuwählen. Mehr dazu in Schritt 4.

Die Desktop-App bringt Claude Code schon mit. Du musst nichts weiter installieren. Linux-Nutzer finden die Anleitung in der [Linux-Doku der App](https://code.claude.com/docs/en/desktop-linux). Die offizielle Anleitung: [Desktop-Schnellstart](https://code.claude.com/docs/en/desktop-quickstart).

### Weg B: Terminal

**Schritt 1: Terminal öffnen.** Das Terminal ist ein Fenster, in das du Befehle tippst.

| System | So öffnest du es |
|---|---|
| macOS | `Cmd` + `Leertaste`, dann `Terminal` eintippen und Enter drücken |
| Windows | `Windows`-Taste + `X`, dann **Windows PowerShell** (oder **Terminal**) wählen |
| Linux | `Strg` + `Alt` + `T` |

Unter Windows erkennst du PowerShell an `PS C:\Users\DeinName>` am Zeilenanfang. Steht dort kein `PS`, bist du in der CMD und brauchst den CMD-Befehl unten.

**Schritt 2: Installationsbefehl einfügen.** Kopiere die Zeile für dein System, füge sie ins Terminal ein (Mac: `Cmd` + `V`, Windows: `Strg` + `V` oder Rechtsklick, Linux: `Strg` + `Umschalt` + `V`) und drücke Enter.

macOS, Linux und WSL:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Windows PowerShell:

```powershell
irm https://claude.ai/install.ps1 | iex
```

Windows CMD (nur wenn du in der CMD bist):

```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

Es läuft Text durch, am Ende steht „Claude Code successfully installed!“. Alternativ geht auf dem Mac `brew install --cask claude-code` (falls du Homebrew hast) und unter Windows `winget install Anthropic.ClaudeCode`.

**Schritt 3: Prüfen, ob es geklappt hat.** Öffne ein **neues** Terminalfenster und tippe:

```bash
claude --version
```

Es erscheint eine Versionsnummer mit dem Zusatz `(Claude Code)`. Dann ist alles in Ordnung. Kommt stattdessen „command not found“ oder „not recognized“, hilft [Fehlerbehebung, Punkt 1](#6-fehlerbehebung).

### Erster Start und Login (beide Wege)

- **Terminal:** Tippe `claude` und drücke Enter. Beim ersten Start öffnet sich dein Browser. Melde dich mit deinem Claude-Konto an und gehe zurück ins Terminal. Dort siehst du jetzt den Claude-Code-Willkommensbildschirm.
- **Desktop-App:** Die Anmeldung hast du in Weg A schon erledigt.

Du bist drin, wenn du einen Eingabebereich siehst, in den du Text an Claude schreiben kannst. Ab hier sind alle Befehle mit `/` für Claude gedacht.

---

## 3. scout installieren

Dafür sind **zwei Befehle** nötig. Sie beginnen mit `/` und werden **in Claude Code eingetippt**, nicht in einer normalen Terminal-Zeile.

> **Wichtig:** Die Befehle `/plugin …` funktionieren in einer Claude-Code-Sitzung im **Terminal**. Im Eingabefeld der Desktop-App meldet Claude Code dazu: „/plugin isn't available in this environment“. Wenn du Weg A gewählt hast, nimm die Variante unten („Ohne Sitzung“).

### Variante 1: In Claude Code im Terminal

1. Öffne das Terminal und starte Claude Code mit `claude` (siehe Schritt 2).
2. Tippe den ersten Befehl und drücke Enter. Er sagt Claude Code, wo scout liegt:

   ```text
   /plugin marketplace add jsommershoff-a11y/scout-immobilien
   ```

   Du siehst: `Successfully added marketplace: scout-immobilien`. Das dauert einige Sekunden.

3. Tippe den zweiten Befehl und drücke Enter:

   ```text
   /plugin install scout@scout-immobilien
   ```

   Jetzt öffnet sich eine Detailansicht zu scout. Dort stehen die drei Skills (`fetch`, `search`, `setup`) und ein allgemeiner Sicherheitshinweis, den Claude Code bei jedem Plugin zeigt. scout besteht nur aus Textdateien mit Anweisungen. Es enthält keine Programme, keine Hooks und keine MCP-Server (das kannst du mit `claude plugin details scout` selbst nachprüfen).

4. Wähle mit den Pfeiltasten **Install for you (user scope)** und drücke Enter. Damit steht scout in all deinen Ordnern zur Verfügung.
5. Zum Schluss steht in der Zusammenfassung entweder `Plugin is now active.` oder `Run /reload-plugins to apply.` Im zweiten Fall lädt Claude Code automatisch neu. Falls nicht, tippe `/reload-plugins`.

### Variante 2: Ohne Sitzung (für Desktop-Nutzer, geht aber auch sonst)

Öffne ein normales Terminal (Tabelle in Schritt 2). Falls `claude` dort noch nicht existiert, installiere es mit der Zeile aus Weg B, Schritt 2. Einloggen musst du dafür nicht. Füge dann diese zwei Zeilen nacheinander ein (jeweils mit Enter):

```bash
claude plugin marketplace add jsommershoff-a11y/scout-immobilien
claude plugin install scout@scout-immobilien
```

Du siehst `Successfully added marketplace: scout-immobilien` und danach `Successfully installed plugin: scout@scout-immobilien (scope: user)`. Terminal, Desktop-App und VS Code lesen laut Anthropic-Doku dieselben Plugin-Einstellungen. Starte die Desktop-App danach einmal neu, dann findest du scout dort ebenfalls. Gibt es Probleme, arbeite mit scout einfach im Terminal.

### Neustart oder Reload

Wenn der Check unten nichts zeigt: tippe `/reload-plugins`. Hilft das nicht, beende Claude Code (tippe `/exit` oder drücke zweimal `Strg` + `D`) und starte es mit `claude` neu.

### Check: Bin ich fertig?

Tippe in Claude Code nur `/scout`. Es erscheint eine Liste mit diesen drei Einträgen:

```text
/scout:setup
/scout:search
/scout:fetch
```

Siehst du alle drei, ist scout installiert. Du kannst die Liste mit `Esc` wieder schließen. Merke dir den Doppelpunkt: Es heißt `/scout:setup`, nicht `/scout-setup`.

### Später aktualisieren oder entfernen

```bash
claude plugin update scout@scout-immobilien
claude plugin uninstall scout@scout-immobilien
```

Beides tippst du in einem normalen Terminal. Ein Update wirkt nach einem Neustart von Claude Code.

---

## 4. Dein erster Lauf in 5 Minuten

Wir legen ein Suchprofil an, suchen Makler in Dortmund und vertiefen einen Treffer.

**1. Arbeitsordner anlegen und Claude Code dort starten.** scout speichert alles im Ordner, in dem du Claude Code startest. Starte Claude Code später immer wieder im selben Ordner, dann findet es dein Profil.

macOS und Linux:

```bash
mkdir -p ~/Scout && cd ~/Scout && claude
```

Windows PowerShell:

```powershell
mkdir $HOME\Scout; cd $HOME\Scout; claude
```

In der Desktop-App legst du den Ordner „Scout“ im Finder bzw. Explorer an und wählst ihn im Reiter **Code** mit **Select folder**.

Fragt Claude Code, ob du den Dateien in diesem Ordner vertraust, bestätige mit **Yes**.

**2. Suchprofil anlegen.** Tippe (du kannst Region und Zahlen für dich ändern):

```text
/scout:setup MFH Ruhrgebiet 1–3 Mio, Faktor max 18, auch Sanierungsfälle
```

Claude stellt dir jetzt ein paar Fragen: Rolle, Ziel, Region, Objektarten, Ticket, Kennzahlen, Ausschlüsse. Antworte in normalen Sätzen. Am Ende gibt es eine Zusammenfassung in fünf Zeilen und einen Vorschlag für den ersten Aufruf. Fragt Claude um Erlaubnis, etwa Dateien zu schreiben, wähle **Yes**.

**3. Makler suchen.**

```text
/scout:search makler Dortmund
```

Claude durchsucht das Web und bewertet die Treffer. Das dauert einige Minuten. Erscheinen Rückfragen zu Webzugriffen, bestätige sie mit **Yes**.

**4. Ergebnis ansehen.** Frag Claude einfach: „Zeig mir die 5 besten Leads.“ Die Dateien liegen im Ordner `.scout/leads/` in deinem Arbeitsordner, als lesbare Liste (`.md`) und für Excel (`.csv`). Auf dem Mac blendest du im Finder mit `Cmd` + `Umschalt` + `.` Ordner ein, die mit einem Punkt beginnen.

**5. Einen Treffer vertiefen.** In der Liste hat jeder Lead eine ID, zum Beispiel `S-20261007-03`:

```text
/scout:fetch S-20261007-03
```

Du bekommst einen Steckbrief mit Kennzahlen, Red Flags, Impressum und dem empfohlenen nächsten Schritt unter `.scout/leads/details/`. Statt einer ID geht auch die Adresse eines Exposés oder einer Makler-Website.

**Weiter:** `/scout:search signale` zeigt Off-Market-Hinweise wie Zwangsversteigerungen. `/scout:search objekte` sucht konkrete Angebote.

---

## 5. Alle Befehle und Suchmodi

```text
/scout:setup MFH Ruhrgebiet 1–3 Mio, Faktor max 18, auch Sanierungsfälle
/scout:search makler Dortmund
/scout:search signale
/scout:fetch S-20261007-03
```

Ergebnisse landen lokal im aktuellen Ordner:

```text
.scout/
├── profile.md                  # dein Suchprofil
└── leads/
    ├── 2026-10-07-makler.md    # bewertete Liste
    ├── 2026-10-07-makler.csv   # für Excel oder CRM-Import
    └── details/                # Steckbriefe aus /scout:fetch
```

### Suchmodi von `/scout:search`

- `objekte`: Angebote auf Portalen, Makler- und Bankenseiten
- `signale`: Off-Market-Hinweise wie Zwangsversteigerungen (zvg-portal.de), Insolvenzen, Bebauungspläne, Konzeptvergaben, Leerstand, Verkäufe der öffentlichen Hand
- `makler`: Makler als Quelle oder Kooperationspartner, priorisiert nach passenden Angeboten
- `markt`: Bauträger, Projektentwickler, Hausverwaltungen, Bestandshalter, Family Offices, Insolvenzverwalter, Finanzierer
- `alle` (Standard): alles oben, je Modus kompakt

---

## 6. Fehlerbehebung

Such dein Symptom, dann steht die Lösung direkt dahinter.

**1. `command not found: claude` oder `'claude' is not recognized`**
Schließe das Terminal und öffne ein **neues** Fenster. Hilft das nicht, kennt dein System den Installationsordner noch nicht. Mac (Standard-Shell Zsh):

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Windows PowerShell (danach neues Fenster öffnen):

```powershell
$currentPath = [Environment]::GetEnvironmentVariable('PATH', 'User')
[Environment]::SetEnvironmentVariable('PATH', "$currentPath;$env:USERPROFILE\.local\bin", 'User')
```

Linux-Details und mehr: [Anthropic-Hilfe zur Installation](https://code.claude.com/docs/en/troubleshoot-install).

**2. Windows: `irm is not recognized` oder `The token '&&' is not a valid statement separator`**
Du hast die Zeile im falschen Programm eingefügt. `irm …` gehört in PowerShell, die `curl …`-Zeile in die CMD. Öffne mit `Windows` + `X` die **Windows PowerShell** und nimm den PowerShell-Befehl aus Schritt 2.

**3. `syntax error near unexpected token '<'`, `403` oder „App unavailable in region“**
Der Installer wurde nicht geladen. Versuche es noch einmal. Steht dort „App unavailable in region“, ist Claude Code in deinem Land nicht verfügbar ([Länderliste](https://www.anthropic.com/supported-countries)). Auf dem Mac hilft sonst `brew install --cask claude-code`.

**4. Mac: `dyld … built for Mac OS X 13.0`**
Dein macOS ist zu alt. Prüfe die Version über Apfel-Menü → „Über diesen Mac“ und aktualisiere über „Softwareupdate“ auf Version 13 oder neuer.

**5. Login klappt nicht**
Öffnet sich kein Browser, drücke im Terminal `c`: Der Anmeldelink wird kopiert, und du fügst ihn selbst im Browser ein. Bei „OAuth error: Invalid code“ den Vorgang schnell wiederholen. Bleibt es dabei, tippe `/logout`, beende Claude Code und starte mit `claude` neu. Bei „403 Forbidden“ prüfe, ob dein Abo aktiv ist ([claude.ai/settings](https://claude.ai/settings)). In der Desktop-App hilft: abmelden, wieder anmelden. Ohne bezahltes Abo bleibt der Code-Reiter gesperrt (Schritt 1).

**6. Du hast `/plugin …` eingegeben, aber es kommt `zsh: no such file or directory: /plugin` oder `The term '/plugin' is not recognized`**
Du hast den Befehl in einer normalen Terminal-Zeile eingegeben. Tippe zuerst `claude`, drücke Enter und gib den Befehl dann in Claude Code ein. Oder nimm die Zeilen aus Schritt 3, Variante 2.

**7. `/plugin isn't available in this environment`**
Das passiert in der Desktop-App, in VS Code und im Browser. Nimm Schritt 3, Variante 2 (zwei Zeilen im Terminal).

**8. `Invalid marketplace source format`**
Beim Abtippen hat sich ein Fehler eingeschlichen. Kopiere den Befehl 1:1: `jsommershoff-a11y/scout-immobilien` mit Bindestrich, ohne Leerzeichen.

**9. `Failed to clone marketplace repository`, `SSH authentication failed` oder `host key`**
Claude Code konnte GitHub nicht erreichen. Nimm die Adresse im https-Format, dann wird kein SSH-Schlüssel gebraucht:

```text
/plugin marketplace add https://github.com/jsommershoff-a11y/scout-immobilien.git
```

**10. Windows: `Command 'git' not found or is in an unsafe location`**
Git fehlt. Installiere [Git for Windows](https://git-scm.com/downloads/win) (überall „Next“ klicken), öffne ein neues Terminal, prüfe mit `git --version` und wiederhole den Befehl.

**11. `Plugin "scout" not found in marketplace "scout-immobilien"`**
Aktualisiere die Liste und versuche es noch einmal:

```text
/plugin marketplace update scout-immobilien
/plugin install scout@scout-immobilien
```

**12. Nach der Installation fehlt `/scout:setup`**
Reihenfolge: (1) `/reload-plugins` tippen. (2) `/plugin` tippen und im Reiter **Errors** nachsehen, ob dort etwas steht. (3) Claude Code beenden und mit `claude` neu starten. (4) In der Desktop-App die App ganz beenden und neu öffnen. Achte auf die Schreibweise `/scout:setup`.

**13. `/scout:search` sagt, es gebe kein Suchprofil**
Entweder fehlt `/scout:setup`, oder du hast Claude Code in einem anderen Ordner gestartet. Das Profil liegt in `.scout/profile.md` in dem Ordner, in dem du gestartet hast. Wechsle in deinen Ordner `Scout` (siehe Schritt 4).

**14. Ein Link oder Exposé ist „nicht abrufbar“**
Manche Seiten sperren automatische Abrufe (Login, Bot-Schutz, AGB). scout umgeht das bewusst nicht. Kopiere das Exposé als Text hinein oder lege es als PDF ab und gib es Claude.

**15. Die Websuche scheint nicht zu funktionieren**
`/scout:setup` testet sie beim Einrichten. Ohne Websuche arbeitet `/scout:search` nur mit Links, die du selbst lieferst. Frag Claude: „Funktioniert die Websuche?“.

Weitere Hilfe: [Fehlerhilfe für Plugins](https://code.claude.com/docs/en/plugins/troubleshooting) und [für die Installation](https://code.claude.com/docs/en/troubleshoot-install). Oder schreib Jan (siehe [Kontakt](#8-wer-steckt-dahinter)).

---

## 7. Spielregeln (bitte lesen)

- **Portale:** Keine Massenabrufe bei Portalen, deren AGB das verbieten. Gelesen werden Suchergebnisse und einzelne Detailseiten, und robots.txt wird respektiert.
- **DSGVO:** Erfasst werden nur geschäftliche, öffentlich bereitgestellte Kontaktdaten von Unternehmen. Privatpersonen wie Eigentümer, Erben oder Mieter werden nicht recherchiert.
- **UWG § 7:** Die Skills versenden nichts. Werbe-E-Mails brauchen eine vorherige Einwilligung. Kaltanrufe bei Unternehmen brauchen mindestens eine mutmaßliche, bei Privatpersonen eine ausdrückliche Einwilligung.
- **Keine Beratung:** Die Kennzahlen sind eine Ersteinschätzung und weder Gutachten noch Rechts-, Steuer- oder Finanzierungsberatung.
- `.scout/` steht in `.gitignore`. Leads gehören nicht in öffentliche Repos.

---

## 8. Wer steckt dahinter?

**Jan Sommershoff, Dein Automatisierungsberater.**

scout zeigt, wie das aussieht: Recherche, die sonst Stunden kostet (Objekte, Makler und Marktsignale finden, bewerten und vorsortieren), übernimmt die KI. Du entscheidest. Dasselbe Prinzip setzt Jan für Unternehmen in Köln, Bonn und NRW um: KI-Agenten und Prozessautomatisierung, strukturiert und persönlich begleitet.

**Was wäre in deinem Unternehmen automatisierbar?** Teste es kostenlos und unverbindlich an einem echten Ablauf aus deinem Alltag. Jan zeigt dir, welche seiner KI-Agenten dir Arbeit abnehmen und wo du selbst entscheidest:

**[Agentensystem testen](https://www.dein-automatisierungsberater.de/agentensystem-testen)**

| Kontakt | Link |
|---|---|
| Website | [dein-automatisierungsberater.de](https://www.dein-automatisierungsberater.de) |
| Agentensystem testen | [dein-automatisierungsberater.de/agentensystem-testen](https://www.dein-automatisierungsberater.de/agentensystem-testen) |
| LinkedIn | [Jan Sommershoff](https://www.linkedin.com/in/jan-sommershoff-719787218/) |
| Instagram | [@dein_automatisierungsberater](https://www.instagram.com/dein_automatisierungsberater/) |
| WhatsApp | [Nachricht an Jan](https://wa.me/491751127114) |

Fragen zu scout, Ideen oder Feedback? Schreib Jan über einen der Kanäle oder eröffne ein [Issue](https://github.com/jsommershoff-a11y/scout-immobilien/issues).

---

## Mitmachen

Issues und Pull Requests sind willkommen, zum Beispiel weitere Quellen je Bundesland, Assetklassen-Checklisten oder CRM-Exporte. Die Skills sind reine Markdown-Dateien unter `plugins/scout/skills/`.

## Lizenz

MIT
