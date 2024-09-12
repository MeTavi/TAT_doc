---
title: Home
layout: home
nav_order: 1
---

## Introduction

Transit Analytics Tool is a series of code-based processes that provide insightful
statistics on the usage and performance of the bus network at various aggregation levels.

The Extract, Transform, and Load (ETL) process of Transit Analytics tools takes raw input data, such as ticketing data (
transaction and trip stop timing), containing the details of scheduled services, and HASTUS inputs (converting the bus routes to the underlying road
network), and converts it into different curated datasets. These curated datasets are then used in Tableau dashboards
for visualising performance metrics. The two main visualisation products that use these curated datasets are:

- The **Corridor Explorer (CE) dashboards** allows users to explore bus performance at a corridor, segment, and stop
  level.
  It can be used to easily identify constraints, low-performing sections, or areas of the network where further
  investigations could be warranted. The data provided in the dashboard is easily filterable and dissectible, enabling
  the creation of custom views and analyses.
- The **Bus Corridors Action Plan (BCAP) dashboards**, while using the same ETL process, is designed to spatially
  identify and quantify bus network performance "hotspots" at a segment level and includes all of Southeast
  Queensland's (SEQ) bus network segments. The dashboard features segment-level ranking and comparison of performance
  across five (5) key network performance metrics related to efficiency and reliability. It allows filtering the results
  by road type (e.g., state versus local roads, busway versus non-busway), TMR region, and local government area (LGA).
  Additional data, such as the number of services and passengers, operator feedback, and bus stop utilization data, is
  included to provide further insights into the nature and magnitude of segment performance.

The overall ETL process, shown in figure below, outlines the high-level steps involved. The resulting outputs are
utilised in Corridor Explorer and BCAP.However, these outputs can also be adapted to support other custom dashboards and
analyses.

[_comment: update the image and annotate the common ETL stages and specific etl stages, now that we have it all in
python,
specify the part that is done in jupyter notebook_]

![alt text](./media/img.png)
