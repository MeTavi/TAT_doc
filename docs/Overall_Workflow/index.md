---
layout: default
title: Overall Design of the ETL Tool
nav_order: 3
has_toc: false  # Disable the automatic Table of Contents
---

# Overall Design
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overall Design

The ETL tool of Transit Analytics is built with a modular design, focusing on separating data ingestion, processing, and
output. Each stage of the ETL process is delineated to ensure maintainability, readability, and flexibility.
Below, we provide an overview of the design and structure to guide users through how the tool functions and how it was
intended to be used. 

This section of the document is structured as follows: 
1. Key Design Principles
2. ETL Components and Folder Structure
3. High-Level Workflow

## Key Design Principles

The ETL tool adheres to four core principles:

1. **Separation of Concerns**: Each step—reading, processing, and writing—is handled independently to allow for modularity and flexibility in updates or enhancements.

2. **Modularity**: The system is divided into discrete components, each handling a specific part of the process.

3. **Extensibility**: The design allows for easy integration of new data sources or functionalities without disrupting the existing workflow.

4. **Transparency**: Transparency is another key focus throughout the ETL process, ensuring that users can clearly understand how data is processed, excluded, or transformed. For instance:
    - Service exclusions are managed through reference tables, showing which services are omitted.
    - Excluded analysis dates are handled through configurable settings.
    - The aggregation module for dashboard outputs includes a transparent listing of all formulas and calculations (calculation yaml files), allowing users to trace every step and add or remove measures easily if required.

## ETL Components and Folder Structure

The ETL process is structured around a set of modular components. The folder structure shown below introduces the different components of the ETL process. This section provides an overview of the various modules within the ETL, and the next section, "High-Level Workflow," will link these components based on their roles within the workflow.

```shell
+-- Transit_Analytics_Tools_2024
|   +-- 3_etl
|   |   +-- assets
|   |   |   +-- config_files
|   |   |   +-- calculation_config
|   |   |   +-- schema_files
|   |   +-- jupyter_notebooks
|   |   +-- src
|   |   |   +-- configuration
|   |   |   +-- data_preparation
|   |   |   +-- gtfs
|   |   |   +-- ticketing
|   |   |   +-- transit_metrics
|   |   |   +-- transit_metrics_aggregate
|   |   |   |   +-- corridor_explorer
|   |   |   |   +-- bcap
|   |   |   |   +-- transformer
|   |   |   +-- utils
|   |   |   |   +-- data_reader
|   |   |   |   +-- data_writer
|   |   |   |   +-- dataframe_utils
|   |   |   |   +-- logger
```

The following high-level folders provide the core structure for the ETL process:

- assets: Contains static files such as different configuration files, and data schemas. Below are the key items under this folder:
  - main configuration file: A yaml file that is defined to specify the folder locations and different data items used throughout the ETL process. For more information about the content and maintenance of this file see `main configuration` section.  
  - calculation configuration files: Three different yaml files containing a list of all the operations that is sequentially applied on the curated datasets for estimating the link based and stop based measures of corridor explorer and the segment measures in BCAP. 
  - schema files: Several yaml file containing the data schema of each data item. For more information about the content and maintenance of these schema files refer to the 'Data Schema' section. 
- Jupyter notebooks: Contains all Jupyter notebooks used for running the ETL pipeline. These notebooks provide a hands-on approach to executing workflows and is further explained in next section High-Level Workflow.
- source: This folder contains the main ETL code and is subdivided into several key subfolders:
  - configuration: Contains modules responsible for managing main configurations. Validates the main config yaml file and provides method to easily access attributes of each data item.
  - data_preparation: A module responsible for converting raw data into intput data.
  - gtfs: A module responsible for processing and transforming GTFS data.
  - ticketing: A module handling transformation of the ticketing-related data.
  - transit_metrics: A module responsible for calculating and aggregating transit metrics.
  - transit_metrics_aggregation: A module responsible for summarising and aggregating transit metrics data to be used for either of the CE or BCAP dashboards.
