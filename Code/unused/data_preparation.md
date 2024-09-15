


## Data Preparation Module

The Data Preparation module is responsible for converting raw data into a format suitable for further processing. A Jupyter notebook named `1_prepare_input_data.ipynb` is used. This notebook creates an instance of `DataPreparation` and runs the following methods:
   - `prepare_full_network()`
   - `prepare_all_itineraries()`
   - `prepare_spatial_itinerary()`
   - `prepare_stop_location()`
   - `prepare_transactions()`
   - `prepare_trip_stop_timing()`

Each method calls a specific preparation class, encapsulated under the Data Preparation interface. Once prepared, the data is saved locally in the input data folder, ready for use in subsequent steps. Below is a brief description data preparation occurs at each step:
1. Prepare Full Network

   The raw network data, as described before, is provided at three different resolution levels (low, medium, and high) in shape file format. Each layer represents underlying spatial road network (at different resolution level) across the entire state. But in its raw form, one spatial object is defined for both directions, and the directionality of that single spatial object is based on how the spatial object was drawn, not on the actual direction of travel. 
   The data preparation stage combines these three different resolutions networks into a single dataset, and corrects the directionality by creating a separate link per direction for the entire network. Additionally, the `LENGTH` attributes of the links are updated to reflect the correct spatial objects' length.
   The final processed data is stored in Parquet format, which is significantly more efficient for both processing and storage compared to raw spatial file formats like shapefiles.
   
   In the raw network files, the direction of the link either `To Destination` (that is, the link is drawn in the direction of travel for a one way link), `To Origin` (that is, the link is drawn against the direction of travel for a one way link. This is less common), or in `Both` directions (for two-way links) is indicated in `FLOW` attributes as shown in table below.
   
   | `FLOW` | `Direction`     | Description                                        |
   |--------|-----------------|----------------------------------------------------|
   | OD     | To Destination  | from origin node to destination                    |
   | ODB    | To Destination  | from origin node to destination (except for bikes) |
   | DO     | To Origin       | from destination node to origin (except for bikes) |
   | DOB    | To Origin       | from destination node to origin (except for bikes) |
   | Null   | Both            | both directions                                    |
   
   The `PrepareFullNetwork` class within the data preparation module is responsible for preforming the above-mentioned preparation step. The directional corrections of the links are performed as follows: 
   - It reverses the direction of links with `FLOW` values equal to `DO`, `DOB` to correct the link directions to be the direction of travel. 
   - It also generates a new spatial object for two-way links, so that the end result contains one link per direction. 
   - Links with `FLOW` values equal to `OD`, `ODB` remains unchanged as the direction in which they are drawn is the same the direction of travel.

2. Prepare All Itineraries

   The `prepare_all_itineraries()` method iterates through all the [GTFS regions] and processes each itinerary file by calling the `prepare` method from the `PrepareItinerary` class. The purpose is to ensure that the segment numbers are aligned with the stop sequences. The steps involved in updating the segments are as follows:
   - Segment Renumbering: The segment number on the last link of the final segment is updated to match the last stop sequence. The order of this segment is adjusted, setting its value to 1.
   - Cumulative Segment Count: A new column is added to the dataset, which shows the cumulative count of segments for each shape_id.
   The processed itinerary data is then saved in csv format for further use in the ETL process. 

3. Prepare Spatial Itinerary

   Once the itinerary data has been processed, the spatial itinerary data is prepared by merging the spatial objects (from the full network) with the corresponding itinerary data. This ensures that the geospatial details are integrated into the itineraries, making the process of reading the spatial network much easier, as only the part of the network relevant to the region of interest is loaded into memory, rather than the full network.

4. Prepare Stop Locations

   The `prepare_stop_location` method within the `DataPreparation`, processes stop location data by calling the `prepare` method from the `PrepareStopLocation` class. The process involves the following steps:
   - Column Mapping: The method first performs a simple mapping of column names to ensure that only the required columns from the input data are returned for further processing. 
   - Inferring Direction: It then infers the direction of the link where each stop is located by analysing the loca_segment_side attribute, which indicates the side of the road or link the stop is positioned on.
   The processed stop location data is then saved in csv format for further use in the ETL process. 
   
5. Ticketing Data Preparation

   The `prepare_transactions` and `prepare_trip_stop_timing` methods from the `DataPreparation` class perform a similar function for their respective datasets. These methods call the `prepare` method from the `PrepareTicketing` class, where the data is read and partitioned by the year, month, and day of the `period dates`. The partitioned data is then stored locally in a structured format, allowing for efficient access and processing in subsequent sections of the ETL.


  Below is the folder structure where the data preparation stage saves the input data:
   
   ```shell
   |   +-- 2_input_data
   |   |   +-- 1_reference_table
   |   |   +-- 2_corridor_definition
   |   |   +-- 3_hastus
   |   |   |   +-- [YYYYMMDD]
   |   |   |   |   +-- 1_itineraries
   |   |   |   |   |   +-- [region]_[YYYYMMDD]_GTFS_Itineraries.cs
   |   |   |   |   +-- 2_streetsegment_network
   |   |   |   |   |   +-- hastus_network.parquet.gz
   |   |   |   |   +-- 3_stops
   |   |   |   |   |   +-- stop_location.csv
   |   |   |   |   +-- 4_spatial_itinerary
   |   |   |   |   |   +-- [region]_[YYYYMMDD]_GTFS_Itineraries.gpk
   |   |   +-- 4_gtfs
   |   |   |   +-- 1_public
   |   |   |   +-- 2_cubic
   |   |   |   |   +-- [YYYYMMDD]
   |   |   |   |   |   +-- [region]_GTFS.zip
   |   |   +-- 5_transactions
   |   |   |   +-- partitioned
   |   |   |   |   +-- Year=[YYYY]
   |   |   |   |   |   +-- Month=[MM]
   |   |   |   |   |   |   +-- Day=[d]
   |   |   |   |   |   |   |   +-- transaction.csv
   |   |   |   |   |   |   |   +-- transaction.parquet.gz
   |   |   +-- 6_trip_stop_timing
   |   |   |   +-- partitioned
   |   |   |   |   +-- Year=[YYYY]
   |   |   |   |   |   +-- Month=[MM]
   |   |   |   |   |   |   +-- Day=[d]
   |   |   |   |   |   |   |   +-- trip_stop_timing.csv
   |   |   |   |   |   |   |   +-- trip_stop_timing.parquet.gz
   ```

