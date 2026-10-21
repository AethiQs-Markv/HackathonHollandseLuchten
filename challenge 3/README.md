# Challenge 3:  Help residents analyse data more easily

## Challenge description

Hollandse Luchten collects large amounts of sensor data on air quality. However, analysing and interpreting this data can be difficult. It often requires specific expertise in air quality, particulate matter, and data analysis.

This challenge seeks solutions that enable residents to explore and analyse air-quality data themselves, even if they do not have this specialist knowledge. The key objective is to make data analysis more accessible while ensuring that results remain valid, understandable, and transparent.

The core idea: residents ask their questions naturally, the system interprets the request, **must use available verified data analysis tools from samenmeten** to answer it, provides an answer, including interpretation, any uncertainties and along with a complete audit trail showing which steps were taken to produce the results.


## Problem statement

Residents who want to understand air quality in their area can use the existing [Samen Analyseren Tool](https://analyseren.samenmeten.nl/) to select sensor data, filter possible outliers, do analysis and create visualizations such as pollution roses, bar charts, and time-series plots. However, the usage of the Samen Analyseren Tool can be difficult for residents, because for good usage it requires users to understand data-analysis concepts, make methodological choices, understand any limitations on sensor hardware etc. While residents are just looking for funded answers to their research questions.

Therefore, this challenge wants to bring those two worlds more together by developing solutions that make it easier for residents to find answers to their questions. The solutions are constrained to **use only validated data analysis functions and techniques** as used in the [Github of the Samen Analyseren Tool](https://github.com/rivm-syso/Analyse-Together).

The solution should also make the analysis transparent. Users need to be able to see which data was used, which choices were made, which methods were applied, and how these choices may affect the outcome. This enables residents to carry out analyses independently in a valid, understandable, and reproducible way.

A solution may, for example:
1.	**Help** residents turn a question into an appropriate analysis.
2.	**Support** the selection of relevant sensor data, locations, and time periods.
3.	**Guide** users in making methodological choices, such as filtering outliers or comparing groups.
4.	Create clear and useful **visualizations**.
5.	**Explain** results, assumptions, limitations, and uncertainty in accessible language.
6.	Provide a **transparent record/audit trail** of the data, methods, decisions, and steps used.
7.	**Bonus:** Support follow-up questions and refinements.


## Possible techniques
You are free to use whatever techniques and frameworks seem most valuable to solve this challenge. However, it is a **requirement** to use the existing [Samen Analyseren Tool](https://github.com/rivm-syso/Analyse-Together) functions to do analysis and make visualisations. This ensures any analysis are always carried out with validated functionality.

A simple, well-validated approach may be more valuable than a complex solution that is difficult to explain or verify. Teams should be able to substantiate their technical choices and considerations to the jury, for example with validation metrics, test cases, research, or comparisons with reference measurements.


## Sample of resident questions

To help guide development, consider the following resident question examples:

1. **User Education**: "What different types of measurements are stored from the sensors?"
2. **Temporal Comparison**: "How do the PM 2.5 January values compare to  the values in December?"
3. **Location Analysis**: "Which sensor has the worst air quality in our neighborhood?"
4. **Trend Analysis**: "Is air quality improving or getting worse this year?"
5. **Anomaly Detection**: "Were there any unusual air quality events last month?"
6. **Comparative Analysis**: "Is air quality better on weekends vs. weekdays?"
7. **Source Investigation**: "What is the impact of the construction site on the PM 2.5 air quality at my street?"
8. **Statistical Summaries**: "What's the average air quality in our area?"
9. **Alerts/Thresholds**: "When do air quality values exceed safe levels?"
10. **Forecasting Support**: "Based on current trends, how will next week's air quality look?"


---

[Back to main README](../README.md)