---
layout: default
title: Overall Design
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

The ETL process is structured around a set of modular components. The folder structure shown below introduces the different components of the ETL process. This section provides an overview of the various modules within the ETL, and the next section, [High-Level Workflow] will link these components based on their roles within the workflow.

```shell
+-- transit_analytics_tools
|   +-- 3_etl
|   |   +-- assets
|   |   |   +-- main_config_file
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

- `assets`: Contains static files such as different configuration files, and data schemas. Below are the key items under this folder:
  - `main_config_file`: A yaml file that is defined to specify the folder locations and different data items used throughout the ETL process. For more information about the content and maintenance of this file see `main configuration` section.  
  - `calculation_config`: Three different yaml files containing a list of all the operations that is sequentially applied on the curated datasets for estimating the link based and stop based measures of corridor explorer and the segment measures in BCAP. 
  - `schema_files`: Several yaml file containing the data schema of each data item. For more information about the content and maintenance of these schema files refer to the 'Data Schema' section. 
- `jupyter_notebooks`: Contains all Jupyter notebooks used for running the ETL pipeline. These notebooks provide a hands-on approach to executing workflows and is further explained in next section High-Level Workflow.
- `src`: This folder contains the main ETL code and is subdivided into several key subfolders:
  - `configuration`: Contains modules responsible for managing main configurations. Validates the main config yaml file and provides method to easily access attributes of each data item.
  - `data_preparation`: A module responsible for converting raw data into intput data.
  - `gtfs`: A module responsible for processing and transforming GTFS data.
  - `ticketing`: A module handling transformation of the ticketing-related data.
  - `transit_metrics`: A module responsible for calculating and aggregating transit metrics.
  - `transit_metrics_aggregation`: A module responsible for summarising and aggregating transit metrics data to be used for either of the CE or BCAP dashboards.
- `utils`: This folder contains general-purpose utility functions used across the ETL pipeline for tasks such as loading, validation, and common operations. Below are some of the important components found in this folder:
  - `data_reader`: A module responsible for reading data from various formats, including CSV, Parquet, GeoPackage, and more. It can be extended to handle cloud-based databases or additional file formats. The data reader also performs schema validation during the reading process and supports basic data cleaning operations, such as renaming headers, as specified by the run time configuration file.
  - `data_writer`: A module responsible for writing data, supporting formats such as CSV, Parquet, Hyperfile, and GeoPackage. It can be extended to write to cloud-based storage solutions. 
  - `data_frame_operation`: This utility provides methods for common operations on data frames, particularly focusing on joins and filtering. It is designed to assist in the validation of these operations, allowing access to both the left and right components of the join operation. The standardised approach makes it easier to reuse this class for performing data frame operations consistently across the ETL pipeline.
  - `logger`: Handles logging throughout the ETL process. 


## High-Level Workflow

The workflow is structured around a series of Python modules that handle the different stages of the ETL process. Users
interact with these modules primarily through predefined Jupyter Notebooks. These notebooks serve as a practical
interface for end users to ensure that the steps of the workflow can be executed in the correct order.

The high-level design of the code is shown in below image and explained further in the proceeding sections:

![High-level workflow]({{ "/assets/images/overall_workflow.png" | relative_url }})

1. [Data Preparation]: This process involves transforming raw data into a format that is ready for analysis or further processing.
2. Transit Metric Estimation:  This process involves estimating key transit metrics, such as travel time (both estimated and scheduled) and passenger loading (including boarding and alighting at each stop). These metrics are calculated at the most granular level possible, specifically for each link along the travelled path of individual trips.
3. Transit Metric Aggregation: This process involves aggregating the previously estimated metrics and calculating secondary measures such as speed, travel time reliability, and delays. The level of aggregation can be adjusted depending on the specific application. These calculations produce the required outputs for the Corridor Explorer and BCAP dashboards. 

[Data Preparation]: {% link docs/overall_design/data_prep.md %}