# Casa del Paradiso

Flöden för stugans Homey (Homey Bridge, "Casa del Paradiso"). Lägenhetens Homey Pro finns i repot [Casa-del-Kville](https://github.com/bonedaw/Casa-del-Kville).

`Flows/` innehåller ett JSON-dokument per flöde, exporterat från Homey (avancerade flöden med `cards`, standardflöden med `trigger`/`conditions`/`actions`). Filnamnet är flödets namn (`/` ersatt med `-`).

## Koppling till lägenheten (Pro)

Stugan och Pro pratar med varandra via Homey-webhooks (`webhook.homey.app/<homeyId>/<händelse>_a77168457227`).

Stugan skickar till Pro:
- **Larm** – brand, läckage och inbrott (`larm_…?tag=brand|lackage_kok|lackage_badrum|lackage_loftet|inbrott`) från Brandalarm, Läckagesensorer och Larm.
- **Puls** varje hel timme och temperatur/luftfuktighet (ST Puls till lägenheten).
- **Vakthund** – svarar `pong` på Pros `ping` (ST Vakthund svar till lägenheten).
- **Elpris-färg** (ST Elpris till lägenheten) och **framme/avbruten förvärmning** (ST Framme, ST Förvärmning avbruten, ST Vi åker).

Pro skickar till stugan: mörker (ST Mörkt från Pro), väder/regn (ST Väder från Pro), påsk/midsommar (ST Kalender från Pro), elpris/frost/mulet/vädra (ST Prognos från Pro) och "vi åker" (ST Vi åker till stugan).

Make/Google Sheet används fortfarande för loggning och den dagliga batteri-/miljösammanställningen (Export_To_Sheets m.fl.), men larm går inte längre den vägen till Pro.

## Kända lösa trådar i Homey (2026-11-24)

- **Rullgardin hallen**: triggern `windowcoverings_set_changed` pekar även på kortet `d6dc150c-…`, som inte finns.
- **Hemma /Borta**: `someone_enter` och ALL-kortet pekar på `ed4874fa-…`, som inte finns. ALL-kortet väntar därför på en ingång som aldrig kommer, så stämningen (mood `7921a075-…`) vid hemkomst sätts sannolikt aldrig.
