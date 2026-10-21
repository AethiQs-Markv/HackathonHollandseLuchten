# Challenge 1: Spot the outliers

## Challenge description

The Hollandse Luchten project makes use of SODAQ Air sensors to measure air quality, in particular particulate matter (PM2.5). Due to several factors, these sensors can produce faulty or anomalous measurements. Such measurements can have a big impact on downstream analysis, other tools that use the data and/or other follow-up actions that are taken. Therefore, in this challenge your task is to work on an automated approach to ‘spot these outliers’.


## Problem statement

By spotting an outlier, we identify a data point that is distinctly different from other data points. An unusual measurement is thereby not automatically an incorrect measurement. A few examples of why outliers may occur:
-	Sensor calibration drift over time,
-	Electronic faults or data transmission errors,
-	Environmental/weather conditions,
-	New years firework,
-	Unusual but valid atmospheric conditions and
-	Other local hotspots / sources of pollution.

An important element in identifying outliers, might be the context in which you assess the data point and how certain are we that the data point is an outlier? Is the datapoint an outlier *within* the data from that specific sensor, in comparison to other Hollandse Luchten sensors in the region and/or compared against official reference measurements?

Therefore, in this challenge you will focus on:
1.	Using data science and AI techniques to automatically **detect** outliers.
2.	A scoring metric to **indicate** the status of the sensor at a moment in time and **how certain** the techniques are about the outlier.
3.	**Communicating** this scoring metric to residents in a clear and transparent manner and **why** it is considered an outlier.
4.	**Bonus**: indicating to the residents for what kind of analysis the sensor in that time period can be used for and for what kind of questions it cannot. And possible follow-up actions that need to be taken in case of a malfunctioning sensor.

## Possible techniques
Teams are allowed to use all possible techniques. A logical starting point may be to look at relevant statistical, time-series, machine-learning, deep learning and/or hybrid methods. A simple and well-validated approach may be more valuable than a complex model that is difficult to explain or verify. We expect from teams that they are able to substantiate (validation metrics, research, others) any made technical choices, considerations and results to the jury.

---

[Back to main README](../README.md)