# Casa del Paradiso

Flöden för stugans Homey (Homey Bridge, "Casa del Paradiso"). Lägenhetens Homey Pro finns i repot [Casa-del-Kville](https://github.com/bonedaw/Casa-del-Kville).

`Flows/` innehåller ett JSON-dokument per flöde, exporterat från Homey (avancerade flöden med `cards`, standardflöden med `trigger`/`conditions`/`actions`). Filnamnet är flödets namn (`/` ersatt med `-`).

## Koppling till lägenheten (Pro)

Stugan och Pro pratar med varandra via Homey-webhooks (`webhook.homey.app/<homeyId>/<händelse>_a77168457227`).

Stugan skickar till Pro:
- **Larm** – brand, läckage och inbrott (`larm_…?tag=brand|lackage_kok|lackage_badrum|lackage_loftet|inbrott`) från Brandalarm, Läckagesensorer och Larm.
- **Puls** varje hel timme och temperatur/luftfuktighet inne, på loftet och i båda förråden (ST Puls till lägenheten). Pro räknar vädringsråd och mögelindex (MOLD_INDEX) på värdena.
- **Lämna stugan** – tre minuter efter att Stugläge blivit Borta skickas låset, loftdörren/fönstret, ventilen, TV:n, värmepumpen, fönster, larmet och lamporna (ST Lämna stugan-check). Pro pushar bara avvikelserna.
- **Vattnat** – när ventilen öppnas (Bevattning), så att Pro nollställer vattenbalansen.
- **Vakthund** – svarar `pong` på Pros `ping` (ST Vakthund svar till lägenheten).
- **Elpris-färg** (ST Elpris till lägenheten) och **framme/avbruten förvärmning** (ST Framme, ST Förvärmning avbruten, ST Vi åker).

Pro skickar till stugan: mörker (ST Mörkt från Pro), väder/regn och Vattna idag (ST Väder från Pro), påsk/midsommar (ST Kalender från Pro), elpris/frost/mulet/vädra (ST Prognos från Pro) och "vi åker" (ST Vi åker till stugan).

Make/Google Sheet används fortfarande för loggning och den dagliga batteri-/miljösammanställningen (Export_To_Sheets m.fl.), men larm går inte längre den vägen till Pro.

## Bevattning efter avdunstning (2026-11-25)

Bevattning vattnar 07:00 (april–september) i 10 minuter när *Vattna idag* är Ja. Pro (ET0_WATER, flödet Bevattning ET₀ till stugan 💧) räknar 06:40 vattenbalansen sedan senaste bevattningen: Open-Meteos referensavdunstning ET₀ minus 80 % av regnet. Vid minst 15 mm underskott och under 3 mm regn väntat idag sätts *Vattna idag* till Ja. Du kan också slå på den själv från dashboarden; Pro skriver aldrig över den till Nej. Den gamla regeln (måndag efter torr helg) är borttagen.

## Larmvakter (2026-11-25)

- **Brandalarm**: kritisk push var 10:e minut så länge brandlarmet pågår (variabeln *Brand pågår*, nollas av status `normal` eller Disarm). Push om brandvarnaren varit offline i 30 min (*Brandvarnare offline*).
- **Läckagesensorer**: kritisk push var 10:e minut så länge en sensor är blöt. Push när en sensor går under 20 % batteri. Bridge kan inte se tappad kontakt för läckagesensorerna utan falsklarm.
- **ST Mörkt från Pro**: push till båda om Pro inte hörts av på ca 3 timmar (en gång, spärr *Pro tyst varnat*) och när Pro hörs av igen.
- **Larm**: "LARM i stugan" från stugans egna sensorer är kritisk push.

## Kända lösa trådar i Homey (2026-11-24)

- **Rullgardin hallen**: triggern `windowcoverings_set_changed` pekar även på kortet `d6dc150c-…`, som inte finns.
- **Hemma /Borta**: ALL-kortet byggdes om 2026-11-25 och har nu bara de tre villkoren som ingång, så stämningen vid hemkomst kan sättas. Hemkomst-triggern pekar fortfarande på det borttagna kortet `ed4874fa-…`, vilket inte påverkar något.
