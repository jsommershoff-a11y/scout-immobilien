---
name: search
description: Sucht passend zum Scout-Suchprofil nach Immobilien-Akquisechancen – Angebote und Off-Market-Signale (Objekte), Makler, sowie weitere Marktteilnehmer (Bauträger, Projektentwickler, Hausverwaltungen, Bestandshalter, Family Offices, Insolvenzverwalter). Liefert eine bewertete Lead-Liste als Markdown und CSV. Nutze ihn für "finde Objekte/Makler/Investoren in …".
argument-hint: "[objekte|makler|markt|signale|alle] [optional: Ort oder Zusatz]"
allowed-tools: Read, Write, Edit, Glob, WebSearch, WebFetch
---

# /scout:search – Akquisechancen finden

## Vorbedingung

Lies `.scout/profile.md`. Fehlt sie, brich ab und empfiehl `/scout:setup`. Argumente aus `$ARGUMENTS` (Modus, Ort, Zusatz) überschreiben das Profil nur für diesen Lauf.

Modus aus dem ersten Wort von `$ARGUMENTS`; ohne Angabe: `alle` (aber höchstens ~10 Suchanfragen pro Modus, damit der Lauf schnell bleibt).

## Modi und Suchmuster

Formuliere die Suchanfragen konkret mit Region und Assetklasse aus dem Profil. Mehrere Anfragen parallel stellen.

### `objekte` – Angebote am Markt
- Portale nur über die Websuche erschließen (z. B. `"Mehrfamilienhaus kaufen" <Ort> site:immowelt.de`, `site:kleinanzeigen.de`, `site:immonet.de`), dazu Makler-Websites und regionale Banken-/Sparkassen-Immobilienseiten.
- Gewerbe: Gewerbeportale und Maklerhäuser der Region.
- Pro Treffer: Titel, Ort, Preis, Fläche, Einheiten, Ist-Miete (falls genannt), Anbieter, URL.

### `signale` – Off-Market- und Sondersituationen
- **Zwangsversteigerungen:** zvg-portal.de (Bundesland/Amtsgericht aus der Region), dazu Versteigerungskalender regionaler Anbieter.
- **Insolvenzen:** Hinweise auf Insolvenzverfahren von Immobilien-/Bauträgergesellschaften über Presse und insolvenzbekanntmachungen.de (nur als Signal; Ansprechpartner ist der Insolvenzverwalter, nicht der Schuldner).
- **Projekte/Bauland:** Bebauungsplanverfahren, Grundstücksausschreibungen und Konzeptvergaben der Kommunen, Bauvoranfragen in Ratsinformationssystemen.
- **Leerstand/Stillstand:** Presseberichte über stillgelegte Projekte, Leerstand, Verkaufsabsichten (Kirchen, Kommunen, Bahn, Bundesanstalt für Immobilienaufgaben/BImA).
- Signale sind Hinweise, keine Angebote. Markiere sie als `signal`.

### `makler` – Makler als Quelle oder Partner
- Verbandsverzeichnisse (IVD-Maklersuche, RDM/BVFI), Google-Maps-ähnliche Branchensuche via Websuche, Maklerprofile auf Portalen, Gewerbe-Maklerhäuser der Region.
- Pro Makler: Firma, Ansprechpartner (nur geschäftlich, laut Website/Impressum), Spezialisierung (Wohnen/Gewerbe/Anlage), Region, Anzahl sichtbarer Angebote im Suchprofil, Website, Impressum-URL.
- Priorisiere Makler, die bereits passende Objekte (Assetklasse + Ticket) vermarkten.

### `markt` – weitere Marktteilnehmer
Je nach Akquiseziel aus dem Profil:
- **Bauträger/Projektentwickler** (Grundstücksankauf, Globalverkauf, Forward Deals),
- **Hausverwaltungen/WEG-Verwalter** (kennen Verkaufsabsichten von Eigentümern),
- **Bestandshalter, Wohnungsunternehmen, Family Offices, Fonds** (als Käufer oder Verkäufer),
- **Insolvenzverwalter, Nachlassverwalter, Testamentsvollstrecker** (nur Kanzleien/Amtsträger, keine Privatpersonen),
- **Finanzierer, Gutachter, Notare** (als Tippgeber/Netzwerk).
Quellen: Firmenwebsites, Presse, Handelsregister-Bekanntmachungen (nur Firmendaten), Projektlisten, Fachmedien.

## Bewertung (Score 0–100)

Bewerte jeden Treffer transparent:
- **Profil-Fit (0–40):** Region, Assetklasse, Ticket, Kennzahlen.
- **Aktualität (0–20):** Datum der Anzeige/Meldung; älter als 90 Tage = max. 5.
- **Zugang (0–20):** direkter, legitimer Ansprechpartner vorhanden (geschäftliche Kontaktseite, Impressum)?
- **Exklusivität (0–20):** Off-Market/Signal > nur ein Anbieter > breit gestreut auf allen Portalen.
Begründe den Score in einem Halbsatz. Erfinde keine Zahlen: Fehlt ein Wert, schreibe `k. A.` und vergib dafür keine Punkte.

## Ausgabe

1. Datei `.scout/leads/<JJJJ-MM-TT>-<modus>.md` mit Tabelle, sortiert nach Score:
   `| ID | Typ | Name/Titel | Ort | Kennzahlen | Score | Begründung | Quelle |`
   Typ ∈ `objekt`, `signal`, `makler`, `markt`. IDs fortlaufend, z. B. `S-20261007-01`.
2. Dieselben Daten als `.scout/leads/<JJJJ-MM-TT>-<modus>.csv` (Semikolon-getrennt, UTF-8) für Excel/CRM-Import.
3. Bestehende Leads nicht duplizieren: vorher `.scout/leads/*.csv` nach gleicher URL oder Firma prüfen und Dubletten als `bekannt` markieren statt neu anzulegen.
4. Im Chat: Top 5 mit Score und je einem Satz zum nächsten Schritt, dann der Hinweis `/scout:fetch <ID oder URL>` zum Vertiefen.

## Regeln (Recht & Fairness)

- **Portale:** Keine automatisierten Massenabrufe von Portalen, deren AGB das untersagen (z. B. ImmoScout24). Nutze Suchmaschinen-Ergebnisse und einzelne Detailseiten in normalem Umfang; robots.txt respektieren.
- **Datenschutz (DSGVO):** Nur geschäftliche, öffentlich bereitgestellte Kontaktdaten von Unternehmen und Gewerbetreibenden erfassen. Keine Privatpersonen (z. B. Eigentümer aus Versteigerungen oder Nachlässen) recherchieren oder anlegen.
- **Ansprache (UWG § 7):** Der Skill schreibt keine Nachrichten und versendet nichts. Weise darauf hin: Werbe-E-Mails brauchen eine vorherige Einwilligung, Kaltanrufe bei Unternehmen mindestens eine mutmaßliche Einwilligung, bei Privatpersonen eine ausdrückliche.
- Quellen immer mit URL angeben. Nichts als Fakt darstellen, was nur vermutet ist.
