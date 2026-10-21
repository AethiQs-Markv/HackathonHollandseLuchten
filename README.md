<p align="center">
  <img src="images/logo.svg" alt="HL Logo">
</p>


# Hackathon Hollandse Luchten

<img src="https://hollandse-luchten.org/wp-content/uploads/2026/08/hackathon.jpg" alt="Hollandse Luchten Hackathon">

Repository for the **Hackathon Hollandse Luchten** on November 13th, 2026.  
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
| 4 | **Resident questions** | `resident_questions` | Real questions residents have about their air quality. |
| 5 | **Known / reported sensor issues** | `known_sensor_issues` | Sensors and periods with reported faults or movements of the sensor. Incomplete: no report does not mean a sensor was fine. |

> [!TIP]
> Read the [conventions and pitfalls](./docs/data.md#7-conventions-and-pitfalls) before you start: time zones, hour labelling (start vs. end), missing values, wind-direction codes and sensors without a location are the most common sources of errors.

### Looking at the data before the hackathon
The sources behind the data are public. You can explore the same data on the [Hollandse Luchten map](https://hollandse-luchten.org/kaart/), in the [Samen Analyseren Tool](https://analyseren.samenmeten.nl/), via the [Samen Meten API](https://api-samenmeten.rivm.nl/v1.0) and the [Luchtmeetnet API](https://api.luchtmeetnet.nl/open_api).

## Deliverables

Your submission should at least contain:
1)	The GitHub repository with any used code and/or examples, and a self-contained `README` explaining how to run it from scratch locally so we can check your solution.
2)	The presentation of your solution that your team is giving to the jury (maximum 5 minutes).
3)	A concrete real-life example showing your solution at work, for your challenge. For example, but not-limited to these ways:
	- **Challenge 1:** the detected outliers for a few sensors/periods, including the score, its certainty and the explanation shown to residents.
	- **Challenge 2:** the detected hotspot(s) on a map, including the score, the likely wind direction/source area and the explanation shown to residents.
	- **Challenge 3:** a resident question handled end-to-end, with an audit trail of the data, choices and Samen Analyseren functions used.
4)	An explanation of your methodological choices and considerations.
5)	A short description of the validation of your approach (metrics, comparison with reference stations, research papers/other research or known issues, limitations).

Every team is expected to present their solution to our jury.

> [!CAUTION]
> **Deadline**: all deliverables (your submission) need to be handed in Wednesday 18 November 2026 before 12:00 at mark.verschuren@aethiqs.nl and janus.jansen@noord-holland.nl.


## Jury
Our diverse and highly specialized jury will evaluate and judge your submission:
1. **Richard Floor**: citizen scientist, Hollandse Luchten.
2. **Sandjai Bhulai**: Professor Business Analytics, Vrije University Amsterdam.
3. **Laura Hornick**: programme manager Strengthening Local Democracy, municipality of Haarlem.
4. **Philipp Tuertscher**: Professor of Collaborative Innovation, Vrije University Amsterdam.

Your team is expected to present their solution in a maximum of 5 minutes. Afterwards, the jury has 5 minutes for a Q&A.

### Jury criteria

| Criterion | What the jury looks for |
|---|---|
| 1. Relevance to the challenge | To what extent does the submission provide a concrete solution to the chosen challenge? The team explains who the solution is intended for and why it is relevant to Hollandse Luchten. |
| 2. Technical quality and reliability | The solution is logically designed and demonstrably works. The team justifies its chosen method, algorithm, or model: why is this choice appropriate for the task and the available data? The team also explains its trade-offs, for example through references to literature, comparisons with alternatives, or its own validation and reliability measurements using data-science methods. Assumptions, uncertainties, and limitations are made clear. |
| 3. Value for residents | The submission helps residents better understand, analyse, or use air-quality data. The jury considers clarity, accessibility, and practical value. |
| 4. Innovation and smart application | The submission takes an original, surprising and/or novel approach. Innovation may lie in the technical solution, the combination of data sources, how results are presented, how residents are supported and more. |
| 5. Presentation | The team clearly presents their solution and relevance. The jury considers whether the demonstration or explanation is convincing, whether next steps are realistic, and whether practical requirements such as data availability, maintenance, privacy, and real-world use have been considered. The jury assesses the extent to which the solution is feasible to implement and aligns with the existing technical, social, and organisational structures of Hollandse Luchten. |

# **TODO: scoring verdeling toevoegen**

## Practical information
Important practical information about the hackathon is shown down below.

| | |
|---|---|
| **Q&A Hackathon** | Friday 6 November 2026, 14:45 - 16:30 through Zoom videocall. We will sent registered participants the link. |
| **Hackathon day** | Friday 13 November 2026, full day (**TODO: add times**) |
| **Location** | De Koepel, Haarlem |
| **Deadline** | The deadline for handing in your solution is on ***Wednesday 18 November before 12:00.*** at mark.verschuren@aethiqs.nl and janus.jansen@noord-holland.nl. |
| **Final presentations & award ceremony** | Thursday 19 November 2026, late afternoon (TODO exact time: **to be added**), De Koepel, Haarlem. Everyone is welcome, [register here](https://hollandse-luchten.org/updates/de-grote-hackathon-finale/). |
| **Language** | English |
| **Participants** | Maximum 45. Sign up as a team or individually; we will help matching into teams. |
| **Work after the hackathon day** | Teams are allowed to continue to work on their solution after the Hackathon day. Make sure you hand in your solution on time before the **deadline**. |
| **Support** | *Experts from the province of Noord-Holland, Waag Futurelab, RIVM, TNO, GGD and the Hollandse Luchten measurement groups are present during the hackathon.* |

## Prizes
Of course, there are prizes for the winners! The winning team will receive a fantastic trophy and a fun day out. In addition, we will help generate publicity for your idea, for example through articles or a presentation at a conference, and you will have the opportunity to stay involved in its further development. We’ll be able to share more details about this soon.

## Use of tools and generative AI
AI (coding) assistants and vibe coding are allowed, as long as teams disclose clearly on where they did and did not use them. We encourage the use of such tools, but also stress that teams should be able to explain and validate important parts of their solutions themselves.  

## Contact

For questions about the hackathon, please reach out to the organizing committee: 
- Janus Jansen - provincie Noord-Holland - janus.jansen@noord-holland.nl
- Mark Verschuren - AethiQs - mark.verschuren@aethiqs.nl
- Lianne Worrell - Waag Futurelab - lianne@waag.org

---

Good luck and have fun with your challenge! 

## Copyright and permission to use
TODO
