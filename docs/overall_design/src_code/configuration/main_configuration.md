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

### Folder Locations

The `folder_locations` key defines the structure of the project's folder hierarchy. It contains several sub-keys, each representing one of the main folder locations within the project:

- `src_path` (Required): Specifies the absolute path to the folder where the project's source code is stored.
    - Example: `C:/path/to/transit_analytics_tools/3_etl/src`
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


### Data Items

The `data_items` key contains different data items listed under predefined categorisation sub-keys. The categories defined under the `data_items` are compatible with the folder structure defined above. The following are the categories defined:

- `raw` (Required): Lists all the data items in the `raw_folder`.
- `inputs` (Required): Lists all the data items in the `input_folder`.
- `outputs` (Required): Lists all the data items in the `output_folder`.

Each data item under any of these categories may require various parameters. This configuration file allows parameterising paths and files. The sub-keys defined within each data item are explained below. Optional fields (sub-keys) are indicated, and examples of their application are provided accordingly.

- `name` (Required): A custom name defined by the user, assigned to each data item. This name is used to access the data items programmatically and should be unique within each category (`raw`, `inputs`, `outputs`).
    - Example: `raw_transaction`, `ref_gtfs_time_periods`, `stop_to_stop_measure`
- `storage_type` (Required): Specifies the type of storage for the data item. It can be any of the predefined types: `local_drive`, `db_local`, or `db_cloud`. Currently, all data processed within the Transit Analytics Tools are stored on `local_drive`.
    - Example: `local_drive`
- `file_type` (Required for `local_drive`): Specifies the file type for locally stored data. Predefined data types include `csv`, `shapefile`, `hyper`, `geopackage`, `zip`, `parquet`. These file types are defined in `src.configuration.file_types`.
    - Example: `csv`, `parquet`
- `relative_path` (Required if `absolute_path` is not provided): Defined for each data item stored on a local drive and is relative to the base folder in the category where the data item is defined. For example, the relative path for items under the `inputs` key is relative to the `input_folder`. If both `relative_path` and `absolute_path` are provided, `absolute_path` takes precedence.
    - Example: `2_ticketing/{version}/transactions_daily_{version}.zip`
- `absolute_path` (Optional): Optionally defined for each data item stored locally. If both `relative_path` and `absolute_path` are provided, `absolute_path` takes precedence.
    - Example: `C:/Modelling_Projects/Data/transactions_daily_oct_2023.zip`
- `schema_file_name` (Optional): Similar to the relative path, the schema file name is a relative path to where the schema file for the corresponding data item is stored. It is relative to the `schema_folder` defined in `folder_locations`.
    - Example: `raw_transactions_schema_{schema_version}.yaml`
- `is_partitioned` (Optional): A boolean indicating whether the data is partitioned. The partitioning format is currently based on 'Year', 'Month', and 'Day', used for reading ticketing data (e.g., transaction data and trip stop timing reports). This indicator informs the process that the path for this data item should be updated accordingly if `is_partitioned` is set to true.
    - Example: `True` or `False`
- `schema_version` (Required for main input and output tables reading from local storage): If not provided, the reading and writing process considers all fields as strings. This version number follows Semantic Versioning format (e.g. `YYYY.MAJOR.MINOR`).
    - Example: `2023.0.0`
- `requires_region` (Optional): A boolean indicating whether the data item requires a specific region in its path or processing.
    - Example: True or False
- `layer_name` (Optional): Specifies the name of the layer when dealing with geospatial data files, such as geopackage.
    - Example: `staged`

Additionally, `default_variables` can be defined for data items to set default values for placeholders used in paths or other keys.

- Example: `{'version': '2023.0.0', 'schema_version': '2021.0.0'}`

### Connection

The `connection` section contains configuration details for database connections (this is currently not used within the Transit Analytics Tools as all the data are stored on local drives). Each connection is identified by a `connection_name`, which is used by tables with the same `connection_name`. Attributes of each connection vary based on specific requirements, but some common keys include:

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




## Configuration Module

The configuration module provides a structured way to manage and access various configuration settings defined within the [main configuration file] for different data types (raw, input, output). It revolves around the **ConfigReader** class, which is responsible for loading, validating and interpreting YAML config file. This module also defines entities like data types, storage types, and regions, which collectively determine how data is processed, stored, and accessed.

### ConfigReader Class

The main class that users interact with is `ConfigReader`. When instantiated, it takes a path to a YAML configuration file and loads the necessary settings. The class provides an interface for retrieving configurations for raw data, input data, and output data, based on the structure defined in the YAML file. 
Following is how this class initialised within different stages of the ETL tools which loads the configuration from the specified YAML file. 

```python
from src.configuration.config_reader import ConfigReader
        
config_file_path = r"path/to/transit_analytics_tools/3_etl/assets/config_files/config_v2024.0.xx.yaml"
config_reader = ConfigReader(config_file_path)
```
- Key Method:
   - `get_table_config`: This method retrieves the configuration of a particular data item (raw, input, or output). It returns details like file paths, storage types, partitioning information, and schema details, depending on the data type being queried. 
   Example:
   
    ```python
   
    config = config_reader.raw.get_table_config( name="itinerary")
   
    ```
   For data that requires regional and version-based partitioning, the module allows specifying default variables like `region`, `version`, and `schema_version`. These are replaced dynamically when constructing file paths or querying data configurations. For example, accessing one of the outputs of the ETL process that is partinoned and stored locally is shown below: 
    ```python
    append_corridor_config = config_reader.outputs.get_table_config(
                  "appended_corridors",
                  corridor_type='Infrastructure',
                  region='SEQ',
                  corridor_id='1041',
                  version='20240305',
              ) 
    ```
  



[main configuration file]: {% link docs/Appendix/yaml_files.md %}#main-configuration

