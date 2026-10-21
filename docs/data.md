# Data description

This page describes every dataset that is supplied for the hackathon: what it contains, how the tables relate to each other and what each column means.

> [!WARNING]
> TODO: at final release or earlier information of Elma we update!

## Contents

1. [Overview](#1-overview)
2. [Hollandse Luchten sensors](#2-hollandse-luchten-sensors) (metadata + measurements)
3. [Official reference measurements](#3-official-reference-measurements) (metadata + measurements)
4. [Weather data (KNMI)](#4-weather-data-knmi)
5. [Resident questions](#5-resident-questions)
6. [Known / reported sensor issues](#6-known--reported-sensor-issues)
7. [Conventions and pitfalls](#7-conventions-and-pitfalls)

---

## 1. Overview

| # | Dataset | Table | Granularity | Original source |
|---|---|---|---|---|
| 1 | Hollandse Luchten sensors | `sensor_metadata` | one row per sensor (device) | [Samen Meten API](https://api-samenmeten.rivm.nl/v1.0) (`Things`, `Locations`) |
| 1 | Hollandse Luchten sensors | `sensor_datastreams` | one row per sensor × measured quantity | Samen Meten API (`Datastreams`, `Sensors`, `ObservedProperties`) |
| 1 | Hollandse Luchten sensors | `sensor_measurements` | one row per datastream × timestamp (hourly) | Samen Meten API (`Observations`) |
| 2 | Official reference stations | `reference_station_metadata` | one row per station | [Luchtmeetnet open API](https://api.luchtmeetnet.nl/open_api) |
| 2 | Official reference stations | `reference_measurements` | one row per station × component × hour | [Luchtmeetnet open data (validated)](https://data.rivm.nl/data/luchtmeetnet/) |
| 3 | Weather | `weather_station_metadata`, `weather_hourly` | one row per KNMI station / per station × hour | [KNMI hourly observations](https://www.daggegevens.knmi.nl/klimatologie/uurgegevens) |
| 4 | Resident questions | `resident_questions` | one row per question | Hollandse Luchten measurement groups |
| 5 | Known sensor issues | `known_sensor_issues` | one row per reported issue | Hollandse Luchten / RIVM / measurement groups |

---

## 2. Hollandse Luchten sensors

The Hollandse Luchten (HoLu) network consists of citizen-operated particulate-matter sensors in Noord-Holland (mainly the current **SODAQ AIR** with a Sensirion SPS30, and older HoLu kits with a Nova SDS011), plus a smaller number of NO₂ sensors. Sensor data is stored by RIVM in a [SensorThings API](https://www.ogc.org/standards/sensorthings/) (OGC). The hackathon tables are a flattened version of it.

At the time of writing the API lists ~560 devices with `owner = Hollandse Luchten`; ~570 devices have at least one datastream. Observed quantities per device: PM2.5 and PM10 (raw and calibrated), temperature, relative humidity and, for a minority, NO₂ and pressure.

### 2a. `sensor_metadata` – one row per sensor (device)

| Column | Type | Description | Source field |
|---|---|---|---|
| `sensor_id` | integer | Unique numeric id of the device in the Samen Meten API. | `Things/@iot.id` |
| `sensor_name` | string | Unique device name, e.g. `HLL_hl_device_227`. This is the `station` name used in the Samen Analyseren Tool. | `Things/name` |
| `project` | string | Project the device belongs to (`Hollandse Luchten`). | `Things/properties/project` |
| `owner` | string | Organisation owning the device (mostly `Hollandse Luchten`; a few devices are `Luftdaten`/Sensor.Community devices that were added to the project). | `Things/properties/owner` |
| `municipality_code` | string | CBS municipality code (e.g. `375` = Beverwijk, `363` = Amsterdam). The text `None` means unknown/outside a municipality. | `Things/properties/codegemeente` |
| `latitude` | float | Latitude in WGS84 (EPSG:4326). **Rounded to ~3 decimals (≈100 m) for privacy**. `0` together with `longitude = 0` means the location is unknown (see [pitfalls](#7-conventions-and-pitfalls)). | `Locations/location/coordinates[1]` |
| `longitude` | float | Longitude in WGS84 (EPSG:4326). Same remarks as `latitude`. | `Locations/location/coordinates[0]` |
| `location_registered_at` | datetime (UTC) | Moment the current location was registered. This is **not** necessarily the installation date. | `HistoricalLocations/time` |
| `knmi_station` | string | Nearest KNMI weather station, formatted `knmi_06xxx` (xxx = KNMI station number). | `Things/properties/knmicode` |
| `pm25_close_ref_station` | string | Official reference station selected as *closest* comparison station for PM2.5 (Luchtmeetnet station number, e.g. `NL49570`; may also be a Belgian id such as `BE_43N132`). | `properties/pm25closecode` |
| `pm25_region_ref_station` | string | Reference station selected as *regional* comparison station for PM2.5. | `properties/pm25regiocode` |
| `pm25_city_ref_station` | string | Reference station selected as *urban* comparison station for PM2.5. | `properties/pm25stadcode` |
| `pm10_close_ref_station`, `pm10_region_ref_station`, `pm10_city_ref_station` | string | Same as above, for PM10. | `properties/pm10closecode`, `…regiocode`, `…stadcode` |
| `no2_close_ref_station`, `no2_region_ref_station`, `no2_city_ref_station` | string | Same as above, for NO₂. | `properties/no2closecode`, `…regiocode`, `…stadcode` |
| `nh3_close_ref_station`, `nh3_region_ref_station`, `nh3_city_ref_station` | string | Same as above, for NH₃. Empty for Hollandse Luchten devices. | `properties/nh3closecode`, `…regiocode`, `…stadcode` |

> 🚧 The exact meaning of *close / region / city* is inferred from the Samen Meten metadata (closest station, regional background station, urban background station) and will be confirmed by RIVM.

### 2b. `sensor_datastreams` – one row per sensor × measured quantity

One device has several datastreams, e.g. `HLL_hl_device_227-12-pm25` and `HLL_hl_device_227-12-pm25_kal`. The datastream name pattern is `{sensor_name}-{sensor_type_id}-{quantity}`.

| Column | Type | Description | Source field |
|---|---|---|---|
| `datastream_id` | integer | Unique id of the datastream. | `Datastreams/@iot.id` |
| `sensor_id` | integer | Foreign key to `sensor_metadata`. | `Datastreams/Thing/@iot.id` |
| `datastream_name` | string | Name of the datastream (`{sensor_name}-{sensor_type_id}-{quantity}`). | `Datastreams/name` |
| `quantity` | string | Measured quantity, see [quantity table](#quantities-observedproperties). | `ObservedProperties/name` |
| `quantity_description` | string | Dutch description of the quantity. | `ObservedProperties/description` |
| `quantity_definition_url` | string | URL to the definition of the quantity. | `ObservedProperties/definition` |
| `unit` | string | Unit symbol: `ug/m3` (= µg/m³), `C` (= °C), `%`, `hPa`. | `Datastreams/unitOfMeasurement/symbol` |
| `unit_definition_url` | string | QUDT definition of the unit. | `Datastreams/unitOfMeasurement/definition` |
| `sensor_type_id` | integer | Id of the sensor hardware type (the number in the datastream name). | `Sensors/@iot.id` |
| `sensor_type` | string | Hardware type that produced the measurement, e.g. `Sensirion SPS030` (PM, SODAQ AIR), `Nova SDS011` (PM, older HoLu kit), `DHT22` (temp/humidity), `Alphasense NO2`, `BME280`. | `Sensors/name` |
| `observed_area_lon`, `observed_area_lat` | float | Point location of the datastream (identical to the sensor location). | `Datastreams/observedArea` |

#### Quantities (`ObservedProperties`)

| `quantity` | Description | Unit | Remark |
|---|---|---|---|
| `pm25` | PM2.5, particulate matter < 2.5 µm (raw sensor value) | µg/m³ | Overestimates at high humidity and during peaks. |
| `pm25_kal` | PM2.5, **calibrated** by RIVM ([calibration method](https://samenmeten.nl/dataportaal/kalibratie-van-fijnstofsensoren)) | µg/m³ | Preferred for comparison with reference stations. |
| `pm10` | PM10, particulate matter < 10 µm (raw) | µg/m³ | Low-cost sensors cannot measure PM10 reliably. |
| `pm10_kal` | PM10, calibrated | µg/m³ | Idem. |
| `temp` | Temperature | °C | Measured inside the sensor housing, not a weather-station value. |
| `rh` | Relative humidity | % | Measured inside the sensor housing. |
| `no2` | Nitrogen dioxide | µg/m³ | Only a minority of the devices; see [sensor guidance](./tips.md). |
| `pres` | Atmospheric pressure | hPa | Only a few devices. |

### 2c. `sensor_measurements` – one row per datastream × timestamp

| Column | Type | Description | Source field |
|---|---|---|---|
| `observation_id` | integer | Unique id of the observation. | `Observations/@iot.id` |
| `datastream_id` | integer | Foreign key to `sensor_datastreams`. | `Observations/Datastream` |
| `timestamp_utc` | datetime (UTC) | Moment of the observation, **hourly**, ISO 8601 with `Z`, e.g. `2026-10-21T07:00:00.000Z`. 🚧 Whether this marks the start or the end of the averaging hour is being confirmed with RIVM. | `Observations/phenomenonTime` |
| `value` | float or null | Measured value in the unit of the datastream. Missing measurements are **null** (the row can still exist). Negative values and extreme values do occur. | `Observations/result` |

Notes:
- The SODAQ AIR measures every few minutes, but the published series is aggregated to **hourly** values.
- `resultTime` exists in the API but is always empty and is dropped.
- To get a wide table (one column per quantity), pivot on `quantity` after joining `sensor_datastreams`.

---

## 3. Official reference measurements

Validated and near-real-time measurements from the official Dutch air-quality network (Luchtmeetnet: RIVM, GGD Amsterdam, DCMR, …) and, for some sensors, Belgian stations. These are the benchmark for judging the HoLu sensors. Included for the stations that are referenced in `sensor_metadata`, for PM2.5, PM10 and NO₂.

### 3a. `reference_station_metadata` – one row per station

| Column | Type | Description | Source field (Luchtmeetnet API `/stations/{number}`) |
|---|---|---|---|
| `station_id` | string | Station number, e.g. `NL49701`. Join key with `sensor_metadata.*_ref_station`. | `number` |
| `name` | string | Station name, e.g. `Zaandam-Wagenschotpad`. | `location` / `description.EN` |
| `station_type` | string | Station type, e.g. `Municipal`, `Regional`, `Industrial`, `Traffic`, `Other`. Important when judging how comparable a sensor is with the station. | `type` |
| `latitude`, `longitude` | float | Location in WGS84 (EPSG:4326). | `geometry.coordinates[1]`, `[0]` |
| `municipality` | string | Municipality of the station. | `municipality` |
| `province` | string | Province (often empty). | `province` |
| `organisation` | string | Organisation operating the station, e.g. `GGD Amsterdam`, `RIVM`. | `organisation` |
| `components` | list of string | Components measured at this station, e.g. `PM25, PM10, NO2, NO`. | `components` |
| `year_start` | string | Year the station started measuring (may be empty). | `year_start` |

### 3b. `reference_measurements` – one row per station × component × hour

This follows the layout of the [validated Luchtmeetnet files](https://data.rivm.nl/data/luchtmeetnet/Vastgesteld-jaar/2025/) (`;`-separated, `#`-comment header lines). Details of every field are in the [Luchtmeetnet readme (PDF)](https://data.rivm.nl/data/luchtmeetnet/readme.pdf).

| Column | Type | Description |
|---|---|---|
| `meetreeks_id` | string | Id of the measurement series: `{station}_{component}_{matrix}`, e.g. `NL01485_PM2.5_lucht`. |
| `meetlocatie_id` → `station_id` | string | Station number. Foreign key to `reference_station_metadata`. |
| `bron_id` | string | Organisation that delivered the data (e.g. `DCMR`, `GGD`, `RIVM`). |
| `accreditatienummer` | string | Accreditation number of the measuring organisation. |
| `component` → `pollutant` | string | Measured component: `PM2.5`, `PM10`, `NO2`, … |
| `matrix` | string | Medium measured, `lucht` (air). |
| `meetopstelling_id` | string | Instrument used, e.g. `aero_spectromet_FIDAS200`. Different instruments have different accuracy. |
| `meetduur` | string | Averaging period, `uur` (hour). |
| `eenheid` → `unit` | string | Unit, `µg/m³`. |
| `begindatumtijd` → `start_time` | datetime | Start of the hour, ISO 8601 **with UTC offset** (e.g. `2025-01-01T00:00:00+01:00`). |
| `einddatumtijd` → `end_time` | datetime | End of the hour, with UTC offset. |
| `waarde` → `value` | float or empty | Hourly mean concentration. Empty when invalid/missing. |
| `opm_code` | string | Remark code of the data provider (empty = no remark). |

> 🚧 Column names after the arrow (`→`) are the English names used in the hackathon release; the Dutch names are the original Luchtmeetnet names.

For the most recent (not yet validated) hours the [Luchtmeetnet API](https://api.luchtmeetnet.nl/open_api) returns, per station and component (`formula` = `PM25`, `PM10`, `NO2`, …): `station_number`, `formula`, `value`, `timestamp_measured`, `timestamp_measured_start`, `timestamp_measured_end` (all UTC; `timestamp_measured` equals the **end** of the hour).

---

## 4. Weather data (KNMI)

Hourly weather observations from the KNMI weather stations that are linked to the sensors via `sensor_metadata.knmi_station`. Source: [KNMI hourly data](https://www.daggegevens.knmi.nl/klimatologie/uurgegevens) (an [EDR API with 10-minute data](https://dataplatform.knmi.nl/dataset/10-minute-in-situ-meteorological-observations-1-0) also exists, but requires an API key). Values are provided **as published by KNMI, i.e. in tenths** (0.1 m/s, 0.1 °C, …): divide by 10 where indicated.

### 4a. `weather_station_metadata`

| Column | Description |
|---|---|
| `station_id` | KNMI station number (`STN`), e.g. `240` (Schiphol). Equals `sensor_metadata.knmi_station` without the `knmi_06` prefix. |
| `longitude`, `latitude` | Station location (WGS84). |
| `altitude_m` | Station altitude in metres. |
| `name` | Station name. |

### 4b. `weather_hourly` – one row per station × hour

| Column | Unit as published | Description |
|---|---|---|
| `STN` | – | KNMI station number. |
| `YYYYMMDD` | – | Date (UTC). |
| `HH` | – | Hour **1–24 UTC**; hour 5 covers 04:00–05:00 UTC (the hour *ending* at 05:00). |
| `DD` | degrees | Mean wind direction in the last 10 min of the hour; the direction the wind blows **from** (360 = north, 90 = east, 180 = south, 270 = west, **0 = calm, 990 = variable**). |
| `FH` | 0.1 m/s | Hourly mean wind speed. |
| `FF` | 0.1 m/s | Mean wind speed over the last 10 min of the hour. |
| `FX` | 0.1 m/s | Maximum wind gust during the hour. |
| `T` | 0.1 °C | Temperature at 1.50 m. |
| `T10N` | 0.1 °C | Minimum temperature at 0.1 m in the preceding 6 hours (only filled at some hours). |
| `TD` | 0.1 °C | Dew-point temperature at 1.50 m. |
| `SQ` | 0.1 hour | Sunshine duration in the hour (−1 = < 0.05 h). |
| `Q` | J/cm² | Global radiation in the hour. |
| `DR` | 0.1 hour | Precipitation duration in the hour. |
| `RH` | 0.1 mm | Hourly precipitation sum (−1 = < 0.05 mm). |
| `P` | 0.1 hPa | Air pressure reduced to sea level. |
| `VV` | code | Horizontal visibility (0 = < 100 m … 89 = > 70 km). |
| `N` | octants | Cloud cover (9 = sky invisible). |
| `U` | % | Relative humidity at 1.50 m. |
| `WW` | code 00–99 | Present weather code. |
| `IX` | code | Indicator for the way the weather code was determined (manned/automatic). |
| `M`, `R`, `S`, `O`, `Y` | 0/1 | Occurrence of fog, rain, snow, thunder and ice formation in the preceding hour. |

> Not every station measures every variable; empty values mean "not available". Weather stations are often several kilometres from the sensor, so wind and humidity are **indicative** for the sensor location.

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
| `related_sensor_ids` | list of string | `sensor_name`(s) the question refers to, if any. |
| `period_start`, `period_end` | date | Period the question refers to, if any. |
| `reference_answer` | string | Optional answer prepared by experts, to compare tool output with. |
| `source` | string | Where the question comes from (workshop, forum, e-mail, …). Personal data is removed. |

---

## 6. Known / reported sensor issues 🚧

Issues that were reported or confirmed for sensors, e.g. by measurement groups, RIVM or GGD. They can be used as (partial) **ground truth** for Challenge 1. Not every faulty period has been reported, so absence of an issue does **not** mean a sensor was fine. The columns below are the **proposed** schema.

| Column | Type | Description |
|---|---|---|
| `issue_id` | integer | Unique id of the issue. |
| `sensor_id` | string | `sensor_name` of the sensor (foreign key to `sensor_metadata`). |
| `issue_type` | string | e.g. `stuck_value`, `drift`, `offline`, `implausible_peaks`, `humidity_artifact`, `wrong_location`, `other`. |
| `start_time_utc`, `end_time_utc` | datetime (UTC) | Period of the issue; `end_time_utc` empty = ongoing/unknown. |
| `description` | string | Free-text description of the observed problem. |
| `reported_by` | string | Role of the reporter (measurement group, RIVM, GGD, …). |
| `confirmed` | boolean | Whether the issue was verified (e.g. by a physical check or by experts). |
| `action_taken` | string | e.g. sensor replaced, restarted, data removed. |
| `affects_quantities` | list of string | Which quantities are affected (`pm25`, `pm25_kal`, `temp`, …). |

---

## 7. Conventions and pitfalls

| Topic | What to know |
|---|---|
| **Time zones** | Samen Meten and Luchtmeetnet API: **UTC**. Luchtmeetnet validated files: local time **with offset** (`+01:00`/`+02:00`). KNMI: **UTC**, hour 1–24. Convert everything to one time zone (UTC is advised) before joining; a one-hour shift is a classic error. |
| **Hour labelling** | KNMI `HH` and Luchtmeetnet `timestamp_measured` mark the **end** of the hour. Check the label for the sensor data (see 🚧 in 2c) and align the intervals before comparing. |
| **Missing values** | Sensor data: `null`. Reference data: empty `waarde`. KNMI: empty field. Some exports use `NA` or sentinel values such as `-999`; check each file. |
| **Special wind values** | KNMI `DD`: `0` = calm, `990` = variable. Exclude both from wind-direction analysis. Wind direction is where the wind comes **from**. |
| **Sensor location** | Coordinates are rounded (≈100 m). Devices with `latitude = 0` and `longitude = 0` (and `municipality_code = None`) have no known location and cannot be used for spatial analysis. |
| **Raw vs. calibrated** | `*_kal` is calibrated by RIVM, the plain value is the raw sensor output. State which one you use. Reference stations have no calibrated variant. |
| **Sensor types** | SPS30 (SODAQ AIR) and SDS011 (older kits) behave differently. Consider `sensor_type` in your analysis. |
| **PM10** | Low-cost sensors cannot measure PM10 reliably; treat as indicative. |
| **Negative and extreme values** | Both occur in sensor data and (small negatives) in reference data. Peaks > 100 µg/m³ are usually artefacts, except for events like New Year's fireworks. See [data tips](./tips.md#4-data-cleaning). |
| **Reference station ≠ truth everywhere** | Stations differ in type (traffic, industrial, regional background) and distance to a sensor. |
| **Units** | Sensor `ug/m3` = µg/m³. KNMI values are in tenths. |
| **Live API history** | The live Samen Meten API only contains a limited recent history for a sensor (about four weeks at the time of testing). For longer periods use the Zenodo files. |

## Try the sources yourself

```text
# All Hollandse Luchten devices with their location and datastreams
https://api-samenmeten.rivm.nl/v1.0/Things?$filter=properties/owner eq 'Hollandse Luchten'&$expand=Locations,Datastreams/Sensor,Datastreams/ObservedProperty

# Latest hourly values of one datastream
https://api-samenmeten.rivm.nl/v1.0/Datastreams(56067)/Observations?$top=24&$orderby=phenomenonTime desc

# Reference station metadata and its latest PM2.5
https://api.luchtmeetnet.nl/open_api/stations/NL49701
https://api.luchtmeetnet.nl/open_api/measurements?station_number=NL49701&formula=PM25
```
