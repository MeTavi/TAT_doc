---
layout: default
title: Main Configuration
parent: Configuration Settings
nav_order: 1
---

# Main Configuration
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---



## Main Configuration File

The main configuration file (in Yaml format) is designed to provide flexible access to various data sources utilised within the Transit Analytics Tools. A customised Python module accompanies this YAML configuration file, enabling validation of the provided data. The file is structured into several high-level keys and can be expanded as needed for future applications.   

Each key contains specific configuration information as outlined below:

#### 1. `folder_locations`

This section defines the structure of the project's folder hierarchy. It contains several sub-keys, each representing
one of the main folder locations within the project:

- `src_path` (Required): Specifies the absolute path to the folder where the project's source code is stored.
    - Example: `C:/path/to/transit_analytics_tools/3_eTL/src`
- `raw_folder` (Required): Contains the relative path to the location where raw data (e.g., Ticketing data, HASTUS data,
  GTFS data) are stored. This path is relative to the `src_path`.
    - Example: `../../1_raw_data/`
- `schema_folder` (Required): Contains the relative path to the location where data schema files (in YAML format) are
  stored. This path is relative to the `src_path`.
    - Example: `../assets/schema_files`
- `input_folder` (Required): Contains the relative path to the location where input data (processed/ingested by the
  Transit Analytics Tools) are stored. This path is relative to the `src_path`.
    - Example: `../../2_input_data/`
- `output_folder` (Required): Contains the relative path to the location where output data (produced by the Transit
  Analytic Toolsets) are stored. This path is relative to the `src_path`.
    - Example: `../../4_outputs/`

#### 2. `data_items`

Under the `data_items`, different data items are listed under predefined categorisation keys. The categories defined
under the `data_items` are compatible with the folder structure defined above. The following are the categories defined:

- `raw` (Required): Lists all the data items in the `raw_folder`.
- `inputs` (Required): Lists all the data items in the `input_folder`.
- `outputs` (Required): Lists all the data items in the `output_folder`.

Each data item under any of these categories may require various parameters. This configuration file allows
parameterising paths and files. The sub-keys defined within each data item are explained below. Optional fields (
sub-keys) are indicated, and examples of their application are provided accordingly.

- `name` (Required): A custom name defined by the user, assigned to each data item. This name is used to access the data
  items programmatically and should be unique within each category (`raw`, `inputs`, `outputs`).
    - Example: `raw_transaction`, `ref_gtfs_time_periods`, `stop_to_stop_measure`
- `storage_type` (Required): Specifies the type of storage for the data item. It can be any of the predefined
  types: `local_drive`, `db_local`, or `db_cloud`. Currently, all data processed within the Corridor Analytics Toolset
  are stored on `local_drive`.
    - Example: `local_drive`
- `file_type` (Required for `local_drive`): Specifies the file type for locally stored data. Predefined data types
  include `csv`, `shapefile`, `hyper`, `geopackage`, `zip`, `parquet`. These file types are defined
  in `src.configuration.file_types`.
    - Example: `csv`, `parquet`
- `relative_path` (Required if `absolute_path` is not provided): Defined for each data item stored on a local drive and
  is relative to the base folder in the category where the data item is defined. For example, the relative path for
  items under the `inputs` key is relative to the `input_folder`. If both `relative_path` and `absolute_path` are
  provided, `absolute_path` takes precedence.
    - Example: `2_ticketing/{version}/transactions_daily_{version}.zip`
- `absolute_path` (Optional): Optionally defined for each data item stored locally. If both `relative_path`
  and `absolute_path` are provided, `absolute_path` takes precedence.
    - Example: `C:/Modelling_Projects/Data/transactions_daily_oct_2023.zip`
- `schema_file_name` (Optional): Similar to the relative path, the schema file name is a relative path to where the
  schema file for the corresponding data item is stored. It is relative to the `schema_folder` defined
  in `folder_locations`.
    - Example: `raw_transactions_schema_{schema_version}.yaml`
- `is_partitioned` (Optional): A boolean indicating whether the data is partitioned. The partitioning format is
  currently based on 'Year', 'Month', and 'Day', used for reading ticketing data (e.g., transaction data and trip stop
  timing reports). This indicator informs the process that the path for this data item should be updated accordingly
  if `is_partitioned` is set to true.
    - Example: `True` or `False`
- `schema_version` (Required for main input and output tables reading from local storage): If not provided, the reading
  and writing process considers all fields as strings. This version number follows Semantic Versioning format (e.g., "
  YYYY.MAJOR.MINOR").
    - Example: `2023.0.0`
- `requires_region` (Optional): A boolean indicating whether the data item requires a specific region in its path or
  processing.
    - Example: True or False
- `layer_name` (Optional): Specifies the name of the layer when dealing with geospatial data files, such as geopackage.
    - Example: `staged`

Additionally, `default_variables` can be defined for data items to set default values for placeholders used in paths or
other keys.

- Example: `{'version': '2023.0.0', 'schema_version': '2021.0.0'}`

#### 3. `connection`

The `connection` section contains configuration details for database connections (this is currently not used within the
Corridor Analytics Toolsets as all the data are stored on local drives). Each connection is identified by
a `connection_name`, which is used by tables with the same `connection_name`. Attributes of each connection vary based
on specific requirements, but some common keys include:

- `connection_name` (Required): Identifies the correct connection details for a given table.
    - Example: `local_postgres`
- `host` (Required): The hostname or IP address of the database server.
    - Example: `localhost`
- `port` (Required): The port number for the database server.
    - Example: `5432`
- `database_name` (Required): The name of the database to connect to.
    - Example: `sampledb`
- `user` (Required): The username for the database connection.
    - Example: `user`
- `password` (Required): The password for the database connection.
    - Example: `password`
