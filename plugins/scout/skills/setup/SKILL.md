---
name: setup
description: Legt das Immobilien-Akquise-Suchprofil (.scout/profile.md) an oder ändert es – Rolle, Akquiseziel, Region, Assetklasse, Ticketgröße, Kennzahlen wie Kaufpreisfaktor. Nutze diesen Skill, wenn jemand Scout einrichten, ein Suchprofil anlegen oder ändern will („ich suche MFH in Köln bis 3 Mio“, „ich bin Makler und suche Mandate in Bonn“, „wir sind Bauträger und suchen Grundstücke“), und immer dann, wenn /scout:search oder /scout:fetch ohne vorhandenes Profil aufgerufen wird.
argument-hint: "[Kurzbeschreibung, z. B. 'Investor, MFH Köln 1–3 Mio, Faktor max 20']"
allowed-tools: Read, Write, Edit, Glob, AskUserQuestion
---

# /scout:setup – Suchprofil anlegen

Ziel: die Datei `.scout/profile.md` im aktuellen Arbeitsverzeichnis. Darauf bauen `/scout:search` und `/scout:fetch` auf. Der Lauf soll in unter einer Minute fertig sein und **immer mit einer gespeicherten Datei enden**, sobald die vier Kernangaben bekannt sind.

## Ablauf

1. **Bestand prüfen.** Existiert `.scout/profile.md`, lies sie. Enthält `$ARGUMENTS` Änderungen, übernimm sie direkt und schreibe die Datei neu. Ohne Argument: zeige das Profil in 5 Zeilen und frage, was sich ändern soll.

2. **Kernangaben aus `$ARGUMENTS` ableiten.** Die vier Kernangaben sind:
   - **Rolle:** Investor/Bestandshalter, Bauträger/Projektentwickler, Makler, Aufteiler, Asset-/Fondsmanager, Finanzierer, Dienstleister.
   - **Akquiseziel:** `objekte` (kaufen), `mandate` (Verkaufsmandate gewinnen), `partner` (Makler, Tippgeber), `kaeufer` (Käufer/Investoren für eigene Objekte). Mehrfachwahl möglich. Leite es aus der Rolle ab, wenn es eindeutig ist (Makler → `mandate`, Bauträger „sucht Grundstücke“ → `objekte`).
   - **Region:** Städte, Kreise, PLZ oder Radius.
   - **Assetklasse:** ETW, MFH/Zinshaus, Wohn- und Geschäftshaus, Büro, Einzelhandel, Logistik, Hotel, Pflege, Grundstück (Bauland/Bauerwartungsland), Portfolio.

3. **Entscheiden: schreiben oder fragen.**
   - **Alle vier Kernangaben bekannt →** sofort Schritt 4. Nicht vorher nachfragen. Alle übrigen Felder, die nicht genannt wurden, werden `offen`.
   - **Eine Kernangabe fehlt →** genau eine Rückfrage-Runde, nur zu den fehlenden Kernangaben (höchstens 3 Fragen, jeweils mit Beispielen zum Antippen). Mit `AskUserQuestion`, wenn verfügbar, sonst als nummerierte Liste im Chat. Danach schreiben, auch wenn Antworten unvollständig sind (fehlendes Feld = `offen`).

4. **Datei schreiben** nach der Vorlage unten (der Ordner `.scout/` entsteht dabei automatisch; `.scout/leads/` legt `/scout:search` beim ersten Lauf an – dafür keine Shell-Befehle). Liegt im Ordner ein Git-Repo (`.git` vorhanden), prüfe, ob `.scout/` in `.gitignore` steht; sonst ergänzen und kurz darauf hinweisen (Leads enthalten Kontaktdaten).

5. **Werkzeuge notieren, ohne Testsuche.** Trage `WebSearch`/`WebFetch` als `ja` ein, wenn diese Werkzeuge in der Sitzung angeboten werden, sonst `nein`. Keine Probesuche – das kostet Zeit und sagt wenig. Weitere vorhandene Daten-Tools (z. B. Firecrawl, Bright Data, CRM-Connector) nur nennen, wenn sie tatsächlich angeboten werden.

6. **Abschluss im Chat** (genau dieses Format):

   ```
   Profil gespeichert: .scout/profile.md
   - <Rolle> · Ziel: <Akquiseziele>
   - <Region> · <Assetklassen> · <Ticket>
   - Filter: <gesetzte Kennzahlen oder "keine">
   Noch offen (optional, jederzeit mit /scout:setup <Ergänzung> nachtragbar): <Felder>
   Nächster Schritt: /scout:search <modus>   ← <ein Satz, was dabei herauskommt>
   ```

## Modus-Empfehlung (nur diese Modi existieren)

| Akquiseziel | Erster Aufruf | Danach |
|---|---|---|
| `objekte` | `/scout:search objekte` | `/scout:search signale` |
| `mandate` | `/scout:search markt` (Tippgeber: Hausverwaltungen, Nachlass- und Insolvenzverwalter-Kanzleien) | `/scout:search signale` |
| `partner` | `/scout:search makler` | `/scout:search markt` |
| `kaeufer` | `/scout:search markt` (Bestandshalter, Family Offices, Fonds) | – |

Schlage nie einen Modus vor, der nicht in dieser Tabelle steht.

## Vorlage `.scout/profile.md`

```markdown
# Scout-Suchprofil
Stand: <JJJJ-MM-TT>

## Rolle & Ziel
- Rolle: …
- Akquiseziele: objekte | mandate | partner | kaeufer
- Empfohlener Startmodus: …

## Suchraum
- Region: …
- Assetklassen: …
- Ticket: … bis … €  (bei Grundstücken zusätzlich m² von–bis)
- Sonderlagen: … (Off-Market, Zwangsversteigerung, Insolvenz, Nachlass, Share Deal, Erbbaurecht, Denkmal – oder offen)

## Kennzahlen (Filter)
- Kaufpreisfaktor max: …
- Bruttorendite min: … %
- €/m² max: …   (bei Grundstücken: €/m² Grundstück)
- Zustand/Baujahr/Leerstand: …

## Ausschlüsse
- …

## Ansprache
- Absender: …
- Pitch: …
- Kanal: …

## Werkzeuge
- WebSearch: ja/nein
- WebFetch: ja/nein
- Weitere: …
```

## Regeln

- Frage nichts, was aus `$ARGUMENTS` oder der bestehenden Datei schon hervorgeht. Höchstens eine Rückfrage-Runde.
- Keine erfundenen Defaults bei Kennzahlen: Lässt der Nutzer ein Feld offen, schreibe `offen`. Kennzahlen, die für die Assetklasse nicht passen (Faktor/Rendite bei Grundstücken), als `nicht relevant` eintragen.
- Bei Ziel `mandate`: Weise einmal kurz darauf hin, dass Scout keine privaten Eigentümer recherchiert und Privatpersonen nur mit ausdrücklicher Einwilligung beworben werden dürfen (UWG § 7). Mandate entstehen über Tippgeber und eigene Inbound-Kanäle.
- Speichere keine Zugangsdaten, API-Keys oder privaten Kontaktdaten in der Profildatei.
