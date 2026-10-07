---
name: fetch
description: Vertieft einzelne Immobilien-Leads – ruft eine Exposé-/Angebots-URL, eine Makler- oder Firmenwebsite oder eine Lead-ID aus /scout:search ab, extrahiert strukturierte Daten (Objektdaten, Mieten, Anbieter, Impressum), rechnet Schnellkennzahlen (Kaufpreisfaktor, Bruttorendite, €/m²) und erstellt einen Lead-Steckbrief mit nächstem Schritt.
argument-hint: "<URL | Lead-ID | mehrere, durch Leerzeichen getrennt>"
allowed-tools: Read, Write, Edit, Glob, WebSearch, WebFetch
---

# /scout:fetch – Lead anreichern und bewerten

## Eingabe

`$ARGUMENTS` enthält eine oder mehrere URLs oder Lead-IDs (z. B. `S-20261007-03`). IDs löst du über `.scout/leads/*.csv` auf. Ohne Argument: nimm die drei höchstbewerteten, noch nicht vertieften Leads aus der neuesten Lead-Datei. Lies `.scout/profile.md`, falls vorhanden (für den Profil-Abgleich).

## Ablauf je Lead

1. **Abrufen.** Seite mit `WebFetch` laden. Klappt das nicht (Login, Bot-Schutz, AGB-Verbot), nicht umgehen: vermerke `nicht abrufbar` und bitte den Nutzer, das Exposé als PDF oder Text einzufügen.
2. **Typ erkennen** und passend extrahieren:

   **Objekt / Exposé**
   - Adresse bzw. Lage (so genau wie veröffentlicht), Assetklasse, Baujahr, Zustand, Energieausweis (Klasse, Bedarf/Verbrauch)
   - Wohn-/Nutzfläche, Grundstück, Einheiten (Wohnen/Gewerbe), Stellplätze
   - Kaufpreis, Ist-Nettokaltmiete p. a., Soll-Miete, Leerstand, Hausgeld/nicht umlagefähige Kosten
   - Provision, Anbieter, Datum der Anzeige
   - **Kennzahlen:** Kaufpreisfaktor = Kaufpreis ÷ Jahresnettokaltmiete; Bruttorendite = Jahresnettokaltmiete ÷ Kaufpreis; Preis je m²; grobe Kaufnebenkosten (Grunderwerbsteuer des Bundeslands + ca. 2 % Notar/Grundbuch + Provision) – Rechenweg zeigen, nur mit belegten Werten rechnen.
   - **Red Flags:** Erbbaurecht, Denkmal, Leerstand > 10 %, Sanierungsstau, Mietpreisbremse/Milieuschutz, auffällige Preisänderungen, fehlende Mietangaben.

   **Makler / Firma / Marktteilnehmer**
   - Firmenname, Rechtsform, Sitz, Registernummer, Vertretungsberechtigte laut **Impressum**
   - Bei Maklern: Erlaubnis nach § 34c GewO laut Impressum, Verbandsmitgliedschaft (IVD o. ä.)
   - Spezialisierung, Regionen, Anzahl/Art aktueller Angebote, Referenzen/Projekte
   - Geschäftliche Kontaktwege (Kontaktformular, zentrale E-Mail/Telefon laut Website)
   - Öffentliche Signale: Presse, neue Projekte, Stellenanzeigen (Wachstum), Bewertungen

3. **Profil-Abgleich:** Passt der Lead zu Region, Assetklasse, Ticket und Kennzahlen? Score aus `/scout:search` neu berechnen, wenn sich durch die Details etwas ändert.
4. **Nächster Schritt:** Ein konkreter Vorschlag, z. B. „Exposé und Mieterliste anfordern“, „Besichtigung anfragen“, „Kooperationsgespräch für Off-Market-Objekte ab 1 Mio. vorschlagen“, „verwerfen, weil Faktor 32“. Optional ein kurzer Gesprächsaufhänger (1–2 Sätze) auf Basis des Pitches aus dem Profil – nur als Entwurf, nichts wird versendet.

## Ausgabe

- Steckbrief je Lead in `.scout/leads/details/<ID oder Domain>.md` mit den Abschnitten: Kurzfazit · Daten · Kennzahlen (mit Rechenweg) · Red Flags · Kontakt (geschäftlich) · Nächster Schritt · Quellen.
- Status-Spalte in der zugehörigen CSV auf `vertieft` setzen und den neuen Score eintragen.
- Im Chat: pro Lead 3–5 Zeilen (Fazit, Schlüsselkennzahl, Red Flag, nächster Schritt).

## Regeln

- Nur veröffentlichte Daten verwenden; fehlende Werte als `k. A.` markieren, nie schätzen ohne es als Schätzung zu kennzeichnen.
- Keine Privatpersonen recherchieren (Eigentümer, Mieter, Erben). Personen nur in ihrer geschäftlichen Rolle laut Impressum/Firmenwebsite.
- Keine Umgehung von Logins, Paywalls oder Bot-Schutz.
- Keine Rechts-, Steuer- oder Finanzierungsberatung; Kennzahlen sind eine Ersteinschätzung, kein Gutachten.
- Nichts versenden – Ansprache-Entwürfe bleiben Entwürfe.
