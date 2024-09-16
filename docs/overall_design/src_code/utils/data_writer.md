---
layout: default
title: Data Writer
parent: Utilities
nav_order: 2
---
# Data Writer
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

The DataWriter module is designed to facilitate writing data to various output formats using an Object-Oriented Programming (OOP) approach. It supports multiple file formats including `CSV`, `HYPER`, `GeoPackage`, `Parquet` and `Shapefile`, each handled by dedicated writer classes.  This modular approach allows for extending the functionality of the package to support additional formats with minimal changes to the existing codebase.

Different methods within the `DataWriter` class instantiate relative writers to direct data write operations to the appropriate format-specific method.


**Usage Example**:

Here's a brief example to demonstrate the usage of the `DataWriter` module to write data to a CSV file:

```python
import pandas as pd
from src.utils.data_writer.data_writer import DataWriter

# Sample data
data = {
    "Name": ["John", "Alice", "Bob", "Emily"],
    "Age": [25, 28, 22, 30],
    "City": ["New York", "London", "Paris", "Sydney"],
    "Salary": [50000, 55000, 45000, 60000],
}
dataframe = pd.DataFrame(data)

# Initialise DataWriter
data_writer = DataWriter()

# Using keyword arguments:
data_writer.to_csv(
    dataframe, folder_path="path/to/destination_folder", file_name='output.csv')

# Using a configuration Dictionary
config_csv = {'folder_path': "path/to/destination_folder", 'file_name': 'output.csv'}
# Write to CSV
data_writer.to_csv(
    dataframe, config_csv)
```


Depending on the writer, the user may need to provide different set of arguments to the writer method, see table below for more details:

| Writer Method | Required Arguments                    |
|---------------|---------------------------------------|
| to_csv        | folder_path,   file_name              |
| to_parquet    | folder_path,   file_name              |
| to_shapefile  | folder_path,   file_name              |
| to_geopackage | folder_path,   file_name,  layer_name |
| to_hyper      | file_path,     table_name             |









