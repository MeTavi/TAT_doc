## Data Preparation

The Data Preparation module provides a unified interface to streamline the data processing pipeline, ensuring that all
necessary data preparation are performed consistently and organised. It processes various data items, including full
network data, itinerary data, spatial itinerary data, and ticketing data (transactions and trip stop timing). These
items are prepared for use by the Transit Analytic Toolkit, with each undergoing different levels of processing, all
encapsulated within the `DataPreparation` module.

The `DataPreparation` module manages the initialisation, configuration, and execution of specific data preparation
scripts. It utilises other modules, such as configuration, data reader, and data writer modules, to perform its tasks.
Below is a snippet of the code demonstrating the data preparation process.

```python
from src.data_preparation.data_preparation import DataPreparation
from src.configuration.config_reader import ConfigReader

config_file_path = r"path\to\config_file"
config_reader = ConfigReader(config_file_path)
version = '20240305',
itinerary_schema_version = '2024.0.0'

data_preparation = DataPreparation(config=ConfigReader)
data_preparation.prepare_itinerary(version=version, schema_verison=itinerary_schema_version)
data_preparation.prepare_full_network(version=version)
data_preparation.prepare_spatial_itinerary(version=version, schema_version=itinerary_schema_version)


```

Following describes different sub-modules within the `DataPreparation` module.

#### `prepare_full_network.py`

The `prepare_full_network.py` script is responsible for processing the raw network data into a format suitable for
further analysis and integration with other datasets. It reads raw shapefiles containing street segments categorised by
different resolution levels (low, medium, and high). The script combines these shapefiles into a single GeoDataFrame and
then preforms the required processing. For example, it sets the direction of street segments, calculate additional
attributes, or filter out unnecessary data. Finally, the processed data is saved in a standardised format, such as
GeoPackage or Parquet, for subsequent use in other parts of the ETL process.

#### `prepare_itinerary.py`

The `prepare_itinerary.py` script processes raw itinerary data to prepare it for integration with the network data and
other datasets. The script ensures that the data is cleaned and transformed to meet the requirements of the broader
analysis framework.

Processing steps include reading raw itinerary files, renumber segments of the itinerary data to be
aligned with the network segments, enabling accurate mapping of GTFS trips and to the underlying street network in the
later stages of the process. The processed itinerary data is then saved for use to local drive.

#### `prepare_spatial_itinerary.py`

The `prepare_spatial_itinerary.py` script specialises in preparing spatial itinerary data, which includes geospatial
information of the itineraries. This script reads the processed full network, and processed itineraries that is produced
in previous steps and append the spatial object to the processed itineraries.

Key processing steps involve reading the spatial itinerary files, performing spatial joins and merges with the
network
