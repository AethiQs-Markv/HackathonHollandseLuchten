# Relevant background information & resources

> [!NOTE]
> The majority of the original sources are in Dutch. Browser translation works well on most pages below. 

## About the project

| Link | What participants will find |
|---|---|
| [Hollandse Luchten – homepage](https://hollandse-luchten.org/) | General introduction to the citizen measurement network, its goals, and news. |
| [Measurement groups](https://hollandse-luchten.org/meetgroepen/) | Overview of the active measurement groups: IJmond, Haarlem, Gooi- en Vechtstreek and Zaanstad, and how residents formulate measurement questions together with data analysts and health experts. |
| [Frequently asked questions](https://hollandse-luchten.org/faq/) | The key facts in one place: which sensors are used (SODAQ AIR since Nov 2022, HoLu kits 2019–2022), which substances are measured, where the open data is available, and how to view historical data. |
| [Measurement results](https://hollandse-luchten.org/meetresultaten/) | Overview page that links to the map and the analysis tool, explains what is measured (PM2.5 with SODAQ AIR, NO2 with Palmes tubes) and lists local case studies. |
| [Hollandse Luchten map](https://hollandse-luchten.org/kaart/) | The map with all Hollandse Luchten sensors and official stations; the quickest way to see which sensors exist and how their data looks. |
| [Sensors](https://hollandse-luchten.org/sensoren/) | Explanation of the current SODAQ AIR (open source hardware and data, hanging instructions, LED colours, manual) and the older HoLu sensor kit. |
| [Why Hollandse Luchten chose the SODAQ AIR (PDF)](https://hollandse-luchten.org/wp-content/uploads/2023/02/20230221-Waarom-hebben-we-voor-de-SODAQ-Air-gekozen.pdf) | Background on the choice of sensor hardware in 2022–2023. |
| [SODAQ AIR manual (PDF)](https://hollandse-luchten.org/wp-content/uploads/2026/08/SODAQ-AIR-Handleiding.pdf) | Manufacturer manual for the current sensor. |
| [A look back at the first phase](https://hollandse-luchten.org/updates/een-terugblik-op-de-eerste-fase-van-hollandse-luchten-wat-zijn-de-belangrijkste-lessen-en-waar-zijn-we-trots-op/) | Blog from 2019 on the first 200 HoLu sensor kits: modular build kit, PM2.5/PM10, NO2 and ozone in the extended version, the LoRaWAN network and lessons learned. |

## Data, analysis and tools

| Link | What participants will find |
|---|---|
| [Samen Meten data portal](https://samenmeten.rivm.nl/dataportaal/) | RIVM portal with current and historical data of citizen sensors across the Netherlands. This is amongst others where the Hollandse Luchten data is stored. |
| [Samen Meten API documentation](https://www.samenmeten.nl/dataportaal/api-application-programming-interface) | How to use the open SensorThings API (Things, Datastreams, Observations), with example queries. No key needed. |
| [Samen Meten sensor types (PDF)](https://samenmeten.rivm.nl/dataportaal/help/sensortype.pdf) | List of the sensor hardware types that appear in the data. |
| [Samen Meten forum](https://forum.samenmeten.nl/) | Community forum for questions on the data and the tools. |
| [Samen Analyseren Tool – source code (GitHub)](https://github.com/rivm-syso/Analyse-Together) | The R/Shiny source of the tool: the validated analysis and plotting functions that Challenge 3 must use, plus how the tool handles reference stations, KNMI data and uncertainty. Licence: GPL v3. |
| [Analyse the data with the Samen Analyseren Tool](https://hollandse-luchten.org/updates/analyseer-de-hollandse-luchten-data-met-de-samen-analyseren-tool/) | Feb 2026 article: how to select, visualise and analyse Hollandse Luchten data, combined with Luchtmeetnet and KNMI data; includes an explanation video and a manual, and explains the difference with the map. |
| [Samen Analyseren Tool: manual announced](https://hollandse-luchten.org/updates/handleiding-beschikbaar-voor-de-samen-analyseren-tool/) | 2020 announcement of the tool becoming open source and getting a manual. |
| [Getting started with data in Haarlem](https://hollandse-luchten.org/updates/aan-de-slag-met-data-in-haarlem/) | July 2026 workshop report: checking sensor availability, comparing sensor groups, weekday patterns, pollution roses and triangulation around a possible source. Very relevant for Challenge 2. |
| [Updates](https://hollandse-luchten.org/updates/) | News archive with software updates, API status, calibrations, GGD and TNO research, and tool changes. |

## Reference stations and weather data

| Link | What participants will find |
|---|---|
| [Luchtmeetnet open data (validated, per year)](https://data.rivm.nl/data/luchtmeetnet/) | Download of validated hourly data of the official Dutch air-quality network (PM2.5, PM10, NO2, and more). |
| [Luchtmeetnet data description (PDF)](https://data.rivm.nl/data/luchtmeetnet/readme.pdf) | 79-page bilingual description of the files: folder structure, validation, normalisation, measurement uncertainty, time conventions, FAQ and an R reading script. |
| [Luchtmeetnet open API documentation](https://api-docs.luchtmeetnet.nl/) | API for stations, components and the latest (not yet validated) measurements. |
| [Luchtkwaliteitsindex](https://www.luchtmeetnet.nl/informatie/luchtkwaliteit/luchtkwaliteitsindex) | How RIVM translates concentrations to an index that residents understand; useful when communicating results. |
| [KNMI hourly observations](https://www.daggegevens.knmi.nl/klimatologie/uurgegevens) | Hourly weather data (wind direction and speed, temperature, humidity, precipitation) per KNMI station, with variable descriptions. |
| [KNMI Data Platform](https://dataplatform.knmi.nl/) | KNMI open data, including the [10-minute observations](https://dataplatform.knmi.nl/dataset/10-minute-in-situ-meteorological-observations-1-0) (API key required). |

## Data quality, calibration and validation

| Link | What participants will find |
|---|---|
| [GGD Amsterdam: co-location study of SODAQ AIR (article)](https://hollandse-luchten.org/updates/ggd-rapport-over-onderzoek-naar-de-kwaliteit-van-sodaq-air-snuffelfiets-pm2-5-sensorkastjes/) and [full report (PDF, Dutch)](https://hollandse-luchten.org/wp-content/uploads/2024/09/GGDAmsterdam_HLcolocatieonderzoekSodaqPM2.5sensors.pdf) | SODAQ AIR and Snuffelfiets sensors placed next to the official GGD monitors in Zaandam and Beverwijk. Conclusions (Aug 2024): concentrations, and especially peaks, are overestimated and the sensors are not suited to compare with EU/WHO limits, correlation drops below about 10 µg/m³, but peaks are picked up well and the quality is clearly better than the older SDS011. Humidity and temperature influence the errors. |
| [TNO and Waag: improving data quality (Jul 2026)](https://hollandse-luchten.org/updates/opgeruimd-staat-netjes-op-naar-verbeterde-data-kwaliteit/) | Why outliers must be filtered, why a statistical outlier is not always an error (fireworks, local sources), and the interactive dashboard in which residents confirm or reject suggested outliers. Directly relevant for Challenge 1. |
| [RIVM calibration for the Sensirion SPS30 (Dec 2023)](https://hollandse-luchten.org/updates/het-rivm-publiceert-de-kalibratie-voor-sensirion-sps30/) and [calibration on the map (Apr 2024)](https://hollandse-luchten.org/updates/kalibratie-voor-sensirion-sps30-nu-te-zien-op-de-kaart/) | Why sensors overestimate at high humidity, how the RIVM calibration (`pm25_kal`) works and how it relates to the calibration of the Hollandse Luchten data science group. |
| [Research into high SODAQ AIR values (Feb 2023)](https://hollandse-luchten.org/updates/onderzoek-van-rivm-naar-hoge-waarden-sodaq-air/) and [first results (Oct 2023)](https://hollandse-luchten.org/updates/eerste-resultaten-van-onderzoek-naar-hoge-waarden-en-kalibratie/)  | Comparison of SODAQ AIR with official stations and the influence of humidity and temperature. |
| [Sensors that move](https://hollandse-luchten.org/updates/verplaatsingen-sensoren-in-kaart-gebracht/) | Analysis of sensors reporting from other locations than registered; relevant for location quality in the metadata. |
| [Industry measurements: using wind direction (session, May 2024)](https://hollandse-luchten.org/updates/data-analyse-uitleg-industrie-meten/) | Example of a member analysing sensors around the Tata Steel site with wind direction; inspiration for Challenge 2. |
| [Hollandse Luchten tests sensors at the RIVM station Utrecht (Oct 2026)](https://hollandse-luchten.org/updates/hollandse-luchten-test-fijnstofsensoren-bij-het-rivm-station-in-utrecht/) | Ongoing co-location test of three other sensor types next to an official station. |
| [Research](https://hollandse-luchten.org/onderzoek/) | Overview of research in the project; the updates below the headline items cover data quality, calibration and the GGD validation. |

## Particulate matter and interpretation

| Link | What participants will find |
|---|---|
| [Particulate matter and health](https://hollandse-luchten.org/updates/fijnstof-en-gezondheid/) | Explanation of PM2.5, health effects, the limitations of low-cost sensors, and how to interpret measurements correctly. |
| [Citizen science and measurement groups](https://hollandse-luchten.org/burgerwetenschap-en-meetgroepen/) | Explanation of particulate matter dispersion, SODAQ AIR, Palmes tubes, and the difference between continuous sensor measurements and four-weekly NO2 measurements. |
| [Health-effects logbook](https://hollandse-luchten.org/updates/logboek-om-gezondheidseffecten-in-kaart-te-brengen/) | Optional context: a template for residents to log health complaints or events and compare them with sensor measurements or peaks (useful background for Challenge 3 questions). |

## Locations and case studies

| Link | What participants will find |
|---|---|
| [Haarlem measurement group](https://hollandse-luchten.org/meetgroepen/haarlem/) | Haarlem focuses on traffic and wood-burning emissions, with PM sensors and Palmes tubes. |
| [Works at the NSTM site (Aug 2026)](https://hollandse-luchten.org/updates/werkzaamheden-nstm-terrein/) | Case study: three sensors in a triangle around a site and pollution roses to test a source hypothesis; the analysis could not confirm a source. Relevant for Challenge 2. |
| [Traffic emissions around the Zaanse Schans (Mar 2026)](https://hollandse-luchten.org/updates/verkeersuitstoot-rondom-zaanse-schans/) | Example of a local measurement question: the effect of increasing visitor numbers, analysed with one PM sensor installed in 2023 (no NO2 tubes nearby). |
| [Data analysis and TNO presentation in Haarlem (May 2023)](https://hollandse-luchten.org/updates/verslag-data-analyse-en-presentatie-tno-in-haarlem/) | Report of a data-analysis evening with residents. |

## Important for participants

- The current **SODAQ AIR** (since 2022) measures PM2.5, temperature, and humidity.
- The older **HoLu kits** (2019–2022) measured PM2.5 and in addition PM10; the extended version also measured NO2 and ozone. Their historical data is still available.
- NO2 is measured using **Palmes diffusion tubes**.
- SODAQ AIR takes measurements every few minutes; the hackathon data is aggregated to hourly values (see [Data description](./data.md)).
- Open data is amongst others available through the Hollandse Luchten map, **Samen Meten**,**Samen Analyseren Tool**, SODAQ **Knowyourair**.
- Treat sensor data as indicative: the GGD Amsterdam study found that **absolute** PM2.5 concentrations are unreliable, particularly at low concentrations, but that peaks are detected well. Always check individual sensors and compare results with for example reference stations.
