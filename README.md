<p align="center">
  <img src="images/logo.svg" alt="HL Logo">
</p>


# Hackathon Hollandse Luchten

<img src="https://hollandse-luchten.org/wp-content/uploads/2026/08/hackathon.jpg" alt="Hollandse Luchten Hackathon">

Repository for **The Hackathon Hollandse Luchten** on November 13th, 2026.  
Collaborating and putting your skills to work for a cleaner air and real societal impact!

> [!IMPORTANT]
> **Sign up** for the hackathon by filling out [this form](https://survey.waag.org/index.php/491878?lang=nl-informal)!

## Introduction
Welcome to the Hollandse Luchten Hackathon repository!

This GitHub repository has been set up for the event on November 13th, 2026. In this repository, you will find all the resources you need to collaborate on our **three distinct challenges**. Each challenge focuses on different aspects of air quality data analysis and interpretation. You will work with unique air quality datasets collected by residents through sensors across North Holland.

Thank you for helping us work on cleaner air and societal impact. We are looking forward to your solutions!

## Challenges

> ### [Challenge 1: Spot the outliers](./challenge%201/README.md)
Use smart methods – or a combination of approaches – to automatically identify sensors that produce unusual measurements, helping residents and researchers work with data they can trust.

> ### [Challenge 2: Discover pollution hotspots](./challenge%202/README.md)
Apply state-of-the-art AI techniques to create novel ways of identifying local sources of air pollution based on large volumes of sensor data.

> ### [Challenge 3: Help residents analyse data more easily](./challenge%203/README.md)
Build an intelligent assistant that helps residents analyse, understand and interpret their sensor data, without needing to be data analysts themselves.

## Relevant background information
> [!TIP]
> As with any data and/or AI related challenge, understanding the domain, hardware and user needs you are working with are crucial for a successful solution. Therefore, we strongly encourage you to read the relevant background information and resources we have put here: [Relevant resources](./docs/resources.md).

## Data
During the hackathon the data is supplied on [Zenodo](https://zenodo.org/). All data is open and originates from public sources, so no registration or API key is needed to use it.

> [!WARNING]
> The data will be supplied for download on the xx of November. Zenodo link: *to be added*.

### Which data is available?
We supply five datasets, delivered as seven tables in Parquet format. **The full column-by-column description of every table is in [Data description](./docs/data.md).**

| # | Dataset | Tables | What it contains |
|---|---|---|---|
| 1 | **Hollandse Luchten sensors** | `sensor_metadata`, `sensor_datastreams`, `sensor_measurements` | Location, municipality, sensor hardware, linked reference/weather stations, and hourly PM2.5 / PM10 (raw and calibrated), temperature, humidity (and NO₂ for a few devices) of the citizen sensors. |
| 2 | **Official reference measurements** | `reference_station_metadata`, `reference_measurements` | Station type, location and operator, plus hourly validated PM2.5, PM10 and NO₂ from Luchtmeetnet stations to compare sensors against. |
| 3 | **Weather data (KNMI)** | `weather_station_metadata`, `weather_hourly` | Location, hourly wind direction and speed, temperature, humidity, precipitation, pressure and more from the KNMI station linked to each sensor. |
| 4 | **Resident questions** | `resident_questions` | Real questions residents have0 about their air quality. |
| 5 | **Known / reported sensor issues** | `known_sensor_issues` | Sensors and periods with reported faults or movements of the sensor. Incomplete: no report does not mean a sensor was fine. |

> [!TIP]
> Read the [conventions and pitfalls](./docs/data.md#7-conventions-and-pitfalls) before you start: time zones, hour labelling (start vs. end), missing values, wind-direction codes and sensors without a location are the most common sources of errors.

### Looking at the data before the hackathon
The sources behind the data are public. You can explore the same columns on the [Hollandse Luchten map](https://hollandse-luchten.org/kaart/), in the [Samen Analyseren Tool](https://analyseren.samenmeten.nl/), via the [Samen Meten API](https://api-samenmeten.rivm.nl/v1.0) and the [Luchtmeetnet API](https://api.luchtmeetnet.nl/open_api). Example requests are listed at the bottom of the [Data description](./docs/data.md#try-the-sources-yourself).

## Deliverables

Your submission should at least contain:
1)	The GitHub repository with any used code and/or examples, and a self-containing `README` explaining how to run it from scratch locally.
2)	The presentation of your solution.
3)	A concrete example showing your solution at work, for your challenge:
	- **Challenge 1:** the detected outliers for a few sensors/periods, including the score, its certainty and the explanation shown to residents.
	- **Challenge 2:** the detected hotspot(s) on a map, including the score, the likely wind direction/source area and the explanation shown to residents.
	- **Challenge 3:** a resident question handled end-to-end, with an audit trail of the data, choices and Samen Analyseren functions used.
4)	An explanation of your metholodogical choices and considerations.
5)	A short description of the validation of your approach (metrics, comparison with reference stations, research papers/other research or known issues, limitations).

