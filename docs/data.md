# Data description

This page describes every dataset that is supplied for the hackathon: what it contains, how the tables relate to each other and what each column means.


## Contents

1. [Overview](#1-overview)
2. [Hollandse Luchten sensors](#2-hollandse-luchten-sensors) (metadata + measurements)
3. [Official reference measurements](#3-official-reference-measurements) (metadata + measurements)
4. [Weather data (KNMI)](#4-weather-data-knmi)
5. [Resident questions](#5-resident-questions)
6. [Conventions and pitfalls](#6-conventions-and-pitfalls)

---

## 1. Overview

| # | Dataset | Table | Granularity | Original source |
|---|---|---|---|---|
| 1 | Hollandse Luchten sensors | `metadata_sensors` | one row per sensor kit | Hollandse Luchten / RIVM, supplied via Zenodo |
| 1 | Hollandse Luchten sensors | `data_sensors` | one row per sensor kit × measurement interval | Hollandse Luchten / RIVM, supplied via Zenodo |
| 2 | Official reference stations | `reference_station_metadata` | one row per station | [Luchtmeetnet open API](https://api.luchtmeetnet.nl/open_api) and [Luchtmeetnet metadata files](https://data.rivm.nl/data/luchtmeetnet/Metadata/) |
| 2 | Official reference stations | `reference_measurements` | one row per station × component × hour | [Luchtmeetnet open data (validated)](https://data.rivm.nl/data/luchtmeetnet/) |
| 3 | Weather | `weather_station_metadata`, `weather_hourly` | one row per KNMI station / per station × hour | [KNMI hourly observations](https://www.knmi.nl/nederland-nu/klimatologie/uurgegevens) ([custom download](https://www.daggegevens.knmi.nl/klimatologie/uurgegevens)) |
| 4 | Resident questions | `resident_questions` | one row per question | Hollandse Luchten measurement groups |


---

## 2. Hollandse Luchten sensors

The Hollandse Luchten (HoLu) network consists of citizen-operated particulate-matter sensor kits in Noord-Holland. Each kit measures PM2.5 and sometimes PM10 and, besides that, temperature, relative humidity and atmospheric pressure. The data is supplied in two tables: `metadata_sensors` (the hardware of each kit) and `data_sensors` (the measurements, including the location of the kit).

### 2a. `metadata_sensors` – one row per sensor kit

| Column | Type | Description |
|---|---|---|
| `kit_id` | string | Unique identifier of the sensor kit. Foreign key from `data_sensors`. |
| `pm25_sensor` | string | Type of the particulate matter 2.5 µm sensor. |
| `pm10_sensor` | string | Type of the particulate matter 10 µm sensor. |
| `temp_sensor` | string | Type of the temperature sensor. |
| `rh_sensor` | string | Type of the relative humidity sensor. |
| `pres_sensor` | string | Type of the pressure sensor. |

> The metadata contains **no** location, municipality, or link to a reference/KNMI station. The location is part of `data_sensors`; link a kit to a reference station or weather station yourself by distance (see [pitfalls](#7-conventions-and-pitfalls)).

### 2b. `data_sensors` – one row per sensor kit × measurement interval

| Column | Type | Unit | Description |
|---|---|---|---|
| `kit_id` | string | – | Unique identifier of the sensor kit. Foreign key to `metadata_sensors`. |
| `timestamp_from` | datetime (UTC) | – | **Start** of the measurement interval. |
| `timestamp_to` | datetime (UTC) | – | **End** of the measurement interval. |
| `pm25_raw` | float | µg/m³ | Measured PM2.5 concentration (raw sensor value). |
| `pm10_raw` | float | µg/m³ | Measured PM10 concentration (raw sensor value). |
| `pm25_kal` | float | µg/m³ | **Calibrated** PM2.5 concentration ([RIVM calibration method](https://samenmeten.nl/dataportaal/kalibratie-van-fijnstofsensoren)). |
| `pm10_kal` | float | µg/m³ | **Calibrated** PM10 concentration ([RIVM calibration method](https://samenmeten.nl/dataportaal/kalibratie-van-fijnstofsensoren)). |
| `temp` | float | °C | Measured ambient air temperature. |
| `rh` | float | % | Measured relative humidity. |
| `pres` | float | hPa | Measured atmospheric pressure. |
| `latitude` | float | degrees | Latitude of the sensor, WGS84. |
| `longitude` | float | degrees | Longitude of the sensor, WGS84. |

---

## 3. Official reference measurements

Validated (and near-real-time) measurements from the official Dutch air-quality network (Luchtmeetnet: RIVM, GGD Amsterdam, DCMR, Provincie Noord-Holland, OMWB, …). These are the benchmark for judging the HoLu sensors. Included for PM2.5, PM10 and NO₂. 🚧 The selection of stations will be confirmed at release.

### 3a. `reference_station_metadata` – one row per station

Station location data comes from the [Luchtmeetnet metadata files](https://data.rivm.nl/data/luchtmeetnet/Metadata/) (`luchtmeetnet_meetlocaties.csv`: `meetlocatie_id`, `bron_id`, `meetlocatie_naam`, `meetlocatie_plaatsnaam`, `breedtegraad`, `lengtegraad`, `hoogte`, begin/end datetime). The station type, organisation and components are **not** in those files; they come from the Luchtmeetnet API.

| Column | Type | Description | Source field (Luchtmeetnet API `/stations/{number}`) |
|---|---|---|---|
| `station_id` | string | Station number, e.g. `NL49701`. Equals `meetlocatie_id` in the validated files. | `number` |
| `name` | string | Station name, e.g. `Zaandam-Wagenschotpad`. | `location` / `description.EN` |
| `station_type` | string | Station type, e.g. `Municipal`, `Regional`, `Industrial`, `Traffic`, `Other`. Important when judging how comparable a sensor is with the station. API only. | `type` |
| `latitude`, `longitude` | float | Location in WGS84 (EPSG:4326). | `geometry.coordinates[1]`, `[0]` |
| `municipality` | string | Municipality of the station. | `municipality` |
| `province` | string | Province (often empty). | `province` |
| `organisation` | string | Organisation operating the station, e.g. `GGD Amsterdam`, `RIVM`. | `organisation` |
| `components` | list of string | Components measured at this station, e.g. `PM25, PM10, NO2, NO`. | `components` |
| `year_start` | string | Year the station started measuring (may be empty). | `year_start` |

### 3b. `reference_measurements` – one row per station × component × hour

This follows the layout of the [validated Luchtmeetnet files](https://data.rivm.nl/data/luchtmeetnet/Vastgesteld-jaar/2025/) (one file per component and year, e.g. `2025_PM25.csv`; `;`-separated, `#`-comment header lines, `.` as decimal separator). Provisional data for the running year is in `Actueel-jaar/` (monthly files such as `2026_06_PM25.csv`). Details of every field are in the [Luchtmeetnet readme (PDF)](https://data.rivm.nl/data/luchtmeetnet/readme.pdf).

| Column | Type | Description |
|---|---|---|
| `meetreeks_id` | string | Id of the measurement series: `{station}_{component}_{matrix}`, e.g. `NL01485_PM2.5_lucht`. |
| `meetlocatie_id` → `station_id` | string | Station number. Foreign key to `reference_station_metadata`. |
| `bron_id` | string | Organisation that delivered the data, e.g. `LML` (RIVM national network), `DCMR`, `GGDA` (GGD Amsterdam), `PNH` (Provincie Noord-Holland), `OMWB`. |
| `accreditatienummer` | string | Accreditation number of the measuring organisation (e.g. `L224`, `L426`, `L520`) or `nvt` (not applicable). |
| `component` → `pollutant` | string | Measured component: `PM2.5`, `PM10`, `NO2`, … |
| `matrix` | string | Medium measured, `lucht` (air). |
| `meetopstelling_id` | string | Instrument used, e.g. `aero_spectromet_FIDAS200` or `Bstralingsverzw_BAM1020` (β-attenuation). Different instruments have different accuracy. |
| `meetduur` | string | Averaging period, `uur` (hour). |
| `eenheid` → `unit` | string | Unit, `µg/m³`. |
| `begindatumtijd` → `start_time` | datetime | Start of the hour, ISO 8601 with a **fixed UTC+1 offset** (e.g. `2025-01-01T00:00:00+01:00`). The offset is `+01:00` all year round, also in summer (no `+02:00`). |
| `einddatumtijd` → `end_time` | datetime | End of the hour, same fixed UTC+1 offset. |
| `waarde` → `value` | float | Hourly mean concentration. Negative values occur (measurement uncertainty; ≈3% of the 2025 PM2.5 rows). |
| `opm_code` | string | Remark code of the data provider; may be empty (usually is). |

> 🚧 Column names after the arrow (`→`) are the English names used in the hackathon release; the Dutch names are the original Luchtmeetnet names.

Only available, validated hours are exported: rejected or missing hours are **absent as rows** (they are not empty values), so the series can have gaps.

For the most recent (not yet validated) hours the [Luchtmeetnet API](https://api.luchtmeetnet.nl/open_api) returns, per station and component (`formula` = `PM25`, `PM10`, `NO2`, …): `station_number`, `formula`, `value`, `timestamp_measured`, `timestamp_measured_start`, `timestamp_measured_end` (all UTC; `timestamp_measured` equals the **end** of the hour).

---

## 4. Weather data (KNMI)

Hourly weather observations from KNMI weather stations. Source: [KNMI hourly data](https://www.knmi.nl/nederland-nu/klimatologie/uurgegevens) (zip files per station with at most 10 years of ASCII data) or the [custom download](https://www.daggegevens.knmi.nl/klimatologie/uurgegevens); an [EDR API with 10-minute data](https://dataplatform.knmi.nl/dataset/10-minute-in-situ-meteorological-observations-1-0) also exists, but requires an API key. Values are provided **as published by KNMI, i.e. in tenths** for most variables (0.1 m/s, 0.1 °C, …): divide by 10 where indicated.

Good to know about the KNMI data:
- KNMI checks and corrects the data on working days, usually within a few days and in some cases up to two weeks after the measurement. Recent values can still change.
- The hourly series can be inhomogeneous (station moves, changed methods) and are not suited for trend analysis.
- Each variable is either an **average/sum/maximum over the hour** (`FH`, `FX`, `SQ`, `Q`, `DR`, `RH`, `T10N`) or a value **at the time of observation** (the end of the hour: `T`, `TD`, `P`, `VV`, `N`, `U`) or over the **last 10 minutes** of the hour (`DD`, `FF`).

### 4a. `weather_station_metadata`

| Column | Description |
|---|---|
| `station_id` | KNMI station number (`STN`), e.g. `240` (Schiphol). |
| `longitude`, `latitude` | Station location (WGS84). |
| `altitude_m` | Station altitude in metres. |
| `name` | Station name. |

> The sensor tables do not contain a KNMI station code: link a kit to the nearest KNMI station using `latitude`/`longitude`.

### 4b. `weather_hourly` – one row per station × hour

| Column | Unit as published | Description |
|---|---|---|
| `STN` | – | KNMI station number. |
| `YYYYMMDD` | – | Date (UTC). |
| `HH` | – | Hour **1–24 UT**; hour 5 covers 04:00–05:00 UT (the hour *ending* at 05:00). |
| `DD` | degrees | Mean wind direction over the last 10 min of the hour; the direction the wind blows **from** (360 = north, 90 = east, 180 = south, 270 = west, **0 = calm, 990 = variable**). |
| `FH` | 0.1 m/s | Hourly mean wind speed. |
| `FF` | 0.1 m/s | Mean wind speed over the last 10 min of the hour. |
| `FX` | 0.1 m/s | Maximum wind gust (3-second mean wind speed) during the hour. |
| `T` | 0.1 °C | Temperature at 1.50 m at the time of observation. |
| `T10N` | 0.1 °C | Minimum temperature at 0.1 m (10 cm) in the preceding 6 hours (only filled at some hours). |
| `TD` | 0.1 °C | Dew-point temperature at 1.50 m at the time of observation. |
| `SQ` | 0.1 hour | Sunshine duration in the hour, calculated from global radiation (−1 = < 0.05 h). |
| `Q` | J/cm² | Global radiation in the hour. |
| `DR` | 0.1 hour | Precipitation duration in the hour. |
| `RH` | 0.1 mm | Hourly **precipitation** sum (−1 = < 0.05 mm). Not relative humidity: that is `U`. |
| `P` | 0.1 hPa | Air pressure reduced to mean sea level, at the time of observation. |
| `VV` | code | Horizontal visibility at the time of observation (0 = < 100 m, 1 = 100–200 m … 49 = 4.9–5 km, 50 = 5–6 km, 56 = 6–7 km … 80 = 30–35 km … 89 = > 70 km). |
| `N` | octants | Cloud cover at the time of observation (9 = sky invisible). |
| `U` | % | Relative humidity at 1.50 m at the time of observation. |
| `WW` | code 00–99 | Present weather code (observed visually or automatically), for the current weather or the weather in the past hour. |
| `IX` | code | How the weather code was determined: 1 = manned, using visual code; 2, 3 = manned, omitted (no significant weather / no data); 4 = automatic, using visual code; 5, 6 = automatic, omitted; 7 = automatic, using automated code. |
| `M`, `R`, `S`, `O`, `Y` | 0/1 | Occurrence of fog, rain, snow, thunder and ice formation in the preceding hour **and/or** at the time of observation. |

> Not every station measures every variable; empty values mean "not available". Weather stations are often several kilometres from the sensor, so wind and humidity are **indicative** for the sensor location. Note that the KNMI column `RH` (precipitation) is not the same as the sensor column `rh` (relative humidity).

---

## 5. Resident questions 🚧

Real questions that residents and measurement groups of Hollandse Luchten asked about their air quality. They are meant as test material for Challenge 3 (and as inspiration for the other challenges). The columns below are the **proposed** schema; the final file and column names will be published with the dataset.

| Column | Type | Description |
|---|---|---|
| `question_id` | integer | Unique id of the question. |
| `question_text` | string | The question, as asked (original wording). |
| `language` | string | Language of the question (`nl` or `en`). |
| `category` | string | Type of question, e.g. `user education`, `temporal comparison`, `location analysis`, `trend`, `anomaly`, `source investigation`, `threshold`, `forecast`. |
| `measurement_group` | string | Hollandse Luchten measurement group / area the question came from (e.g. Haarlem, IJmond, Zaanstad, Gooi en Vechtstreek). |
| `related_sensor_ids` | list of string | `kit_id`(s) the question refers to, if any. |
| `period_start`, `period_end` | date | Period the question refers to, if any. |
| `reference_answer` | string | Optional answer prepared by experts, to compare tool output with. |
| `source` | string | Where the question comes from (workshop, forum, e-mail, …). Personal data is removed. |

---

## 6. Conventions and pitfalls

| Topic | What to know |
|---|---|
| **Time zones** | Sensor data (`timestamp_from`/`timestamp_to`), Luchtmeetnet API and KNMI: **UTC** (KNMI: hour 1–24 UT). Luchtmeetnet validated and provisional files: fixed **UTC+1** (`+01:00`, also in summer, so not Dutch local time). Convert everything to one time zone (UTC is advised) before joining; a one-hour shift is a classic error. |
| **Hour labelling** | Sensor data has an explicit start and end per interval. Luchtmeetnet files have `begindatumtijd` and `einddatumtijd`. KNMI `HH` and Luchtmeetnet API `timestamp_measured` mark the **end** of the hour. Align the intervals (for example on the end of the hour) before comparing. |
| **Missing values** | Sensor data: 🚧 coding to be confirmed. Reference files: missing or rejected hours are **absent rows**. KNMI: empty field. Some exports use `NA` or sentinel values such as `-999`; check each file. |
| **Special wind values** | KNMI `DD`: `0` = calm, `990` = variable. Exclude both from wind-direction analysis. Wind direction is where the wind comes **from**. |
| **Sensor location** | Coordinates are in `data_sensors` and can change over time (kit moved). Kits have no station links: use distance to the reference stations and KNMI stations. Treat `latitude = 0` and `longitude = 0`, if present, as an unknown location. |
| **Raw vs. calibrated** | `pm25_kal` is calibrated by RIVM, `pm25_raw` is the raw sensor output; PM10 only exists raw. State which one you use. Reference stations have no calibrated variant. |
| **Sensor types** | Different PM sensor hardware behaves differently. Use `metadata_sensors` (`pm25_sensor`, `pm10_sensor`, …) in your analysis. |
| **PM10** | Low-cost sensors cannot measure PM10 reliably; treat as indicative. |
| **Negative and extreme values** | Both occur in sensor data and (small negatives) in reference data. Peaks > 100 µg/m³ are usually artefacts, except for events like New Year's fireworks. See [data tips](./tips.md#4-data-cleaning). |
| **Reference station ≠ truth everywhere** | Stations differ in type (traffic, industrial, regional background), instrument and distance to a sensor. |
| **Units** | Sensor PM in µg/m³, temperature in °C, humidity in %, pressure in hPa. KNMI values are mostly in tenths; KNMI `P` is sea-level pressure. |
| **Column name clash** | KNMI `RH` = precipitation (0.1 mm); sensor `rh` = relative humidity (%). KNMI `U` is relative humidity. |

## Try the sources yourself

```text
# Luchtmeetnet validated hourly data (one file per component and year) and station metadata
https://data.rivm.nl/data/luchtmeetnet/Vastgesteld-jaar/2025/2025_PM25.csv
https://data.rivm.nl/data/luchtmeetnet/Metadata/luchtmeetnet_meetlocaties.csv

# Reference station metadata and its latest PM2.5 (API)
https://api.luchtmeetnet.nl/open_api/stations/NL49701
https://api.luchtmeetnet.nl/open_api/measurements?station_number=NL49701&formula=PM25

# KNMI hourly data, station 240 (Schiphol), 2021-2030
https://cdn.knmi.nl/knmi/map/page/klimatologie/gegevens/uurgegevens/uurgeg_240_2021-2030.zip
```
