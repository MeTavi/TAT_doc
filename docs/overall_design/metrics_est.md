---
layout: default
title: Transit Metrics Estimation
parent: Overall Design
nav_order: 2
---

# Transit Metrics Estimation
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Transit Metrics

After the data is ingested by the data preparation stage above, a series of data cleaning and transformation operations are applied to estimate key transit metrics, such as travel time, load, boarding, and alighting. This phase is handled by the **Transit Metrics Module**, which first calculates the metrics at the stop-to-stop level and then maps them onto the corresponding road network. To generate these metrics, the Jupyter notebook `1_produce_curated_data.ipynb` is used. This notebook instantiates the **TransitMetrics** class and runs two primary methods:
   - `produce_stop_to_stop_measures()`
   - `produce_link_measures()`    

The `produce_stop_to_stop_measures()`method calculates the transit metrics between stops. When this method is called, it saves its output (the stop-to-stop metrics) locally in the `4_outputs\1_stop_to_stop_outputs`, under a structured partition based on the analysis period, region, year, month, and day.   
Once the stop-to-stop metrics are saved, the `produce_link_measures()` method can be called. This method reads the outputs saved by the `produce_stop_to_stop_measures()` method from the local drive and maps them onto the road network. The link-based measures are then saved in the `4_outputs\2_travel_time_split` folder, using a partitioning structure similar to the stop-to-stop outputs.
Below is the folder structure where the outputs of the transit metrics are saved:
```shell
|   +-- 4_outputs
|   |   +-- 1_stop_to_stop_output
|   |   |   +-- [YYYMMDD]
|   |   |   |   +-- region=SEQ
|   |   |   |   |   +-- Year=[YYYY]
|   |   |   |   |   |   +-- Month=[MM]
|   |   |   |   |   |   |   +-- Day=[dd]
|   |   |   |   |   |   |   |   +-- stop_to_stop_measures.parquet.gz
|   |   +-- 2_travel_time_split
|   |   |   +-- [YYYMMDD]
|   |   |   |   +-- region=SEQ
|   |   |   |   |   +-- Year=[YYYY]
|   |   |   |   |   |   +-- Month=[MM]
|   |   |   |   |   |   |   +-- Day=[dd]
|   |   |   |   |   |   |   |   +-- link_based_measures.parquet.gz

```

## Travel Time Estimation 

Following hig-level steps are undertaken to estimate the travel time.

1.Estimating the Actual Arrival and Departure Time: Actual (or estimated) Arrival and Departure times are transaction times when available otherwise, approximated using mid-geofence time added to vehicle times as indicated the trip stop timing report (see StopToStopVehicleTime.enriched_transaction_veh_arrival_departure_time).
2.Identifying Stops within the First and Last Actual Stops: Actual arrival and departure times are not always available. The First and Last Actual Stops are defined as the initial and final stops in a trip where both the actual arrival and departure times are available.
3.Handling Null Values in Actual Arrival and Departure Times within the First and Last Actual Stops: Where the actual arrival and departure times are not available for any of the stops between the first and last actual stops, the values of actual and departure times are interpolated to fill the gaps in the data. As part of this process the sequence of stops are grouped into SectionIDs that is used for the interpolating the travel time.
4.Estimate Travel Time (per Sections): Travel time per section is estimated using the updated actual arrival and departure times. This is the travel time per section. Where a section could be as small as one stop to stop segment of a given trip or comprise multiple consecutive segments in a trip. (see StopToStopTravelTime.estimated_sections_travel_time_df)
5.Interpolating Travel Time (Link Level): Travel time (by sections) is transferred to the Street Segment Links using the link length to proportionally distribute the travel time proportional to the links’ length within each section.

[//]: # (Each method instantiates additional backend classes to handle specific data processing tasks. The **Ticketing Module** processes the ticketing data, while the **GTFS Module** generates the scheduled timetable data from the GTFS input. These two modules independently process their respective data, which are then combined in the `StopToStopMeasures` class within the **Transit Metrics Module**.)

[//]: # (Once combined, the stop-to-stop measures are integrated with the spatial itineraries using the **SegmentLink** class, also within the **Transit Metrics Module**.)

