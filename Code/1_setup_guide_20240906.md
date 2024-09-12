# Transit Analytic Tools - Setup Guide

## Introduction

The overall ETL is composed of multiple stages where the raw data are prepared into inputs and then curated at multiple
stages before producing the outputs that are used for visualisation. This section of the document provides a
step-by-step guide to setting up and running the Transit Analytic ETL process. This guide is intended for new users
setting up this process on a local PC. For detailed
descriptions and additional documentation, please refer to the other relevant documents mentioned herein this document.

The user should follow the below steps where each is further described in this document:

1. Clone the repository.
2. Set up folder structure and place the raw data in the appropriate folders.
3. Set up the python environment and install the required libraries.
4. Check and confirm the configuration files as per the updated folder structure.


This document is not dealing with technical stages involved in data transformation, or the design of the ETL process,
instead it intends to
guide a
new user on how to set up the process for the first time before running the ETL tool for a new analysis period with new
data. 

## Prerequisites

1. **Raw Data Files**: Raw data files are not stored within the repository. Ensure you have the access to the raw data
   files required for processing. 
2. **Folder Structure**: A template of empty folders in the required structure is created when cloning the repository,
   however, the user should set it up (rename the folders based on the period being analysed). A more description can be
   found in the proceeding section titled "Folder Structure".
3. **Git account**: Access to TMR-TAU Git account.


## Steps to follow:

1. Clone the repository:

   ```sh
    git clone TransitCorridorAnalytics2024
    ```

2. Set up folder structure and place the raw data in the appropriate folders:
   When the repository is cloned, it includes a high-level folder structure with empty placeholder files and folders, as shown below. However, since the raw data is not included in the repository, the user must transfer the raw data to the appropriate folders and ensure that subfolders are properly created and named under the raw data directory.
```shell
+-- Transit_Analytics_Tools_2024
|   +-- 1_raw_data
|   |   +-- 1_hastus
|   |   +-- 2_ gtfs
|   |   +-- 3_ticketing
|   +-- 2_input_data
|   |   +-- 1_reference_tables
|   |   +-- 2_corridor_definition
|   |   +-- 3_hastus
|   |   +-- 4_gtfs
|   |   +-- 5_transactions
|   |   +-- 6_trip_stop_timing
|   +-- 3_etl
|   |   +-- assets
|   |   +-- process
|   |   +-- src
|   +-- 4_outputs
|   |   +-- 1_stop_to_stop_outputs
|   |   +-- 2_travel_time_split
|   |   +-- 3_corridor_explorer
|   |   +-- 4_bcap
```
There are three main datasets that must be provided for the process:
1. HASTUS Data: This includes three key items:
    - Itineraries 
    - Street segment network 
    - Stops Location
2. GTFS Data: The GTFS data used in this transit analytics process is produced by CUBIC for maximum compatibility.
3. Ticketing Data: This consists of two key data items:
   - Transaction data 
   - Trip stop timing reports 
The entire folder structure and naming conventions used within the transit analytics tool are based on the GTFS release date. It is essential that the itineraries are extracted on the same date as the GTFS data to ensure compatibility. As a result, the GTFS release date acts as the version identifier and is consistently used across the folder structure and workflow.
The following sections describe the naming conventions and folder structure for each of these datasets. The content and processing of the raw data will be discussed in the proceeding sections.

2.1. **HASTUS Data**

HASTUS data includes itineraries, network, and stops data, which must be organised as follows.

A new subfolder should be created and named based on the relevant GTFS release date (<YYYYMMDD>). The subfolders for the HASTUS data items will be placed within this folder, as explained below.

2.1.1 **Itineraries**

- **Folder Name**: `1_itineraries`
- **File Naming Convention**: `<region>_<YYYYMMDD>_GTFS_Itineraries.csv`.
- **Example**:
    ```sh
    1_raw_data/
    ├── 1_hastus/
    │   └── [YYYYMMDD]/
    │      └── 1_itineraries/
    │           ├── region1_YYYYMMDD_GTFS_Itineraries.csv
    │           ├── region2_YYYYMMDD_GTFS_Itineraries.csv
    │           └── ...
  
  __note__: region_1 and region_2 are indicative of different regions.
    ```

