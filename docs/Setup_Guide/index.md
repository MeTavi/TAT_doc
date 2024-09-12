---
layout: default
title: Setup Guide
nav_order: 2
has_toc: false  # Disable the automatic Table of Contents
---

# Seting up the process
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

1. **Raw Data Files**: Raw data files are not stored in the repository. Ensure you have access to the raw data files needed for processing.
2. **Folder Structure**: A template of empty folders in the required structure is created when cloning the repository; however, the user should set it up by renaming the folders based on the period being analysed. More details can be found in the following section titled 'Folder Structure.'.
3. **Git account**: Access to TMR-TAU Git account.

## Instruction

The overall ETL is composed of multiple stages where the raw data are prepared into inputs and then curated at multiple
stages before producing the outputs that are used for visualisation. This section of the document provides a
step-by-step guide to setting up and running the Transit Analytic ETL process. This guide is intended for new users
setting up this process on a local PC. For detailed
descriptions and additional documentation, please refer to the other documentation sections. Following is the steps required to be undertaken:

### Cloning the Repository

To clone the repository, you can use either GitHub Desktop or the command line.

#### Option 1: Using GitHub Desktop


1. Open **GitHub Desktop**.
2. Go to **File** > **Clone Repository**.
3. In the "URL" tab, paste the repository URL:
   ```
   https://github.com/tmr-tau/transit_analytics_tools.git
   ```
4. Choose the local path where you want to save the repository.
5. Click **Clone**.

GitHub Desktop will download the repository and set it up on your local machine.

#### Option 2: Using the Command Line  


1. Open your command prompt (or terminal).
2. Navigate to the directory where you want to clone the repository.
3. Run the following command:

    ```bash
    git clone https://github.com/tmr-tau/transit_analytics_tools.git
    ```
This will download the repository and create a folder named `transit_analytics_tools` in your current directory.
Now, you can navigate into the repository folder and start working with the project.

### Setting Up the Raw Data Directory

When the repository is cloned, it includes a high-level folder structure with empty placeholder files and folders, as shown below. However, since the raw data is not included in the repository, the user must transfer the raw data to the appropriate folders and ensure that subfolders are properly created and named under the raw data directory. 
```text
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

There are three main datasets that must be provided for the process, as outlined below and further explained in the [data requirements](data_requirements) section:
1. HASTUS Data: This includes three key items:
    - Itineraries 
    - Street segment network 
    - Stops Location
2. GTFS Data: The GTFS data used in this transit analytics process is produced by CUBIC for maximum compatibility.
3. Ticketing Data: This consists of two key data items:
   - Transaction data 
   - Trip stop timing reports 

{: .note } 
   The entire folder structure and naming conventions used within the transit analytics tool are based on the GTFS release date. It is essential that the itineraries are extracted on the same date as the GTFS data to ensure compatibility. As a result, the GTFS release date acts as the version identifier and is consistently used across the folder structure and workflow. The following sections describe the naming conventions and folder structure for each of these datasets. The content and processing of the raw data will be discussed in the proceeding sections.

{: .highlight }
A paragraph

### Setting Up the Python Environment

To set up the Python environment for this project, there is a batch file that automates the process of creating a new virtual environment and installing all the required libraries. Follow the steps below to get started:

1. Navigate to the `3_etl` folder within the project directory:

   ```shell
   transit_analytic_tools/3_etl
   ```

2. Run the batch file called `setup.bat`. This will create a Python virtual environment and install the necessary dependencies:
   - Double-click the `setup.bat` file to run it.
   - Alternatively, you can run it from the command line:
     ```shell
     transit_analytic_tools/3_etl/setup.bat
     ```
     The `setup.bat` file runs the following commands to set up the environment:

      ```shell
      python -m venv env        # Creates a virtual environment in the 'env' folder
      source env/bin/activate   # Activates the virtual environment
      pip install -r requirements.txt  # Installs the required libraries from the requirements.txt file
      ```

      After running the `setup.bat` file, you will have a fully configured Python environment with all necessary libraries installed.

3. Activating the Environment

Once the environment is set up, you can activate it by running the following command:

- **Windows**:
  ```text
  env/Scripts/activate
  ```

- **Mac/Linux**:
  ```text
  source env/bin/activate
  ```

### Check and confirm the configuration files

Ensure that the configuration file contains the full absolute path to the src folder. All other folders are set up with relative paths, so no further updates are needed as long as the folder structure remains consistent.

After placing the raw data in the appropriate folder locations as described above, check the configuration file to ensure that the file names defined for each raw data item are up to date. If any input file names have changed, update the configuration file accordingly.

## More information
for more information refer to :
- [Data requirements](data_requirements) - This section introduces the various data requirements and provides details on the naming conventions the user should follow when placing the raw data.