- utilities: This folder contains general-purpose utility functions used across the ETL pipeline for tasks such as loading, validation, and common operations. Below are some of the important components found in this folder:
  - data_reader: A module responsible for reading data from various formats, including CSV, Parquet, GeoPackage, and more. It can be extended to handle cloud-based databases or additional file formats. The data reader also performs schema validation during the reading process and supports basic data cleaning operations, such as renaming headers, as specified by the run time configuration file.
  - data_writer: A module responsible for writing data, supporting formats such as CSV, Parquet, Hyperfile, and GeoPackage. It can be extended to write to cloud-based storage solutions. 
  - data_frame_operation: This utility provides methods for common operations on data frames, particularly focusing on joins and filtering. It is designed to assist in the validation of these operations, allowing access to both the left and right components of the join operation. The standardised approach makes it easier to reuse this class for performing data frame operations consistently across the ETL pipeline.
  - logger: Handles logging throughout the ETL process. 


## High-Level Workflow

The workflow is structured around a series of Python modules that handle the different stages of the ETL process. Users
interact with these modules primarily through predefined Jupyter Notebooks. These notebooks serve as a practical
interface for end users to ensure that the steps of the workflow can be executed in the correct order.

The high-level design of the code is outlined below, explaining the corresponding Jupyter notebook process for each step.


1. **Data Preparation Module**: The Data Preparation module is responsible for converting raw data into a format suitable for further processing. A Jupyter notebook named `1_prepare_input_data.ipynb` is used. This notebook creates an instance of `DataPreparation` and runs the following methods:
   - `prepare_stop_location()`
   - `prepare_all_itineraries()`
   - `prepare_full_network()`
   - `prepare_spatial_itinerary()`
   - `prepare_transactions()`
   - `prepare_trip_stop_timing()`

   Each method calls a specific preparation class, encapsulated under the Data Preparation interface. Once prepared, the data is saved locally in the input data folder, ready for use in subsequent steps. Below is the folder structure where the data preparation stage saves the input data:
   
   ```shell
   |   +-- 2_input_data
   |   |   +-- 1_reference_table
   |   |   +-- 2_corridor_definition
   |   |   +-- 3_hastus
   |   |   |   +-- [YYYYMMDD]
   |   |   |   |   +-- 1_itineraries
   |   |   |   |   |   +-- [region]_[YYYYMMDD]_GTFS_Itineraries.cs
   |   |   |   |   +-- 2_streetsegment_network
   |   |   |   |   |   +-- hastus_network.parquet.gz
   |   |   |   |   +-- 3_stops
   |   |   |   |   |   +-- stop_location.csv
   |   |   |   |   +-- 4_spatial_itinerary
   |   |   |   |   |   +-- [region]_[YYYYMMDD]_GTFS_Itineraries.gpk
   |   |   +-- 4_gtfs
   |   |   |   +-- 1_public
   |   |   |   +-- 2_cubic
   |   |   |   |   +-- [YYYYMMDD]
   |   |   |   |   |   +-- [region]_GTFS.zip
   |   |   +-- 5_transactions
   |   |   |   +-- partitioned
   |   |   |   |   +-- Year=[YYYY]
   |   |   |   |   |   +-- Month=[MM]
   |   |   |   |   |   |   +-- Day=[d]
   |   |   |   |   |   |   |   +-- transaction.csv
   |   |   |   |   |   |   |   +-- transaction.parquet.gz
   |   |   +-- 6_trip_stop_timing
   |   |   |   +-- partitioned
   |   |   |   |   +-- Year=[YYYY]
   |   |   |   |   |   +-- Month=[MM]
   |   |   |   |   |   |   +-- Day=[d]
   |   |   |   |   |   |   |   +-- trip_stop_timing.csv
   |   |   |   |   |   |   |   +-- trip_stop_timing.parquet.gz
   ```