2.1.2 **Network**

- **Folder Name**: `2_streetsegment_network`
- **Required Shapefiles**: Underlying street segments used within HASTUS at different resolutions covering the whole of Queensland.
- **File Naming Convention**:
    - `production_streetsegment_high.shp`
    - `production_streetsegment_medium.shp`
    - `production_streetsegment_low.shp`
- **Example**:
    ```sh
    1_raw_data/
    ├── 1_hastus/
    │   └── [YYYYMMDD]/
    │      └── 1_itineraries/
    │      └── 2_streetsgment_network/
    │          ├── production_streetsegment_high.shp
    │          ├── production_streetsegment_high.dbf
    │          ├── production_streetsegment_high.shx
    │          ├── production_streetsegment_medium.shp
    │          ├── production_streetsegment_medium.dbf
    │          ├── production_streetsegment_medium.shx
    │          ├── production_streetsegment_low.shp
    │          ├── production_streetsegment_low.dbf
    │          └── production_streetsegment_low.shx    
    │          └── (related .dbf and .shx files)
    ```

2.1.3 **Stops**

- **Folder Name**: `3_stops`
- **Required Files**:
    - `stops_location.txt`
    - `stops_main.txt`
    - `stops_period.txt`
- **Example**:
    ```sh
    1_raw_data/
    ├── 1_hastus/
    │   └── [YYYYMMDD]/
    │      └── 1_itineraries/
    │      └── 2_streetsgment_network/
    │      └── 3_stops/
    │          ├── stops_location.txt
    │          ├── stops_main.txt
    │          └── stops_period.txt
    ```

2.2. **GTFS Data**

GTFS data should be organised by the date it was generated within HASTUS. This data is

- **Folder Name**: `2_gtfs\2_cubic`
- **Folder Structure**: `<YYYYMMDD>`.
- **File Naming Convention**: `<region>_GTFS.zip`
- **NOTE**: The region names must be explicitly included in the file names, as the process iterates through the regions. 
    ```sh
    1_raw_data/
    ├── 1_hastus/
    ├── 2_gtfs/
    │   └── 2_cubic/
    │       └──[YYYYMMDD]/
    │          ├── region1_GTFS.zip
    │          ├── region2_GTFS.zip
    │          └── ...
    ```

2.3. **Ticketing Data**

Ticketing data includes transactions and trip stop timing data for each month.

- **Folder Structure**: `<YYYYMM>`
- **File Naming Convention**: `transactions_daily_<YYYYMM>.csv` and `tripstoptiming_daily_<YYYYMM>.csv`
- **Example**:
    ```sh
    data/
    ├── 1_hastus/
    ├── 2_gtfs/
    ├── 3_ticketing/
    │   └──[YYYYMMDD]/
    │      ├── transactions_daily_202403.csv
    │      └── tripstoptiming_daily_202403.csv
    ```

3. Set up the python environment: There is a batch file set up to create a new python environment and install all the
   required libraries. Run the batch file called `setup.bat` located under the `transit_analytic_tools\3_ETL`. 
This batch file essentially creates a virtual environment and installs the requirement: 
```shell
python -m venv env
source env/bin/activate
pip install -r requirements.txt

```
4. Check and confirm the configuration file: Ensure that the configuration file contains the full absolute path to the src folder. All other folders are set up with relative paths, so no further updates are needed as long as the folder structure remains consistent.

After placing the raw data in the appropriate folder locations as described above, check the configuration file to ensure that the file names defined for each raw data item are up to date. If any input file names have changed, update the configuration file accordingly.


### How to run the code : 

1. Check and validate the reference files with respect to the input data.
2. Prepare input data.
3. Produce the stop to stop measures.
4. Produce the link-based measures.
5. Produce the curated data sets for Corridor Explorer.
6. Produce the curated data sets for BCAP.

