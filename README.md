# Leestrainer-prototypes

**Live bekijken:** https://yusuf013.github.io/leestrainer-prototypes/

- Leestrainer Wereldreis: https://yusuf013.github.io/leestrainer-prototypes/wereldreis/
- Lees & Luister: https://yusuf013.github.io/leestrainer-prototypes/lees-en-luister/
- Lees & Luister – offline spraakherkenning (test): https://yusuf013.github.io/leestrainer-prototypes/lees-en-luister-offline/

Twee klikbare prototypes, gemaakt als concept-uitbreiding op Estafette (Zwijsen).
Elk prototype is **één los HTML-bestand** met alle code (HTML, CSS en JavaScript) erin.
Er is geen installatie of build-stap nodig.

| Map | Prototype | Wat het is |
|---|---|---|
| `wereldreis/` | **Leestrainer Wereldreis** | Woordenschat: leesboek kiezen (AVI-niveau, pakket A/B), wereldbol, verhalen met klikbare woorden en een leeractiviteit |
| `lees-en-luister/` | **Lees & Luister** | Leesmotivatie: het kind leest hardop, de software leest via de microfoon mee en speelt geluiden af bij sleutelwoorden |

## Lokaal draaien
Dubbelklik op een `index.html` om het in de browser te openen (Chrome of Edge).
De microfoon van **Lees & Luister** werkt alleen via `https://` of `localhost`. Lokaal kan dat zo:

```bash
python3 -m http.server 8000
# open http://localhost:8000/lees-en-luister/
```

## Testen met leerlingen
Gebruik de live-link (GitHub Pages, https) in **Chrome of Edge** op een Chromebook of laptop, bij voorkeur met koptelefoon.

> **Privacy:** buiten Claude gebruikt Lees & Luister de spraakherkenning van de browser. Chrome stuurt de spraak daarvoor naar de servers van Google.
> Overleg met de school en vraag ouders om toestemming voordat je met leerlingen test. Zet nooit namen, opnames of gegevens van leerlingen in deze repository.

## Waar pas je wat aan?

### Lees & Luister (`lees-en-luister/index.html`)
- **Het verhaal**: zoek naar `const STORY=`. Elke regel is een zin.
  Een geluid koppel je aan een woord met `{woord|cue}`, bijvoorbeeld `{apen|monkeys}`.
  Het geluid start zodra de leerling dat woord heeft uitgesproken.
- **Beschikbare cues** staan in `const CUES=` (icoon en naam) en `const SCENE=` (wat er in beeld en geluid gebeurt).
- **Geluiden** worden door de computer gegenereerd met de Web Audio API (object `Snd`), er zijn geen audiobestanden.
  Echte opnames toevoegen kan met `new Audio('apen.mp3').play()` in de juiste functie in `SCENE`.
- **Meeleesgevoeligheid**: functies `sim()` (hoe sterk een woord op het verwachte woord moet lijken) en `align()` (hoeveel woorden de software vooruit mag kijken, nu 4).
- **Spraakherkenning**: object `Rec`. Binnen Claude gebruikt het de dictation van Claude, daarbuiten de spraakherkenning van de browser (`webkitSpeechRecognition`, taal `nl-NL`).

### Leestrainer Wereldreis (`wereldreis/index.html`)
- **Verhalen, woorden en activiteiten**: zoek naar `const STORIES = [`.
  Een woordenschatwoord markeer je in de tekst met `{woord}` of `{weergave|sleutel}`.
  De uitleg staat bij `words:`. De activiteit (`match`, `fill` of `spot`) staat bij `activity:`.
- **Leesboeken (AVI-niveaus, pakketten)**: zoek naar `const BOOKS=[`. Een boek met `stories:[...]` is speelbaar, een boek met `preview:[...]` staat op "binnenkort".
- **Huisstijl**: de kleuren staan in het CSS-blok onder `HUISSTIJL` (`--purple`, `--zblue`, enzovoort).
- **3D-wereldbol**: Three.js en de kaartdata worden van een CDN geladen. Zonder internet valt de pagina terug op een eenvoudige wereldbol.

## Goed om te weten
- De verhalen, titels en thema's zijn **fictief**. Alleen de opbouw (groepen, M/E-niveaus, pakket A/B) is afgeleid van Estafette editie 3.
- Logo's en mascottes van Zwijsen zijn bewust niet gebruikt.
- De voortgang en het gekozen boek worden opgeslagen in de browser (`localStorage`) van degene die het gebruikt.

## Lees & Luister – offline spraakherkenning (testversie)
Map `lees-en-luister-offline/`. Hetzelfde prototype, maar de spraak wordt **op het apparaat zelf** herkend met [vosk-browser](https://github.com/ccoreilly/vosk-browser) (WebAssembly) en het Nederlandse model `vosk-model-small-nl-0.22`.

- **Waarom:** minder vertraging, en de spraak van het kind gaat niet naar een server (beter voor de AVG).
- **Woordenlijst:** de herkenner luistert standaard alleen naar woorden uit het verhaal. Dat maakt hem sneller en nauwkeuriger. Dit is uit te zetten in het startscherm.
- **Eerste keer:** het model (±40 MB) wordt gedownload en omgezet. Daarna staat het in de opslag van de browser (Cache API) en start het direct, ook zonder internet.
- **Model zelf hosten:** zet `vosk-model-small-nl-0.22.zip` in `lees-en-luister-offline/model/`. De pagina probeert die map eerst, en daarna de mirror op Hugging Face. Lukt beide niet, dan kan de gebruiker het zip-bestand zelf kiezen in het startscherm.
- **Meten:** naast de herkende tekst staat ⚡ de gemiddelde tijd van beginnen met praten tot het eerste herkende woord. Gebruik dit om te vergelijken met de gewone versie.
- **Werkt alleen** via https (GitHub Pages) of `localhost`, in een recente Chrome of Edge. Niet in een Claude-artifact, omdat die de microfoon blokkeert.

## Plan: samenvoegen tot één geheel
Het idee is om Lees & Luister later als **leesmodus** in de verhaalpagina van de Wereldreis op te nemen:

> leesboek kiezen → bestemming → verhaal lezen (stil, of **hardop met geluid**) → woorden ontdekken → opdracht → stempel

Wat daarvoor nodig is:
1. Geluidsmomenten in de Wereldreis-verhalen markeren met een eigen syntax (bijvoorbeeld `[[olifanten|trumpet]]`), los van de woordenschat-markering `{woord}`.
2. Geluiden per bestemming (savanne, stad, Japan, …), het liefst met echte opnames.
3. De code opsplitsen in losse modules (wereldbol, verhaal, meelezen, geluiden, activiteiten), zodat namen niet botsen en meerdere mensen tegelijk kunnen werken. Eventueel overstappen naar React, zoals in de oorspronkelijke opdracht.
