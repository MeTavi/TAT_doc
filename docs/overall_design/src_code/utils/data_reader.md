---
layout: default
title: Data Reader
parent: Utilities
nav_order: 1
---
# Data Reader
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

The Data Reader module is responsible for reading, validating, and preparing data for the ETL process. It uses configuration files to understand the structure of the data, including file paths, data types, and schema requirements. This module supports various data readers, each designed to handle specific types of data, such as CSV files, geospatial data, and compressed zip archives. Additionally, this module incorporates validation mechanisms to ensure data integrity before proceeding to the next stages of processing.

The DataReader class, along with its extensions, utilises configuration files and input parameters to load, validate, and process data. These configuration files typically define the source, schema, and transformation rules for the data being ingested.

1.	Reading Data 
    The DataReader reads from various sources, including `.csv`, `.txt`, `parquet`, compressed `zip` files, and geospatial datasets like `.gpkg` and `.shp`. Based on the configuration, it fetches the required files and interprets them into a Pandas DataFrame or GeoDataFrame.
2. Apply the Correction:
   After reading the data, if the data schema is provided, a series of validation checks are performed to ensure the data conforms to the expected schema and integrity rules. 
   This includes applying corrections that are defined within the schema. These corrections are generally very basics and often necessary for the data to be later interpreted into the correct data types. checking for null values, verifying data types, and applying custom validation rules specific to the data source.
3. Renaming Columns:
   Following the data correction stage, if it is specified in the schema file, the columns are renamed where required. This is useful for situations where the data provided to the process with a different set of column names than is required by the process.
4. Schema Validation:
   Following the previous steps, the dataframes is validated against the input schema. The schema defines the structure of the data, including column names, data types, and nullability. Each DataReader class verifies that the loaded data conforms to the expected schema. 
   Certain columns are not allowed to contain null values. The validation step checks for nulls in these critical columns and raises exceptions if any are found. The DataReader can apply additional custom validation rules based on the configuration. For example, for geospatial data, it checks that all geometries in a GeoDataFrame are valid.
5. Optional Filtering: 
   If specified explicitly, the data reader class filters the data to only required columns, based on the input schema.  
     

**Usage Example**:


The DataReader is extensively used within various stages of the Transit Analytics Tools, where it uses the configuration inputs for a specific data items and returns a validated dataframe. Here's a brief example to demonstrate the usage of the `DataReader`:

```python
    from src.utils.data_reader.data_reader import DataReader
    from src.configuration.config_reader import ConfigReader
    from datetime import date
    
    
    config_file_path = r"path/to/transit_analytics_tools/3_etl/assets/config_files/config_v2024.0.xx.yaml"
    config_reader = ConfigReader(config_file_path)
    
    link_based_config = config_reader.outputs.get_table_config(
                "link_based_measures",
                date(2023,3,8),
                version='20230305',
                region='SEQ',
            )
    data_reader = DataReader()
    df = data_reader.read(**link_based_config)
```
