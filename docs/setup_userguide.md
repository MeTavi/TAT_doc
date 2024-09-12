---
layout: default
title: Setup Guide
nav_order: 2
---

# Seting up the process
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
## Introduction

The overall ETL is composed of multiple stages where the raw data are prepared into inputs and then curated at multiple
stages before producing the outputs that are used for visualisation. This section of the document provides a
step-by-step guide to setting up and running the Transit Analytic ETL process. This guide is intended for new users
setting up this process on a local PC. For detailed
descriptions and additional documentation, please refer to the other relevant documents mentioned herein this document.

The user should follow the below steps where each is further described in this document:

### Clone the repository
Here is the section written in Markdown:

```markdown
### Cloning the Repository

To clone the repository, you can use either GitHub Desktop or the command line. The repository URL is:

```
https://github.com/tmr-tau/transit_analytics_tools.git

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

### Set up folder structure and place the raw data in the appropriate folders:
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

### Set up the python environment
### Check and confirm the configuration files:



This document is not dealing with technical stages involved in data transformation, or the design of the ETL process,
instead it intends to
guide a
new user on how to set up the process for the first time before running the ETL tool for a new analysis period with new
data.

## Prerequisites

1. **Raw Data Files**: Raw data files are not stored in the repository. Ensure you have access to the raw data files needed for processing.
2. **Folder Structure**: A template of empty folders in the required structure is created when cloning the repository; however, the user should set it up by renaming the folders based on the period being analysed. More details can be found in the following section titled 'Folder Structure.'.
3. **Git account**: Access to TMR-TAU Git account.
