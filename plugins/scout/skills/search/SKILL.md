---
name: search
description: Sucht passend zum Scout-Suchprofil nach Immobilien-Akquisechancen – Kaufangebote, Off-Market-Signale (Zwangsversteigerung, Insolvenz, Bauland), Makler und Marktteilnehmer wie Hausverwaltungen, Bauträger, Family Offices – und liefert eine bewertete Lead-Liste als Markdown und CSV. Nutze diesen Skill für Anfragen wie „finde MFH in Köln“, „such mir Grundstücke im Rhein-Erft-Kreis“, „welche Makler/Verwalter gibt es in Bonn“, „Zwangsversteigerungen in meiner Region“.
argument-hint: "[objekte|signale|makler|markt|alle] [optional: Ort oder Zusatz]"
allowed-tools: Read, Write, Edit, Glob, WebSearch, WebFetch
---

# /scout:search – Akquisechancen finden

## 1. Vorbedingungen

- **Profil:** Lies `.scout/profile.md`. Fehlt sie: nicht suchen. Antworte mit einem Satz („Es gibt noch kein Suchprofil.“) und einem kopierfertigen Beispiel: `/scout:setup Investor, MFH Köln 1–3 Mio, Faktor max 20`. Ende.
- **Modus** = erstes Wort von `$ARGUMENTS`. Erlaubt: `objekte`, `signale`, `makler`, `markt`, `alle`. Unbekannte Wörter zuordnen und das im Ergebnis in einem Satz sagen: `mandate`/`mandat`/`eigentuemer` → `markt` + `signale`; `grundstuecke`/`angebote`/`kaufen` → `objekte`; `partner`/`tippgeber` → `makler`; `investoren`/`kaeufer` → `markt`. Ohne Modus: den „Empfohlenen Startmodus“ aus dem Profil, sonst `alle`.
- Weitere Wörter (Ort, Zusatz) überschreiben das Profil nur für diesen Lauf.
- **Ohne Websuche:** Ist `WebSearch` nicht verfügbar, sag das in einem Satz und bitte um bis zu 10 Links oder eingefügte Anzeigentexte. Verarbeite diese mit `WebFetch` bzw. direkt aus dem Text nach denselben Regeln. Nichts erfinden.

## 2. Budget (Einsteiger warten nicht 15 Minuten)

- Pro Modus höchstens **10 Websuchen** und **8 Seitenabrufe**; bei `alle` höchstens 2 Modi passend zum Akquiseziel (siehe Abschnitt 4), nicht alle vier. Ziel: fertig in unter 10 Minuten. Hängt ein Abruf oder liefert er nichts, nicht wiederholen, weiter zur nächsten Quelle.
- Ziel: **10–20 Leads** pro Lauf. Lieber 12 belegte als 30 halbe. Bleiben es unter 10, im Hinweis sagen warum und welcher Folgeaufruf mehr bringt.
- Mehrere Suchanfragen und Abrufe parallel stellen.
- **Listenseiten:** Je Portal darf die **erste** Ergebnisseite einer passenden Suche einmal gelesen werden (kein Durchblättern, keine Wiederholung). Treffer daraus zählen als Lead, wenn Titel, Preis und ein Link zur Einzelanzeige erkennbar sind; ist nur die Listen-URL bekannt, diese als Quelle angeben und in der Begründung `Quelle: Liste` vermerken.

## 3. Modi und Suchmuster

Suchanfragen immer mit Region und Assetklasse aus dem Profil formulieren.

### `objekte` – Angebote am Markt
- Portale nur über die Websuche erschließen, z. B. `"Mehrfamilienhaus kaufen" Köln site:immowelt.de`, `site:kleinanzeigen.de`, `site:immonet.de`; dazu Makler-Websites und Sparkassen-/Banken-Immobilienseiten der Region.
- Grundstücke: `"Grundstück kaufen" <Ort> Bauland`, kommunale Grundstücksbörsen, Sparkassen.
- Pro Treffer: Titel, Ort, Preis, Fläche (Wohn-/Grundstücksfläche), Einheiten, Ist-Miete (falls genannt), Anbieter (Firma oder `privat`), Datum (falls sichtbar), URL der **Einzelanzeige**.