Every team is expected to present their solution to our jury.


## Jury criteria

| Criterion | What the jury looks for |
|---|---|
| 1. Relevance to the challenge | To what extent does the solution address a concrete problem from the chosen challenge? The team explains who the solution is intended for and why it is relevant to Hollandse Luchten. |
| 2. Technical quality and reliability | The solution is logically designed and demonstrably works. The team justifies its chosen method, algorithm, or model: why is this choice appropriate for the task and the available data? The team also explains its trade-offs, for example through references to literature, comparisons with alternatives, or its own validation and reliability measurements using data-science methods. Assumptions, uncertainties, and limitations are made clear. |
| 3. Value for residents | The solution helps residents better understand, analyse, or use air-quality data. The jury considers clarity, accessibility, and practical value: what can residents do or learn with it? |
| 4. Innovation and smart application | The solution takes an original or surprising approach. Innovation may lie in the technical solution, the combination of data sources, how results are presented, or how residents are supported. The approach must remain appropriate and practical. |
| 5. Feasibility and presentation | The team clearly demonstrates how the solution works and what is needed to develop it further. The jury considers whether the demonstration or explanation is convincing, whether next steps are realistic, and whether practical requirements such as data availability, maintenance, privacy, and real-world use have been considered. The jury assesses the extent to which the solution is feasible to implement and aligns with the existing technical, social, and organisational structures of Hollandse Luchten. |

## Hackathon Format

| | |
|---|---|
| **Hackathon day** | Friday 13 November 2026, full day (**TODO: add times(**) |
| **Location** | De Koepel, Haarlem |
| **Deadline** | The deadline for handing in your solution is on Wednesday 18 November before 17:00. |
| **Final presentations & award ceremony** | Thursday 19 November 2026, late afternoon (TODO exact time: **to be added**), De Koepel, Haarlem. Everyone is welcome, [register here](https://hollandse-luchten.org/updates/de-grote-hackathon-finale/). |
| **Language** | English |
| **Participants** | Maximum 45. Sign up as a team or individually; individuals are matched into teams. |
| **Work after the hackathon day** | Teams are allowed to continue to work on their solution after the hackathon day. Be careful you hand in your solution on time before the **deadline**. |
| **Support** | *Experts from the province of Noord-Holland, Waag Futurelab, RIVM, TNO, GGD and the Hollandse Luchten measurement groups are present during the hackathon.* |

## Prizes
Of course, there are prizes for the winners. The winning team will receive a fantastic trophy and a fun day out. In addition, we will help generate publicity for your idea, for example through articles or a presentation at a conference, and you will have the opportunity to stay involved in its further development. We’ll be able to share more details about this soon.

## Use of tools and generative AI
AI (coding) assistants and vibe coding are allowed, as long as teams disclose clearly on where they did and did not use them. We encourage the use of such tools, but also address that teams should be able to explain and validate important parts of their solutions themselves.  

## Contact

For questions about the hackathon, please reach out to the organizing committee: 
- Janus Jansen - provincie Noord-Holland - janus.jansen@noord-holland.nl
- Mark Verschuren - AethiQs - mark.verschuren@aethiqs.nl
- Lianne Worrell - Waag Futurelab - lianne@waag.org
---

Good luck and have fun with your challenge! 

## Copyright and permission to use
# TODO: wellicht gewoon bij repo aangeven via license?

Copyright for the original code and materials in this repository remains with their respective authors, unless otherwise agreed in writing. The authors grant the Hollandse Luchten consortium and its partners a non-exclusive, royalty-free permission to use, reproduce, modify, adapt, and integrate this solution for evaluating, developing, maintaining, and operating tools and services related to Hollandse Luchten. The consortium may build upon the solution for these purposes. This permission does not transfer ownership of the original work.

This permission applies only to material created by the authors. Third-party software, datasets, text, images, and other materials remain subject to their own copyright, licence terms, and usage conditions. Any required attribution must be retained. Contributors should confirm that they have the rights to grant this permission for the material they contribute.

Before relying on this statement as a formal licence, the team and the consortium should confirm that its scope and wording meet their needs.
