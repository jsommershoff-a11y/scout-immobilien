# scout – Immobilien-Akquise mit Claude Code

Drei Skills für alle, die in der Immobilienwirtschaft **Objekte, Makler und Marktteilnehmer** finden wollen – Investoren, Bauträger, Makler auf Mandatssuche, Asset Manager.

| Befehl | Was er macht |
|---|---|
| `/scout:setup` | Legt dein Suchprofil an: Rolle, Region, Assetklassen, Ticket, Kennzahlen, Sonderlagen, Ausschlüsse |
| `/scout:search` | Findet Angebote, Off-Market-Signale (Zwangsversteigerungen, Insolvenzen, Bauland), Makler und weitere Marktteilnehmer und bewertet sie mit einem Score von 0 bis 100 |
| `/scout:fetch` | Vertieft einen Lead: Exposé oder Firmenseite auslesen, Kaufpreisfaktor, Rendite und €/m² berechnen, Red Flags, Impressum, nächster Schritt |

## Installation (zwei Befehle in Claude Code)

```text
/plugin marketplace add jsommershoff-a11y/scout-immobilien
/plugin install scout@scout-immobilien
```

Danach Claude Code neu starten oder `/reload-plugins` ausführen.

## In 60 Sekunden loslegen

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

## Suchmodi von `/scout:search`

- `objekte`: Angebote auf Portalen, Makler- und Bankenseiten
- `signale`: Off-Market-Hinweise wie Zwangsversteigerungen (zvg-portal.de), Insolvenzen, Bebauungspläne, Konzeptvergaben, Leerstand, Verkäufe der öffentlichen Hand
- `makler`: Makler als Quelle oder Kooperationspartner, priorisiert nach passenden Angeboten
- `markt`: Bauträger, Projektentwickler, Hausverwaltungen, Bestandshalter, Family Offices, Insolvenzverwalter, Finanzierer
- `alle` (Standard): alles oben, je Modus kompakt

## Voraussetzungen

- [Claude Code](https://claude.com/claude-code) mit Websuche (`WebSearch`/`WebFetch`)
- Optional: Scraping- oder CRM-Connectors, die du schon nutzt. `/scout:setup` erkennt sie und trägt sie ins Profil ein.

## Spielregeln (bitte lesen)

- **Portale:** Keine Massenabrufe bei Portalen, deren AGB das verbieten. Gelesen werden Suchergebnisse und einzelne Detailseiten, und robots.txt wird respektiert.
- **DSGVO:** Erfasst werden nur geschäftliche, öffentlich bereitgestellte Kontaktdaten von Unternehmen. Privatpersonen wie Eigentümer, Erben oder Mieter werden nicht recherchiert.
- **UWG § 7:** Die Skills versenden nichts. Werbe-E-Mails brauchen eine vorherige Einwilligung. Kaltanrufe bei Unternehmen brauchen mindestens eine mutmaßliche, bei Privatpersonen eine ausdrückliche Einwilligung.
- **Keine Beratung:** Die Kennzahlen sind eine Ersteinschätzung und weder Gutachten noch Rechts-, Steuer- oder Finanzierungsberatung.
- `.scout/` steht in `.gitignore`. Leads gehören nicht in öffentliche Repos.

## Mitmachen

Issues und Pull Requests sind willkommen, zum Beispiel weitere Quellen je Bundesland, Assetklassen-Checklisten oder CRM-Exporte. Die Skills sind reine Markdown-Dateien unter `plugins/scout/skills/`.

## Lizenz

MIT