### `signale` – Off-Market- und Sondersituationen
- **Zwangsversteigerungen:** zvg-portal.de (Bundesland/Amtsgericht der Region) und regionale Versteigerungskalender. Erfasse Objekt, Amtsgericht, Aktenzeichen, Termin, Verkehrswert – **nie** Namen von Schuldnern oder Eigentümern.
- **Insolvenzen:** Presse und insolvenzbekanntmachungen.de zu Immobilien-/Bauträgergesellschaften. Ansprechpartner ist der Insolvenzverwalter (Kanzlei), nicht der Schuldner.
- **Projekte/Bauland:** Bebauungsplanverfahren, Grundstücksausschreibungen und Konzeptvergaben der Kommunen, Ratsinformationssysteme.
- **Leerstand/Verkaufsabsichten öffentlicher Eigentümer:** Kommunen, Kirchen, Bahn, BImA, Presse.
- Signale sind Hinweise, keine Angebote: Typ `signal`.

### `makler` – Makler als Quelle oder Partner
- Verbandsverzeichnisse (IVD-Maklersuche), Maklerprofile auf Portalen, regionale Maklerhäuser.
- Pro Makler: Firma, Spezialisierung, Region, Zahl sichtbarer passender Angebote, Website, Impressum-URL. Ansprechpartner nur, wenn auf Website/Impressum geschäftlich genannt.
- Priorisiere Makler, die bereits passende Objekte (Assetklasse + Ticket) vermarkten.

### `markt` – weitere Marktteilnehmer
Je nach Akquiseziel:
- **Hausverwaltungen/WEG-Verwalter** (erfahren früh von Verkaufsabsichten – wichtigste Tippgeber für `mandate`),
- **Nachlass- und Insolvenzverwalter, Testamentsvollstrecker** – nur Kanzleien/Amtsträger, nie Erben oder Schuldner,
- **Bauträger/Projektentwickler** (Grundstücke, Globalverkauf, Forward Deals),
- **Bestandshalter, Wohnungsunternehmen, Family Offices, Fonds** (als Käufer oder Verkäufer),
- **Finanzierer, Gutachter, Notare** (Netzwerk).
Quellen: Firmenwebsites, Presse, Handelsregister-Bekanntmachungen (nur Firmendaten), Fachmedien.

## 4. Was ist ein Lead? (hängt vom Akquiseziel ab)

| Akquiseziel | Lead | Kein Lead (nur als Typ `wettbewerb`/`markt-info`, Score max. 20) |
|---|---|---|
| `objekte` | Angebote, Signale, Makler mit passenden Angeboten | – |
| `mandate` | Tippgeber: Hausverwaltungen, Nachlass-/Insolvenzverwalter, Bauträger mit Abverkauf, öffentliche Verkäufer | **andere Makler** (Wettbewerber); private Verkaufsanzeigen (nur als Zahl im Fazit: „x private ETW-Inserate im Ticket“ – keine Einzelzeilen, keine Kontaktdaten) |
| `partner` | Makler, Verwalter, Tippgeber | – |
| `kaeufer` | Bestandshalter, Family Offices, Fonds, Bauträger | Makler |

Bei `alle` wähle die Modi so: `objekte` → `objekte`+`signale`; `mandate` → `markt`+`signale`; `partner` → `makler`+`markt`; `kaeufer` → `markt`.

## 5. Bewertung (Score 0–100) – immer mit Teilpunkten

- **Fit (0–40):** Region (10), Assetklasse (10), Ticket (10), Kennzahlen/Filter (10). Nicht prüfbare Filter (z. B. Faktor ohne Mietangabe) = 5 statt 10 und in der Begründung nennen.
- **Filter klar verletzt** (z. B. Faktor 30 bei max. 20, Preis über Ticket): Gesamtscore höchstens 40, Begründung beginnt mit `Filter verletzt: …`. So landet so ein Treffer nie in den Top 5 vor passenden Leads.
- **Aktualität (0–20):** ≤ 30 Tage = 20, ≤ 90 Tage = 10, älter = 0–5. Datum unbekannt = 5 (nicht 0) mit Vermerk `Datum k. A.`.
- **Zugang (0–20):** geschäftlicher Ansprechpartner mit Website/Impressum = 20; nur Portal-Kontaktformular = 10; nicht erkennbar = 0.
- **Exklusivität (0–20):** Signal/Off-Market = 20; nur bei einem Anbieter gefunden = 10; auf mehreren Portalen = 5. „Nur ein Portal“ heißt nicht „exklusiv“ – so nicht begründen.

