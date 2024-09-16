---
layout: default
title: Transit Metrics Aggregation
parent: Overall Design
nav_order: 3
---

# Transit Metrics Aggregation
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## Transit Metric Aggregation   
The Data Aggregation stage occurs after transit metrics have been estimated at the link level. At this point, the process branches into two distinct streams, each responsible for aggregating the results into curated datasets for the two primary dashboards: the **Corridor Explorer (CE) Dashboards** and the **Bus Corridor Action Plan (BCAP) Dashboards**.
Each of these streams has a dedicated module within the **Transit Metrics Aggregate** module. Additionally, separate Jupyter notebooks are provided for generating the outputs for each dashboard as described below:

### Producing outputs for Corridor Explorer (CE) Dashboard

To generate the outputs for the **Corridor Explorer (CE)** dashboard, two Jupyter notebooks are used:

1. Producing curated data for the Corridor Explorer dashboards: The **CE_Measures** notebook generates the aggregated measures at both the link and stop levels for each corridor for a given analysis period.

2. Producing the Hyper files for the Corridor Explorer dashboards: The  **CE_Export_to_Hyper** notebook combines the results of link and stop measures for all corridors and all periods into one file and creates a Hyper file that will be used for visualisation in the Corridor Explorer dashboard.



#### Step 1: Producing Curated Data for Corridor Explorer Dashboard
The `CE_Measures.ipynb` notebook creates an instance of the `CorridorExplorer` class and runs the following three methods, in sequence, on the link-based measures generated in the previous steps:

1. Appending corridor definition
The `append_corridors()` method reads and combines all the link-based measures produced in the previous steps for a given analysis period. The combined data is appended to the **Corridor Definition**, and the results are saved in a structured partition, where each file contains the link-based measures related to a specific corridor. Below is the folder structure where the appended corridors are saved locally. 

   ```shell
   |   +-- 4_outputs
   |   |   +-- 1_stop_to_stop_output
   |   |   +-- 2_travel_time_split
   |   |   +-- 3_corridor_explorer
   |   |   |   +-- 1_append_corridors
   |   |   |   |   +-- [YYYYMMDD]
   |   |   |   |   |   +-- corridor_type=Infrastructure
   |   |   |   |   |   |   +-- region=[region]
   |   |   |   |   |   |   |   +-- [corridor_id]_tt_estimated.parquet.gz
   |   |   |   |   |   +-- corridor_type=Route
   |   |   |   |   |   |   +-- region=[region]
   |   |   |   |   |   |   |   +-- [corridor_id]_tt_estimated.parquet.gz
   ```

2. Producing corridor metrics
After appending the link-based data to the corridors, the resulting dataset is used for the next two methods. Both the `produce_all_corridors_stop_measure()` and `produce_all_corridors_link_measure()` methods relay on a dedicated YAML configuration file to perform the necessary calculations and aggregations. These methods generate stop-based and link-based metrics, such as passenger load, travel time, and delays, at the link level for each corridor during the entire analysis period.
The results from these methods are saved in a structured format, similar to the output of `append_corridors()` method described above. 
   ```shell
   |   +-- 4_outputs
   |   |   +-- 1_stop_to_stop_output
   |   |   +-- 2_travel_time_split
   |   |   +-- 3_corridor_explorer
   |   |   |   +-- 1_append_corridors
   |   |   |   +-- 2_measures
   |   |   |   |   +-- [YYYYMMDD]
   |   |   |   |   |   +-- corridor_type=Infrastructure
   |   |   |   |   |   |   +-- region=[region]
   |   |   |   |   |   |   |   +-- link_based_measures
   |   |   |   |   |   |   |   |   +-- [corridor_id]_link_based_estimates.parquet.gz
   |   |   |   |   |   |   |   +-- stop_based_measures
   |   |   |   |   |   |   |   |   +-- [corridor_id]_stop_based_estimates.parquet.gz
   |   |   |   |   |   +-- corridor_type=Route
   |   |   |   |   |   |   +-- region=[region]
   |   |   |   |   |   |   |   +-- link_based_measures
   |   |   |   |   |   |   |   |   +-- [corridor_id]_link_based_estimated.parquet.gz
   ```

#### Step 2: Producing the Hyper files for Corridor Explorer dashboards

