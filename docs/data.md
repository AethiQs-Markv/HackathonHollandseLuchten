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

> The metadata contains **no** location, municipality, or link to a reference/KNMI station. The location is part of `data_sensors`; link a kit to a reference station or weather station yourself by distance (see [pitfalls](#7-conventions-and-pitfalls)).

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

Hourly, manually validated weather observations from the KNMI dataset [Hourly in-situ meteorological observations (validated)](https://dataplatform.knmi.nl/dataset/hourly-in-situ-meteorological-observations-validated-1-0) (variable descriptions: [KNMI dataset documentation](https://dataplatform.knmi.nl/dataset/docs/hourly-in-situ-meteorological-observations-validated-1-0)). The source consists of one NetCDF file per hour with all stations (`hourly-observations-validated-YYYYMMDD-HH.nc`, KNMI Open Data API). For the hackathon the files from **2020-01-01 onwards** are combined into one table, keeping only the variables below.

> [!NOTE]
> **File:** the table `weather_hourly` is stored in the `data` folder as [`data/knmi_weather_data.parquet`](../data/knmi_weather_data.parquet). It contains 3,702,647 rows for 70 stations and 59,832 hours, from 2020-01-01 01:00 to 2026-10-29 00:00 UTC.
- **Different averaging periods:** according to the KNMI documentation, `T` and `U` are the value of the **past 1 minute** before the end of the hour, `DD` is the mean of the **past 10 minutes** and only `FH` is the mean over the **whole hour**. The dataset has no hourly mean of temperature, humidity or wind direction.
- **Indicative hourly values:** `T`, `U` and `DD` are therefore a snapshot at the end of each hour, not an hourly average. With this many hours (almost 60,000 per station) they are a good *indication* of the weather at the sensor location, but they are not exact hourly means. Hourly means could only be calculated from the KNMI [10-minute dataset](https://dataplatform.knmi.nl/dataset/docs/10-minute-in-situ-meteorological-observations-1-0) (`t10`, `rh10`, `dd`/`ff`), which is not part of this release.
- **Variables not included:** the source contains more variables (for example `T10N`, `TD`, `FF`, `FX`, `P`, `Q`, `SQ`, `RH` (precipitation), `DR`, `VV`, `N`, `WW`, `W1`–`W6`, `IX`, `EE`). They were left out of the hackathon table; see the KNMI documentation if you need them.

### Table `weather_hourly` – one row per station × hour

The station identifier, name and location are part of every row, so there is no separate KNMI station metadata table. The sensor tables do not contain a KNMI station code: link a kit to the nearest KNMI station using the sensor `latitude`/`longitude` and the station `lat`/`lon`.

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
| **Time zones** | Sensor data (`timestamp_from`/`timestamp_to`), Luchtmeetnet API and KNMI (`time`): **UTC**. Luchtmeetnet validated and provisional files: fixed **UTC+1** (`+01:00`, also in summer, so not Dutch local time). Convert everything to one time zone (UTC is advised) before joining; a one-hour shift is a classic error. |
| **Hour labelling** | Sensor data has an explicit start and end per interval. Luchtmeetnet files have `begindatumtijd` and `einddatumtijd`. KNMI `time` and Luchtmeetnet API `timestamp_measured` mark the **end** of the hour. Align the intervals (for example on the end of the hour) before comparing. |
| **Missing values** | Sensor data: 🚧 coding to be confirmed. Reference files: missing or rejected hours are **absent rows**. KNMI: `NaN` (some stations do not measure every variable). Some exports use `NA` or sentinel values such as `-999`; check each file. |
| **Special wind values** | KNMI `DD`: `0` = calm, `990` = variable. Exclude both from wind-direction analysis. Wind direction is where the wind comes **from**. |
| **KNMI station names** | Use `wsi` as station key. `stationname` (and for a few stations `lat`/`lon`) can change over time. |
| **Sensor location** | Coordinates are in `data_sensors` and can change over time (kit moved). Kits have no station links: use distance to the reference stations and KNMI stations. Treat `latitude = 0` and `longitude = 0`, if present, as an unknown location. |
| **Raw vs. calibrated** | `pm25_kal` is calibrated by RIVM, `pm25_raw` is the raw sensor output; PM10 only exists raw. State which one you use. Reference stations have no calibrated variant. |
| **Sensor types** | Different PM sensor hardware behaves differently. Use `metadata_sensors` (`pm25_sensor`, `pm10_sensor`, …) in your analysis. |
| **PM10** | Low-cost sensors cannot measure PM10 reliably; treat as indicative. |
| **Negative and extreme values** | Both occur in sensor data and (small negatives) in reference data. Peaks > 100 µg/m³ are usually artefacts, except for events like New Year's fireworks. See [data tips](./tips.md#4-data-cleaning). |
| **Reference station ≠ truth everywhere** | Stations differ in type (traffic, industrial, regional background), instrument and distance to a sensor. |
| **Units** | Sensor PM in µg/m³, temperature in °C, humidity in %, pressure in hPa. KNMI (NetCDF): temperature in °C, humidity in %, wind speed in m/s, wind direction in degrees; **not** in tenths. |
| **Humidity and temperature** | Sensor `rh` and `temp` are measured by the kit; the KNMI equivalents are `U` and `T` (1.50 m, value of the past minute at the end of the hour, not an hourly mean) at a weather station that can be several kilometres away. |