2. **Estimating Transit Metrics**: After the data is ingested by the data preparation stage above, a series of data cleaning and transformation operations are applied to estimate key transit metrics, such as travel time, load, boarding, and alighting. This phase is handled by the **Transit Metrics Module**, which first calculates the metrics at the stop-to-stop level and then maps them onto the corresponding road network. To generate these metrics, the Jupyter notebook `1_produce_curated_data.ipynb` is used. This notebook instantiates the **Transit Metrics** class and runs two primary methods:
   - `produce_stop_to_stop_measures()`
   - `produce_link_measures()`
  The `produce_stop_to_stop_measures()` method calculates the transit metrics between stops. When this method is called, it saves its output (the stop-to-stop metrics) locally in the `4_outputs\1_stop_to_stop_outputs`, under a structured partition based on the analysis period, region, year, month, and day. Once the stop-to-stop metrics are saved, the `produce_link_measures()` method can be called. This method reads the outputs saved by the `produce_stop_to_stop_measures()` method from the local drive. It uses these stop-to-stop metrics as input to calculate the link-based measures, mapping them onto the road network. The link-based measures are then saved in the `4_outputs\2_travel_time_split` folder, with a similar partitioning structure as the stop to stop outputs.
  Each method instantiates additional backend classes to handle specific data processing tasks. The **Ticketing Module** processes the ticketing data, while the **GTFS Module** generates the scheduled timetable data from the GTFS input. These two modules independently process their respective data, which are then combined in the `StopToStopMeasures` class within the **Transit Metrics Module**.
  Once combined, the stop-to-stop measures are integrated with the spatial itineraries using the **SegmentLink** class, also within the **Transit Metrics Module**.
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

3. Transit Metric Aggregation: The Data Aggregation stage occurs after transit metrics have been estimated at the link level. At this point, the process branches into two distinct streams, each responsible for aggregating the results into curated datasets for the two primary dashboards: the **Corridor Explorer (CE) Dashboards** and the **Bus Corridor Action Plan (BCAP) Dashboards**.
Each of these streams has a dedicated module within the **Transit Metrics Aggregate** module. Additionally, separate Jupyter notebooks are provided for generating the outputs for each dashboard as described below:



### Producing Curated Data for Corridor Explorer (CE) Dashboard

To generate the outputs for the **Corridor Explorer (CE)** dashboard, two Jupyter notebooks are used:

1. **CE_Measures.ipynb**: This notebook generates the aggregated measures at both the link and stop levels for each corridor for a given analysis period.

2. **CE_Export_to_Hyper.ipynb**: This notebook combines the results of link and stop measures for all corridors and all periods into one file and creates a Hyper file that will be used for visualisation in the Corridor Explorer dashboard.



#### Step 1: CE_Measures.ipynb
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

#### Step 2: CE_Export_to_Hyper.ipynb

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

### Producing Curated Data for BCAP Dashboard
Similarly, to generate the required outputs for the **BCAP Dashboards**, the Jupyter notebook named `BCAP_Measures.ipynb` is used. This notebook instantiates the `BusCorridorActionPlan` class and produces both link-based and stop-based measures using the following methods:


- `produce_link_measures()`

- `produce_stop_measures()`

The `produce_link_measures()` method calls the `BCAPLinksMeasure` class in the backend, which uses a YAML configuration file. This file specifies the list of transformations required to be performed on the input link-based measures produced in step 2 (the transit metrics estimation phase). The transformations ensure that the results align with the specific needs of the BCAP dashboards. The `produce_stop_measures()` method, on the other hand, uses a combination of **trip stop timing reports** and **GTFS data** to produce the required measures at the stop level for the BCAP dashboards. In the backend, this method calls the `BCAPStopMeasures` class, where the operations are performed sequentially to ensure accurate calculation and integration of the stop-level metrics.
Once the **stop measures** and **link measures** are estimated for the BCAP dashboard, they are stored locally in a partitioned folder structure (by region and by analysis period). The output is saved in the `4_outputs/4_bcap/1_measures/[YYYYMMDD]/region=SEQ/` directory, where the `YYYYMMDD` is the analysis period. Inside this folder, the **segment measures** and **stop measures** are saved in **parquet** format. In the next stage, the Jupyter notebook named `BCAP_Attributes.ipynb` reads all the link and stop measures produced for BCAP across different periods. Several spatial boundaries, such as **Local Government Areas (LGAs)**, **suburbs**, **strategic road networks**, **busways**, **CBD**, **TMR regions** are then appended to the data through sequence of multiple spatial join processes. These spatial labels are added to each record in the two datasets of combined link and combined stops' measures and are used within the BCAP dashboard to filter and display the results accordingly.
The final results are stored in two formats: **hyper files** for visualisation in **Tableau**, and **parquet files** for programmatic access in Python, if required for other processes.





### Producing Curated Data for BCAP Dashboard

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


