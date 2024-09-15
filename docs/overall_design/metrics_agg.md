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

1. Producing curated data for Corridor Explorer dashboards: The **CE_Measures** notebook generates the aggregated measures at both the link and stop levels for each corridor for a given analysis period.

2. Producing the Hyper files for Corridor Explorer dashboards: The  **CE_Export_to_Hyper** notebook combines the results of link and stop measures for all corridors and all periods into one file and creates a Hyper file that will be used for visualisation in the Corridor Explorer dashboard.



#### Step 1: Producing Curated Data for Corridor Explorer Dashboard
The `CE_Measures.ipynb` notebook creates an instance of the `CorridorExplorer` class and runs the following three methods to process the link-based measures generated in the previous steps:

1. **`append_corridors()`**  
This method reads and combines all the link-based measures produced in the previous steps for a given analysis period. The combined data is appended to the **Corridor Definition**, and the results are saved in a structured partition, where each file contains the link-based measures related to a specific corridor. Below is the folder structure where the appended corridors are saved locally. 

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

2. **`produce_all_corridors_stop_measure()`** and **`produce_all_corridors_link_measure()`**
After appending the link-based data to the corridors, the resulting dataset is used for the next two methods. Both the `produce_all_corridors_stop_measure()` and `produce_all_corridors_link_measure()` methods relay on a dedicated YAML configuration file to perform the necessary calculations and aggregations. These methods generate stop-based and link-based metrics, such as passenger load, travel time, and delays, at the corridor level for the entire analysis period.
The results from these methods are saved in a structured format, similar to the output of append_corridors(). However, the key difference is that while append_corridors() consolidates daily data into each file, these methods produce aggregated data for the entire analysis period per link of a corridor.

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

The CE_Export_to_Hyper.ipynb notebook combines the link and stop measures from all corridors and all analysis periods. It then exports this consolidated data into Hyper files for use in the Corridor Explorer dashboard in Tableau.

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
Similarly, to generate the required data for the **BCAP Dashboards**, the Jupyter notebook named `BCAP_Measures.ipynb` is used. This notebook instantiates the `BusCorridorActionPlan` class and produces both link-based and stop-based measures using the following methods:


- `produce_link_measures()`  
- `produce_stop_measures()`  

The `produce_link_measures()` method calls the `BCAPLinksMeasure` class in the backend, which uses a YAML configuration file. This file specifies the list of transformations required to be performed on the input link-based measures produced in step 2 (the transit metrics estimation phase). The transformations ensure that the results align with the specific needs of the BCAP dashboards. The `produce_stop_measures()` method, on the other hand, uses a combination of **trip stop timing reports** and **GTFS data** to produce the required measures at the stop level for the BCAP dashboards. In the backend, this method calls the `BCAPStopMeasures` class, where the operations are performed sequentially to ensure accurate calculation and integration of the stop-level metrics.
Once the **stop measures** and **link measures** are estimated for the BCAP dashboard, they are stored locally in a partitioned folder structure (by region and by analysis period). The output is saved in the `4_outputs/4_bcap/1_measures/[YYYYMMDD]/region=SEQ/` directory, where the `YYYYMMDD` is the analysis period. Inside this folder, the **segment measures** and **stop measures** are saved in **parquet** format. In the next stage, the Jupyter notebook named `BCAP_Attributes.ipynb` reads all the link and stop measures produced for BCAP across different periods. Several spatial boundaries, such as **Local Government Areas (LGAs)**, **suburbs**, **strategic road networks**, **busways**, **CBD**, **TMR regions** are then appended to the data through sequence of multiple spatial join processes. These spatial labels are added to each record in the two datasets of combined link and combined stops' measures and are used within the BCAP dashboard to filter and display the results accordingly.
The final results are stored in two formats: **hyper files** for visualisation in **Tableau**, and **parquet files** for programmatic access in Python, if required for other processes.


#### Producing Curated Data for BCAP Dashboard

To generate the outputs for the **BCAP (Bus Corridor Action Plan)** dashboard, two Jupyter notebooks are used:


1. **BCAP_Measures.ipynb**: This notebook generates the link-based and stop-based measures for each period.
2. **BCAP_Export_to_Hyper.ipynb**: This notebook combines the results from all periods and creates a Hyper file for use in Tableau.


#### Step 1: BCAP_Measures.ipynb

The first notebook, `BCAP_Measures.ipynb`, initialises the `BusCorridorActionPlan` class and uses two key methods to generate the required link and stop measures:


1. **`produce_link_measures()`**  
   This method processes link-based metrics using the **BCAPLinksMeasure** class. The input to this method is the link-based measures produced during the **Transit Metrics Estimation** phase (Step 2 of above). In the backend, the method uses a YAML configuration file to define the necessary transformations and aggregations, similar to the process of estimating measures in the corridor explorer.

2. **`produce_stop_measures()`**  
   This method generates stop-based metrics using **trip stop timing reports** and **GTFS data** as the inputs.  In the backend, the method  instantiates **BCAPStopMeasure** class, which performs sequential operations on the trip stop timing reports and joins the result to the stops information extracted from the GTFS data. 

The outputs from these two methods (link-based and stop-based measures) are saved in a partitioned structure based on the analysis period, region, and corridor. Once the stop and link measures are estimated for each period, they are saved and ready to be processed further. below shows the 

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


#### Step 2: BCAP_Export_to_Hyper.ipynb

Once the stop and link measures for each period have been produced, the second notebook, `BCAP_Export_to_Hyper.ipynb`, is used to reads the stop and link measures produced for all corridors and all analysis periods. It consolidates the links and stops and combines them into two separate **Hyper files**, which can be used for visualisation purposes in the BCAP Tableau dashboard.

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