Begründung: ein Halbsatz, der den stärksten Plus- und den größten Minuspunkt nennt. Keine Zahl erfinden: fehlende Werte = `k. A.`.

## 6. Ausgabe

**Dateien** (Datum = heute, `<modus>` = tatsächlich gelaufener Modus bzw. `alle`):

1. `.scout/leads/<JJJJ-MM-TT>-<modus>.csv` – Semikolon-getrennt, UTF-8 mit Umlauten, Felder mit `;` oder `"` in Anführungszeichen. **Genau diese Spalten in dieser Reihenfolge:**
   `ID;Typ;Name;Ort;Preis_EUR;Flaeche_m2;Einheiten;Kennzahlen;Anbieter;Datum;Score;Fit;Aktualitaet;Zugang;Exklusivitaet;Begruendung;Quelle;Status;Gefunden_am`
   - `Typ` ∈ `objekt`, `signal`, `makler`, `markt`, `wettbewerb`.
   - `Preis_EUR`, `Flaeche_m2` nur Zahlen ohne Tausenderpunkt (Dezimalkomma erlaubt), sonst leer.
   - `Status` = `neu` (bzw. `bekannt` bei Dublette). `/scout:fetch` setzt später `vertieft`.
   - `ID` = `S-<JJJJMMTT>-<NN>`; vorher höchste vorhandene Nummer des Tages in `.scout/leads/*.csv` suchen und weiterzählen.
2. `.scout/leads/<JJJJ-MM-TT>-<modus>.md` – Kopfzeile (Profil, Modus, Anzahl Suchen/Abrufe), dann Tabelle sortiert nach Score:
   `| ID | Typ | Name | Ort | Kennzahlen | Score (F/A/Z/E) | Begründung | Quelle |`
   Darunter ein Abschnitt „Hinweise“ (nicht abrufbare Quellen, Wettbewerber, private Inserate als Anzahl).
3. **Dubletten:** Vorher alle `.scout/leads/*.csv` lesen; gleiche URL oder gleiche Firma + Ort = Dublette → nicht neu anlegen, sondern im Fazit als „bekannt: <ID>“ nennen.
4. Beide Dateien jeweils mit **einem** `Write` schreiben, nicht zeilenweise editieren.

**Im Chat** (Einsteiger lesen nur das):

```
<n> Leads gespeichert in .scout/leads/<datei>.md (+ .csv für Excel)
Top 5:
1. <ID> <Name>, <Ort> – Score <x> – <warum, ein Halbsatz> → <nächster Schritt>
…
Hinweise: <max. 2 Zeilen, z. B. Modus-Zuordnung, nicht abrufbare Quellen>
Weiter: /scout:fetch <beste ID>
```

## 7. Regeln (Recht & Fairness)

- **Portale:** Keine automatisierten Massenabrufe; Portale wie ImmoScout24 untersagen das in den AGB. Suchmaschinen-Ergebnisse und einzelne Detailseiten in normalem Umfang; robots.txt respektieren; Bot-Schutz nie umgehen.
- **Datenschutz (DSGVO):** Nur geschäftliche, öffentlich bereitgestellte Daten von Unternehmen und Gewerbetreibenden. Keine Privatpersonen recherchieren oder mit Namen/Kontakt anlegen – auch nicht Eigentümer aus Versteigerungen, Nachlässen oder privaten Inseraten. Private Inserate bei `objekte` dürfen als Objekt erscheinen, Anbieter dann nur `privat`, Kontakt nur über das Inserat.
- **Ansprache (UWG § 7):** Der Skill schreibt und versendet nichts. Einmal pro Lauf kurz erwähnen: Werbe-E-Mails brauchen vorherige Einwilligung; Kaltanrufe bei Unternehmen mindestens mutmaßliche, bei Privatpersonen ausdrückliche Einwilligung.
- Jede Zeile hat eine Quell-URL. Vermutungen als Vermutung kennzeichnen.
