---
name: fetch
description: Vertieft einzelne Immobilien-Leads – liest eine Exposé-/Angebots-URL, eine Makler- oder Firmenwebsite oder eine Lead-ID aus /scout:search, extrahiert Objektdaten, Mieten, Anbieter und Impressum, rechnet Kaufpreisfaktor, Bruttorendite und €/m² mit Rechenweg und schreibt einen Steckbrief mit nächstem Schritt. Nutze diesen Skill für „schau dir S-20261007-03 genauer an“, „prüf dieses Exposé“, „was ist das für ein Makler“ oder wenn der Nutzer eine Immobilien-URL zur Bewertung einfügt.
argument-hint: "<Lead-ID | URL | mehrere durch Leerzeichen> (ohne Angabe: Top 3 der neuesten Liste)"
allowed-tools: Read, Write, Edit, Glob, WebSearch, WebFetch
---

# /scout:fetch – Lead anreichern und bewerten

## 1. Eingabe

- `$ARGUMENTS`: eine oder mehrere Lead-IDs (z. B. `S-20261007-03`) oder URLs; höchstens 3 pro Lauf (mehr → die ersten 3 bearbeiten und den Rest nennen).
- **IDs** über `.scout/leads/*.csv` auflösen (Spalte `ID`). Nicht gefunden → sagen und die 5 höchstbewerteten IDs der neuesten Liste zur Auswahl zeigen.
- **Ohne Argument:** die drei höchstbewerteten Leads mit `Status` = `neu` aus der neuesten CSV.
- **Profil** `.scout/profile.md` lesen. Fehlt es, trotzdem vertiefen, aber den Profil-Abgleich weglassen und am Ende auf `/scout:setup` hinweisen.
- **Ohne Webzugriff** (`WebFetch` nicht verfügbar): um den Exposé-Text oder ein PDF bitten und damit arbeiten.

## 2. Ablauf je Lead

1. **Abrufen.** Die Quell-URL mit `WebFetch` laden. Scheitert das (403, Login, Bot-Schutz, Seite offline): **nicht umgehen**. Dann höchstens zwei Ersatzwege: (a) dasselbe Objekt auf der Website des Anbieters per Websuche finden (`"<Titel>" <Anbieter>`), (b) Impressum des Anbieters direkt abrufen. Klappt beides nicht: `nicht abrufbar` vermerken und den Nutzer bitten, Exposé-Text oder PDF einzufügen.
2. **Typ erkennen und extrahieren.**

   **Objekt / Grundstück / Exposé**
   - Lage (so genau wie veröffentlicht), Assetklasse, Baujahr, Zustand, Energieausweis (Klasse, Bedarf/Verbrauch)
   - Wohn-/Nutzfläche, Grundstück, Einheiten, Stellplätze; bei Grundstücken: Baurecht (B-Plan/§ 34 BauGB, GRZ/GFZ), Erschließung, Altlasten-Hinweise
   - Kaufpreis, Ist-Nettokaltmiete p. a., Soll-Miete, Leerstand, Provision, Anbieter, Datum
   - **Kennzahlen mit Rechenweg, nur aus belegten Werten:**
     Kaufpreisfaktor = Kaufpreis ÷ Jahresnettokaltmiete · Bruttorendite = Jahresnettokaltmiete ÷ Kaufpreis · €/m² · Kaufnebenkosten = Grunderwerbsteuer des Bundeslands (NRW 6,5 %) + ca. 2 % Notar/Grundbuch + Provision laut Exposé. Fehlt ein Wert: Kennzahl `nicht berechenbar` und welcher Wert fehlt.
   - **Red Flags:** Erbbaurecht, Denkmal, Leerstand > 10 %, Energieklasse G/H, Sanierungsstau, Altlasten/Baulasten, Mietpreisbremse/Milieuschutz, fehlende Mietangaben, Filter aus dem Profil verletzt.

   **Makler / Firma / Marktteilnehmer**
   - Firma, Rechtsform, Sitz, Registernummer, Vertretungsberechtigte laut **Impressum**
   - Makler: § 34c-GewO-Erlaubnis laut Impressum, Verbandsmitgliedschaft
   - Spezialisierung, Regionen, aktuelle Angebote, Referenzen; geschäftliche Kontaktwege (zentrale E-Mail/Telefon, Kontaktformular)
   - Bei Typ `wettbewerb` (anderer Makler bei Ziel `mandate`): klar als Wettbewerber einordnen; nächster Schritt höchstens Kooperation/Benchmark.

3. **Profil-Abgleich und Score.** Score nach dem Schema aus `/scout:search` (Fit/Aktualität/Zugang/Exklusivität) neu berechnen und die Änderung begründen („50 → 44, weil Mietdaten fehlen“).
4. **Nächster Schritt:** genau ein konkreter Vorschlag (z. B. „Mieterliste und Ist-Mieten beim Anbieter anfordern“, „verwerfen: Faktor 32 > 20“). Optional ein Gesprächsaufhänger (1–2 Sätze) aus dem Profil-Pitch – nur als Entwurf.

## 3. Ausgabe

**Steckbrief** `.scout/leads/details/<ID>.md` (bei reiner URL: `<domain>-<JJJJMMTT>.md`), mit **einem** `Write`:

```markdown
# <ID> – <Name/Titel>, <Ort>
Stand: <JJJJ-MM-TT> · Score: <alt> → <neu> (F/A/Z/E)

## Kurzfazit
<2–3 Sätze: passt es zum Profil, was ist der Haken, Empfehlung>

## Daten
- … (fehlend = k. A.)

## Kennzahlen (Rechenweg)
- …

## Red Flags
- …

## Kontakt (geschäftlich)
- … (Quelle: Impressum-URL)

## Nächster Schritt
- …

## Quellen
- <URL> (abgerufen / nicht abrufbar: Grund)
```

**CSV aktualisieren:** Die CSV einmal lesen, in der Zeile des Leads `Status` = `vertieft` und `Score` (plus Teilpunkte) setzen und die Datei mit **einem** `Write` zurückschreiben. Fehlt in einer älteren CSV die Spalte `Status`, ergänze sie im selben Schritt für alle Zeilen (`neu`). Nie Zeile für Zeile editieren. Die `.md`-Liste nicht anfassen.

**Im Chat** pro Lead höchstens 5 Zeilen:

```
<ID> <Name> – Score <alt> → <neu>
Fazit: …
Schlüsselkennzahl: …
Red Flag: …
Nächster Schritt: …
```

Am Ende eine Zeile: `Steckbrief: .scout/leads/details/<ID>.md`.

## Regeln

- Nur veröffentlichte Daten; fehlende Werte `k. A.`; Schätzungen ausdrücklich als Schätzung kennzeichnen.
- Keine Privatpersonen recherchieren (Eigentümer, Mieter, Erben, Schuldner). Personen nur in ihrer geschäftlichen Rolle laut Impressum/Firmenwebsite.
- Keine Umgehung von Logins, Paywalls oder Bot-Schutz.
- Keine Rechts-, Steuer- oder Finanzierungsberatung; Kennzahlen sind eine Ersteinschätzung, kein Gutachten.
- Nichts versenden – Ansprache-Entwürfe bleiben Entwürfe.
