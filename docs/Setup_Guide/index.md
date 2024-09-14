---
layout: default
title: Setup Guide
nav_order: 2
has_toc: false  # Disable the automatic Table of Contents
---

# Setting up the process
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

The overall ETL process consists of multiple stages, where the raw data are prepared into inputs and then curated at various stages before producing the outputs used for visualisation. This section of the document provides a step-by-step guide to setting up and running the Transit Analytics ETL process. This guide is intended for new users setting up this process on a local computer.  For more information on a high-level view of the workflow refer to the [overall design] section. 
The following are the steps that need to be undertaken to set up the process:

![Setting Up Steps]({{ "/assets/images/setting_up_steps.png" | relative_url }})

### Cloning the Repository

Clone the repository to access the code on your local computer. You can use either GitHub Desktop or the command line.

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

### Setting Up the Raw Data

When the repository is cloned, it includes a high-level folder structure with empty placeholder files and folders, as shown below. However, since the raw data is not included in the repository, the user must transfer the raw data to the appropriate folders and ensure that subfolders are properly created and named under the raw data directory. 
```text
    +-- transit_analytics_tools
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

There are three main datasets that should be provided for the process, as outlined below and further explained in the [data requirement] section:
1. HASTUS Data: This includes three key items:
   - Itineraries
   - Street segment network
   - Stop locations  
See more information about the [HASTUS Data] in the Data Requirements section.
2. GTFS Data: The GTFS data used in this transit analytics process is produced by CUBIC for maximum compatibility. See more information about the [GTFS Data] in the Data Requirements section.
3. Ticketing Data: This consists of two key data items:
   - Transaction data
   - Trip stop timing reports
See more information about the [Ticketing Data] in the Data Requirements section.

{: .important-title }
> GTFS Release Date as Version Identifier 
>
> 
> The folder structure and naming conventions in the Transit Analytics Tool are centred around the GTFS release date, which acts as the version identifier. This ensures consistency throughout the tool's structure.


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
  ```shell
  env/Scripts/activate
  ```

- **Mac/Linux**:
  ```shell
  source env/bin/activate
  ```

### Update the Configuration Files
Ensure the configuration file includes the full absolute path to the `src` folder. Since all other folders are set up with relative paths, no further changes are required as long as the folder structure remains consistent.
Once the raw data is placed in the correct folder locations, review the configuration file to confirm that the file names for each raw data item are current. If any file names have changed, update the configuration file accordingly.

## More information
for more information refer:
- [Data requirement](data_requirement) - This section introduces the various data requirements and provides details on the naming conventions the user should follow when placing the raw data.

----
[overall design]: {% link docs/Overall_Workflow/index.md %}
[HASTUS Data]: {% link docs/Setup_Guide/data_requirement.md %}#hastus-data
[GTFS Data]: {% link docs/Setup_Guide/data_requirement.md %}#gtfs-data
[Ticketing Data]: {% link docs/Setup_Guide/data_requirement.md %}#ticketing-data
[data requirement]: {% link docs/Overall_Workflow/index.md %}