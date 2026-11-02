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
| 2 | Official reference stations | `reference_station_metadata` (`data/reference_station_metadata.csv`) | one row per station (96 stations) | [Luchtmeetnet metadata files](https://data.rivm.nl/data/luchtmeetnet/Metadata/) |
| 2 | Official reference stations | `reference_measurements` (`data/reference_measurements.parquet`) | one row per station × component × hour (PM10 and PM2.5, 2020 – September 2026, fixed UTC+1) | [Luchtmeetnet open data](https://data.rivm.nl/data/luchtmeetnet/) (validated 2020–2025, provisional 2026) |
| 3 | Weather | `weather_hourly` (`data/knmi_weather_data.parquet`) | one row per KNMI station × hour (UTC, end of the hour) | [KNMI hourly in-situ observations (validated)](https://dataplatform.knmi.nl/dataset/hourly-in-situ-meteorological-observations-validated-1-0), NetCDF |
| 4 | Resident questions | `resident_questions` (`data/resident_questions.csv`) | one row per question | Hollandse Luchten measurement groups |


---

## 2. Hollandse Luchten sensors

The Hollandse Luchten (HoLu) network consists of citizen-operated particulate-matter sensor kits in Noord-Holland. Each kit measures PM2.5 and sometimes PM10, temperature, relative humidity and atmospheric pressure. The data is supplied in two tables: `metadata_sensors` (the hardware of each kit) and `data_sensors` (the measurements, including the location of the kit at the time of the measurement).

### 2a. `metadata_sensors` – one row per sensor kit

| Column | Type | Description |
|---|---|---|
| `kit_id` | string | Unique identifier of the sensor kit. Foreign key from `data_sensors`. |
| `pm25_sensor` | string | Type of the particulate matter 2.5 µm sensor. |
| `pm10_sensor` | string | Type of the particulate matter 10 µm sensor. |
| `temp_sensor` | string | Type of the temperature sensor. |
| `rh_sensor` | string | Type of the relative humidity sensor. |
| `pres_sensor` | string | Type of the pressure sensor. |

> The metadata contains **no** location, municipality, or link to a reference/KNMI station. The location is part of `data_sensors`; link a kit to a reference station or weather station yourself by location (see [pitfalls](#6-conventions-and-pitfalls)).

### 2b. `data_sensors` – one row per sensor kit × measurement interval

| Column | Type | Unit | Description |
|---|---|---|---|
| `kit_id` | string | – | Unique identifier of the sensor kit. Foreign key to `metadata_sensors`. |
| `timestamp_from` | datetime (UTC) | – | **Start** of the measurement interval. |
| `timestamp_to` | datetime (UTC) | – | **End** of the measurement interval. |
| `pm25_raw` | float | µg/m³ | Measured PM2.5 concentration (raw sensor value). |
| `pm10_raw` | float | µg/m³ | Measured PM10 concentration (raw sensor value). |
| `pm25_kal` | float | µg/m³ | **Calibrated** PM2.5 concentration ([RIVM calibration method](https://www.samenmeten.nl/dataportaal/kalibratie-van-fijnstofsensoren)). |
| `pm10_kal` | float | µg/m³ | **Calibrated** PM10 concentration ([RIVM calibration method](https://www.samenmeten.nl/dataportaal/kalibratie-van-fijnstofsensoren)). |
| `temp` | float | °C | Measured ambient air temperature. |
| `rh` | float | % | Measured relative humidity. |
| `pres` | float | hPa | Measured atmospheric pressure. |
| `latitude` | float | degrees | Latitude of the sensor, WGS84. |
| `longitude` | float | degrees | Longitude of the sensor, WGS84. |

---

## 3. Official reference measurements

Hourly PM10 and PM2.5 measurements from the official Dutch air-quality network (Luchtmeetnet: RIVM, GGD Amsterdam, DCMR, Provincie Noord-Holland, OMWB, …). These can be used as a reference/benchmark for analyzing the HoLu sensors or for challenge 2/3 as extra data. The data comes from the [Luchtmeetnet open data](https://data.rivm.nl/data/luchtmeetnet/) download, for the years **2020 up to and including September 2026**, and is combined into two files in the `data` folder.
There are 96 stations, all in the Netherlands. The measurements contain only PM10 and PM2.5.

### 3a. `reference_station_metadata` – one row per station

The columns have the original Luchtmeetnet names ([Luchtmeetnet readme (PDF)](https://data.rivm.nl/data/luchtmeetnet/readme.pdf)). Location, name and height come from `luchtmeetnet_meetlocaties.csv`; `gebiedstype` and `stationstype` come from `luchtmeetnet_meetreeksen.csv`.

| Column | Type | Description |
|---|---|---|
| `meetlocatie_id` | string | Station number, e.g. `NL49701`. Key; join with `reference_measurements.meetlocatie_id`. |
| `meetlocatie_naam` | string | Name of the station: street name and city name separated by a `-`, e.g. `Amsterdam-Einsteinweg`. |
| `meetlocatie_plaatsnaam` | string | City name. |
| `breedtegraad` | float | Latitude of the sensor, WGS84.|
| `lengtegraad` | float | Longitude of the sensor, WGS84. |
| `hoogte` | float | Height of the ground level at the station in metres relative to NAP (Amsterdam Ordnance Datum), derived from the Actueel Hoogtebestand Nederland (AHN) (−4 to 112 m). |
| `meetlocatie_begindatumtijd` | string | Start of the station, ISO 8601 with fixed UTC+1 offset (e.g. `2013-12-21T00:00:00+01:00`). |
| `meetlocatie_einddatumtijd` | string | End of the measurements of the station, same format. **Empty for 93 stations** (still active); filled for 3 stations that stopped. |
| `gebiedstype` | string | Area type according to EU directive 2008/50/EG: `stad` (urban, 57 stations) or `regionaal` (regional, 39). |
| `stationstype` | string | Station type according to EU directive 2008/50/EG: `achtergrond` (background, 52), `verkeer` (traffic, 29) or `industrie` (industrial, 15). Important when judging how comparable a sensor is with the station. |

### 3b. `reference_measurements` – one row per station × component × hour

This follows the layout of the [validated Luchtmeetnet files](https://data.rivm.nl/data/luchtmeetnet/Vastgesteld-jaar/2025/) (one file per component and year, e.g. `2025_PM25.csv`) with the original Dutch column names. The files for 2020–2025 are the validated yearly files; the files for 2026 (January–September) are the *indicative* monthly files (`Actueel-jaar/`). 

| Column | Type | Description |
|---|---|---|
| `meetreeks_id` | string | Id of the measurement series: `{station}_{component}_{matrix}`, e.g. `NL01485_PM2.5_lucht`. |
| `meetlocatie_id` | string | Station number. Foreign key to `reference_station_metadata`. |
| `component` | string | Measured component: `PM10` or `PM2.5`. |
| `meetopstelling_id` | string | Instrument used: `Bstralingsverzw_BAM1020`, `Bstralingsverzw_andersenFH62IR` or `aero_spectromet_FIDAS200`. Different instruments have different accuracy, and the instrument of a station can change over time. |
| `meetduur` | string | Period over which is measured, always `uur` (hour). |
| `eenheid` | string | Unit, always `µg/m³`. |
| `begindatumtijd` | string | **Start** of the hour, ISO 8601 with a **fixed UTC+1 offset** (e.g. `2020-01-01T00:00:00+01:00`). The offset is `+01:00` all year round, also in summer (no `+02:00`). |
| `einddatumtijd` | string | **End** of the hour, same format. |
| `waarde` | float | Value: hourly mean concentration in µg/m³. |

---

## 4. Weather data (KNMI)

Hourly, manually validated weather observations from the KNMI dataset [Hourly in-situ meteorological observations (validated)](https://dataplatform.knmi.nl/dataset/hourly-in-situ-meteorological-observations-validated-1-0) (variable descriptions: [KNMI dataset documentation](https://dataplatform.knmi.nl/dataset/docs/hourly-in-situ-meteorological-observations-validated-1-0)). For the hackathon the files from **2020-01-01 onwards** are combined into one table, keeping only the variables below.

- **Different averaging periods:** according to the KNMI documentation, `T` and `U` are the value of the **past 1 minute** before the end of the hour, `DD` is the mean of the **past 10 minutes** and only `FH` is the mean over the **whole hour**. The dataset has no hourly mean of temperature, humidity or wind direction.
- **Indicative hourly values:** `T`, `U` and `DD` are therefore a snapshot at the end of each hour, not an hourly average. Hourly means could only be calculated from the KNMI [10-minute dataset](https://dataplatform.knmi.nl/dataset/docs/10-minute-in-situ-meteorological-observations-1-0) (`t10`, `rh10`, `dd`/`ff`), which is not part of this release.
- **Variables not included:** the source contains more variables (for example `T10N`, `TD`, `FF`, `FX`, `P`, `Q`, `SQ`, `RH` (precipitation), `DR`, `VV`, `N`, `WW`, `W1`–`W6`, `IX`, `EE`). They were left out of the hackathon table; see the KNMI documentation if you need them.

### Table `weather_hourly` – one row per station × hour

The station identifier, name and location are part of every row, so there is no separate KNMI station metadata table.

| Column | Type | Unit | Description | KNMI standard name |
|---|---|---|---|---|
| `station` | string | – | Station identifier (WIGOS id), the index of the NetCDF file. Identical to `wsi`. | `platform_id` |
| `time` | datetime (UTC, no time zone) | – | **End** of the hourly measurement interval. | `time` |
| `wsi` | string | – | Station WSI (WIGOS station identifier), e.g. `0-20000-0-06240` (Schiphol). Use this as the station key. The last five digits are the WMO station number: `06240` = KNMI station number (`STN`) `240`. | `platform_id` |
| `stationname` | string | – | Station name. Can differ over time for the same station (see notes). | `platform_name` |
| `lat` | float | degrees north | Station latitude (WGS84). | `latitude` |
| `lon` | float | degrees east | Station longitude (WGS84). | `longitude` |
| `T` | float | °C | Air temperature at 1.50 m: the value of the **past 1 minute** (a snapshot at the end of the hour, not an hourly mean). | `air_temperature` |
| `U` | float | % | Relative humidity at 1.50 m: the value of the **past 1 minute**, at the time of observation (not an hourly mean). | `relative_humidity` |
| `DD` | float | degrees | Mean wind direction of the **past 10 minutes** (not an hourly mean), representative for 10 m height; the direction the wind blows **from** (360 = north, 90 = east, 180 = south, 270 = west, **0 = calm, 990 = variable**). | `wind_from_direction` |
| `FH` | float | m/s | **Hourly** mean wind speed, representative for 10 m height. | `wind_speed` |

---

## 5. Resident questions

Real questions that residents of Hollandse Luchten ask about their air quality. The table `resident_questions` is stored in the `data` folder as [`data/resident_questions.csv`](../data/resident_questions.csv). It contains 74 questions, **all in Dutch**.

| Column | Type | Description |
|---|---|---|
| `id` | integer | Unique id of the question. |
| `datum` | date | Date (`YYYY-MM-DD`) of the question. |
| `latitude` | float | Latitude (WGS84) of the location the question is about (52.22–52.50). |
| `longitude` | float | Longitude (WGS84) of the location the question is about (4.58–5.26).|
| `titel` | string | The question itself. |
| `beschrijving` | string | Free text with background and sometimes an expert answer. |

Notes:
- **Structure of `beschrijving`:** it contains the context of the question (why the resident wants to know this) and, for some questions, an expert answer that starts with `Antwoord:`.

---

## 6. Conventions and pitfalls

| Topic | What to know |
|---|---|
| **Time zones** | The data have different time zones. Convert everything to one time zone (UTC is advised) before joining. |
| **Hour labelling** | Sensor data and Luchtmeetnet have an explicit start and end per interval (`timestamp_from`/`timestamp_to`, `begindatumtijd`/`einddatumtijd`). KNMI `time` marks the **end** of the hour.|
| **Missing values** | Be aware that the data files (as any data) contain missing values. These can be empty fields and/or specific codes. |
| **Special wind values** | KNMI `DD`: `0` = calm, `990` = variable. Wind direction is where the wind comes **from**. |
| **Location** | For all sensors be aware that locations can change in the course of time.|
| **Raw vs. calibrated** | `pm25_kal` and `pm10_kal` are calibrated by RIVM, `pm25_raw` and `pm10_raw` are the raw sensor output. Make a well-considered choice which one you make and clearly state. |
| **Sensor types** | Different PM sensor hardware behaves differently. |
| **PM10** | Low-cost sensors cannot measure PM10 reliably; treat as indicative. |
| **Negative and extreme values** | Both occur in sensor data and (small negatives) in reference data. Peaks > 100 µg/m³ are usually artefacts, except for events like New Year's fireworks. |