The `CE_Export_to_Hyper.ipynb` notebook combines the link and stop measures from all corridors and all analysis periods. It then exports this consolidated data into Hyper files for use in the Corridor Explorer dashboard in Tableau.

   ```shell
   |   +-- 4_outputs
   |   |   +-- 1_stop_to_stop_output
   |   |   +-- 2_travel_time_split
   |   |   +-- 3_corridor_explorer
   |   |   |   +-- 1_append_corridors
   |   |   |   +-- 2_measures
   |   |   |   +-- 3_visualisation
   |   |   |   |   +-- link_measure_[run_version].hyper
   |   |   |   |   +-- link_measure_[run_version].parquet.gz
   |   |   |   |   +-- stop_measure_[run_version].hyper
   |   |   |   |   +-- stop_measure_[run_version].parquet.gz
   ```

### Producing outputs for BCAP Dashboard

To generate the outputs for the **BCAP Dashboards** dashboard, two Jupyter notebooks are used:


1. Producing curated data for the BCAP Dashboards: The **BCAP_Measures** notebook generates the aggregated measures at both the link and stop levels for a given analysis period.

2. Appending spatial layers to the curated data and producing the Hyper files for the BCAP Dashboards: The **BCAP_Attributes** notebook reads and combines the link and stops measures produced in the previous step, appends various spatial labels to each record and creates a Hyper file that will be used for visualisation in the Corridor Explorer dashboard.


#### Step 1: Producing curated data for the BCAP Dashboards

Similarly, to generate the required data for the **BCAP Dashboards**, the Jupyter notebook named `BCAP_Measures.ipynb` is used. This notebook instantiates the `BusCorridorActionPlan` class and produces both link-based and stop-based measures using `produce_link_measures()` and `produce_stop_measures()` methods.

The `produce_link_measures()` method calls the `BCAPLinksMeasure` class in the backend, which uses a YAML configuration file similar to those used by the Corridor Explorer above. This file specifies the list of transformations required to be performed on the input link-based measures produced by the transit metrics estimation process. The transformations ensure that the results align with the specific needs of the BCAP dashboards.   

The `produce_stop_measures()` method, on the other hand, uses a combination of **trip stop timing reports** and **GTFS data** to produce the required measures at the stop level for the BCAP dashboards. In the backend, this method calls the `BCAPStopMeasures` class, where the operations are performed sequentially to ensure accurate calculation and integration of the stop-level metrics.

Once the **stop measures** and **link measures** are estimated for the BCAP dashboard, they are stored locally in a partitioned folder structure (by region and by analysis period). The output is saved in the `4_outputs/4_bcap/1_measures/[YYYYMMDD]/region=SEQ/` directory, where the `YYYYMMDD` is the analysis period. Inside this folder, the **segment measures** and **stop measures** are saved in parquet format as shown below.

```shell
|   +-- 4_outputs
|   |   +-- 1_stop_to_stop_output
|   |   +-- 2_travel_time_split
|   |   +-- 3_corridor_explorer
|   |   +-- 4_bcap
|   |   |   +-- 1_measures
|   |   |   |   +-- [YYYYMMDD]
|   |   |   |   |   +-- region=SEQ
|   |   |   |   |   |   +-- segment_measures.parquet.gz
|   |   |   |   |   |   +-- stops_measures.parquet.gz
```


#### Step 2: Appending spatial layers and Producing the Hyper files


At this stage, the Jupyter notebook named `BCAP_Attributes.ipynb` reads all the link and stop measures produced for BCAP across different periods. 
Several spatial boundaries, such as **Local Government Areas (LGAs)**, **suburbs**, **strategic road networks**, **busways**, **CBD**, **TMR regions** are then appended to the data through sequence of spatial join processes. 
These spatial labels are added to each record in the two datasets of combined link and combined stops' measures and are used within the BCAP dashboard to filter and display the results accordingly.
The final results are stored in two formats: **hyper files** for visualisation in **Tableau**.

```shell
|   +-- 4_outputs
|   |   +-- 1_stop_to_stop_output
|   |   +-- 2_travel_time_split
|   |   +-- 3_corridor_explorer
|   |   +-- 4_bcap
|   |   |   +-- 1_measures
|   |   |   +-- 2_visualisation
|   |   |   |   +-- segment_attributes_[run_version].hyper
|   |   |   |   +-- stop_attributes_[run_version].hyper
```


[High-Level Workflow]: {% link docs/overall_design/index.md %}#high-level-workflow
[GTFS regions]: {% link docs/Appendix/tables.md %}#table-1-gtfs-regions-name
