---
name: setup
description: Legt das Akquise-Suchprofil für Immobilien an oder aktualisiert es (Region, Assetklasse, Ticketgröße, Renditeziele, Zielgruppen wie Makler, Bauträger, Verwalter, Family Offices). Nutze diesen Skill beim ersten Start von /scout, wenn noch keine Datei .scout/profile.md existiert oder wenn der Nutzer sein Suchprofil ändern will.
argument-hint: "[optional: Kurzbeschreibung, z. B. 'MFH Ruhrgebiet bis 3 Mio']"
allowed-tools: Read, Write, Edit, Glob, WebSearch, WebFetch, AskUserQuestion
---

# /scout:setup – Suchprofil anlegen

Ziel: eine einzige, gut strukturierte Datei `.scout/profile.md` im aktuellen Arbeitsverzeichnis, auf der `/scout:search` und `/scout:fetch` aufbauen. Kein Profil, keine Suche.

## Ablauf

1. **Bestand prüfen.** Existiert `.scout/profile.md`, lies sie und frage nur, was sich ändern soll. Sonst neu anlegen.
2. **Argument nutzen.** Wenn `$ARGUMENTS` eine Kurzbeschreibung enthält, fülle daraus vor, was eindeutig ist, und frage nur den Rest.
3. **Gebündelt fragen** (höchstens zwei Runden, mit `AskUserQuestion`, wenn verfügbar). Pflichtfelder:
   - **Rolle:** Bestandshalter/Investor, Bauträger/Projektentwickler, Makler (sucht Mandate), Aufteiler, Asset-/Fondsmanager, Finanzierer, Dienstleister.
   - **Akquiseziel:** (a) Objekte/Grundstücke kaufen, (b) Verkaufsmandate gewinnen, (c) Kooperationspartner finden (Makler, Tippgeber), (d) Käufer/Investoren für eigene Objekte finden. Mehrfachwahl.
   - **Region:** Städte, Landkreise, PLZ-Bereiche oder Radius um einen Ort.
   - **Assetklassen:** ETW, MFH/Zinshaus, Wohn- und Geschäftshaus, Gewerbe/Büro, Einzelhandel, Logistik/Hallen, Hotel, Pflege/Sozialimmobilie, Grundstück (Bauland, Bauerwartungsland), Portfolio.
   - **Ticketgröße:** Kaufpreis von–bis, ggf. Einheiten oder m².
   - **Kennzahlen:** max. Kaufpreisfaktor, min. Bruttomietrendite, max. €/m², Leerstandstoleranz, Baujahr/Zustand (Sanierungsfall ok?).
   - **Sonderlagen:** Off-Market, Zwangsversteigerung, Insolvenz, Nachlass/Erbengemeinschaft, Share Deal, Erbbaurecht, Denkmal.
   - **Ausschlüsse:** Lagen, Assetklassen, Anbieter, die nicht gewünscht sind.
   - **Eigene Angaben für Ansprache:** Firmenname, Kurz-Pitch (1–2 Sätze), gewünschter Kanal (E-Mail, Telefon, LinkedIn, Brief).
4. **Werkzeuge prüfen.** Teste kurz, ob `WebSearch` und `WebFetch` funktionieren (eine harmlose Suche, z. B. "IVD Maklersuche"). Notiere zusätzlich vorhandene Scraping-/Daten-Tools (z. B. Firecrawl, Bright Data, Apify, ein CRM-Connector), falls sie in der Sitzung verfügbar sind. Fehlt Websuche, sag es klar: `/scout:search` funktioniert dann nur mit vom Nutzer gelieferten Links.
5. **Datei schreiben** nach der Vorlage unten. Lege außerdem `.scout/leads/` an und – falls im Ordner ein Git-Repo liegt – prüfe, dass `.scout/` in `.gitignore` steht (sonst ergänzen und darauf hinweisen: Leads enthalten Kontaktdaten).
6. **Abschluss:** Fasse das Profil in 5 Zeilen zusammen und schlage den ersten konkreten Aufruf vor, z. B. `/scout:search makler` oder `/scout:search objekte`.

## Vorlage `.scout/profile.md`

```markdown
# Scout-Suchprofil
Stand: <JJJJ-MM-TT>

## Rolle & Ziel
- Rolle: …
- Akquiseziele: objekte | mandate | partner | kaeufer

## Suchraum
- Region: …
- Assetklassen: …
- Ticket: … bis … €
- Sonderlagen: …

## Kennzahlen (Filter)
- Kaufpreisfaktor max: …
- Bruttorendite min: … %
- €/m² max: …
- Zustand/Baujahr: …

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

- Frage nichts, was aus `$ARGUMENTS` oder der bestehenden Datei schon eindeutig hervorgeht.
- Keine erfundenen Defaults bei Kennzahlen: Lässt der Nutzer ein Feld offen, schreibe `offen`.
- Speichere keine Zugangsdaten oder API-Keys in der Profildatei.
