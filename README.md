# Publicatie Portfolio · De Baak

Eén statische HTML-pagina waarop De Baak intern bijhoudt welke publicatieformats (families en publicaties) er zijn en wat daarvan al geautomatiseerd is.

## Status

Actief in gebruik. De publicatiedata wordt dagelijks automatisch gebackupt via een GitHub Actions workflow (zie [Deployment](#deployment)), wat aangeeft dat de tool live draait tegen een Supabase-database.

## Waarom

De Baak heeft veel publicatieformats (rollenspellen, handouts, presentaties, enzovoort), verspreid over meerdere families. Deze pagina geeft in één oogopslag inzicht in welke publicaties er zijn, in welke familie ze horen en of ze al geautomatiseerd zijn, in ontwikkeling zijn, of nog moeten beginnen. Iedereen kan het overzicht bekijken; ingelogde De Baak-medewerkers kunnen families en publicaties toevoegen, bewerken en verwijderen.

## Snel starten

Vereisten:
- Een moderne browser (er wordt niets gebuild of geïnstalleerd).
- Een Supabase-project met de tabellen `families` en `pages`, met Row Level Security die lezen voor iedereen toestaat en schrijven alleen voor ingelogde gebruikers.

Configureer je eigen Supabase-project in `portfolio.html`:

```bash
# open portfolio.html in een editor en pas de configuratie aan
```

```js
const SUPABASE_URL = 'https://JOUW-PROJECT-ID.supabase.co';
const SUPABASE_KEY = 'jouw-anon-public-key';
```

Open de pagina daarna lokaal, bijvoorbeeld met een eenvoudige webserver.

Git Bash (Windows) en Linux:

```bash
python3 -m http.server 8080
# ga naar http://localhost:8080/portfolio.html
```

## Gebruik

- **Bekijken**: iedereen kan zoeken op titel, tag of familie (zoekbalk bovenin) en filteren via de tag-rail onder de hero.
- **Inloggen**: klik op "Inloggen om te bewerken" en log in met een De Baak e-mailadres via een eenmalige inloglink (Supabase magic link / OTP).
- **Beheren** (na inloggen): een familie toevoegen via "+ Familie", een publicatie toevoegen via "+ Publicatie". Elke publicatie heeft een familie, status (geautomatiseerd / in ontwikkeling / nog niet), titel, omschrijving, tags, optionele detailpagina-URL en tot vier links. Bewerken en verwijderen kan vanuit dezelfde formulieren.
- **Live updates**: wijzigingen van andere gebruikers verschijnen automatisch dankzij Supabase realtime-subscriptions op de tabellen `families` en `pages`.
- **Printen**: de pagina heeft een print-stylesheet die de topbar, modals en meldingen verbergt.

## Structuur

```
portfolio.html                                  # De volledige applicatie: markup, stijl en JavaScript in één bestand
Publicatie Portfolio · De Baak.html             # Door de browser opgeslagen kopie van portfolio.html (offline snapshot)
Publicatie Portfolio · De Baak_files/           # Bijbehorende gedownloade assets (o.a. de supabase-js library)
backups/families.json                           # Laatste automatische backup van de tabel `families`
backups/pages.json                              # Laatste automatische backup van de tabel `pages`
.github/workflows/backup-print-portfolio.yml    # Dagelijkse Supabase-backup naar de backups/-map
```

> TODO: aanvullen — waar `portfolio.html` daadwerkelijk gehost wordt (bijvoorbeeld intranet, Netlify of anders) staat niet in deze repository.

## Configuratie

`portfolio.html` bevat de Supabase-projectgegevens (URL en anon key) rechtstreeks in de scriptcode. Dit is bewust: het is de publieke anon-sleutel, en leesrechten zijn voor iedereen open via Row Level Security.

De backup-workflow gebruikt twee GitHub Actions repository secrets:

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`

## Deployment

`.github/workflows/backup-print-portfolio.yml` draait dagelijks om 03:00 UTC (en handmatig via "Run workflow"). De workflow haalt de tabellen `families` en `pages` op via de Supabase REST API, slaat ze op als JSON in `backups/`, en commit alleen als er iets gewijzigd is. Zo blijft er een git-geschiedenis van de portfoliodata bestaan, ook als Supabase zelf niet beschikbaar is.
