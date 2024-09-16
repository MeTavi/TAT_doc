---
layout: default
title: Data Requirement
parent: Setup Guide
nav_order: 2
---
# Data Requirement
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
## Raw Data Sources

The raw data comes mainly from three different sources:
### HASTUS data
HASTUS extracts, including itineraries, network data, and stop locations, are used to transfer transit metrics estimated at the stop-to-stop level to the underlying road network.

For each analysis period, the user must store these three data items in a new folder named after the analysis period, which corresponds to the GTFS release date as described in the [setup guide]. The folder should be named using the GTFS release date format (`<YYYYMMDD>`), with subfolders for the HASTUS data items placed inside, as explained below.
1. Itineraries  
   - Description: Itineraries provide the ordered sequence of links that a bus must travel between stops for every route variant (shape_id in GTFS). The itineraries' segments corresponds with the relevant trip's stop sequence as follows:
      - The first link in the fist segment corresponds to the first stop.
      - The last link in the first segment corresponds to the second stop.
      - The last link of the segment number (n-1) corresponds with the n<sup>th</sup> stop.
   - Schema: Table below shows the required attributes in the raw itinerary and the datatypes of each column based on pandas dtypes: 
    
      | Attribute       | Data Type  | Description                                                                                                                                                |
      |-----------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
      | `shape_id`      | `str`      | This uniquely identifies a route variant by direction and matches shape_id in GTFS.                                                                        |
      | `segment`       | `int64`    | This identifies stop to stop movements. The number of segments is one less than the number of stops in the raw itinerary file.                             |
      | `order`         | `int64`    | This identifies the order of links in a Segment.                                                                                                           |
      | `seg_id`        | `str`      | This identifies the link. This is the same as the `ID` field in the network.                                                                               |
      | `seg_direction` | `category` | This identifies the direction of a link and corresponds with the direction field in the network. Valid values are `To Destination`, `To Origin` and `To `. |

   {: .important-title }
    >    Itineraries and GTFS Compatibility
    > 
    >    It is essential that the itineraries are extracted on the same date as the GTFS data to ensure compatibility. 
    >

   - Folder name: `1_itineraries`
   - File naming convention: `<region>_<YYYYMMDD>_GTFS_Itineraries.csv`.
   - Folder structure:
     ```sh
         1_raw_data/
         ├── 1_hastus/
         │   └── [YYYYMMDD]/
         │      └── 1_itineraries/
         │           ├── region1_YYYYMMDD_GTFS_Itineraries.csv
         │           ├── region2_YYYYMMDD_GTFS_Itineraries.csv
         │           └── ...
     ```
     
       {: .note-title }
        > Data Validation Hint
        >
        >  All `shape_id`s from the selected GTFS feed for an analysis period must be included in the itinerary table for that same period.
2. Network

   - Description: The raw network data is provided at three different resolution levels (low, medium, and high) in shape file format. Each layer represents underlying spatial road network (at different resolution level) across the entire state. 
   - Schema: The raw network data contains several attributes that are not used within the Transit Analytics Tools; below shows only the attributes required by the process the datatypes of each column based on pandas dtypes:
       
      | Attribute | Data Type | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |:---------:|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
      |   `ID`    | `str`     | Identifies a unique link within the HASTUS system. These links IDs comes from HASTUS network source (Street Pro Nav) which over time has been modified. New links created within HASTUS has a prefix of "GIRO" and a same link ID in two different versions of the HASTUS extract may differ, especially when an old link is split.In such cases, part of the link retains the original ID, while the other part(s) receive a new ID prefixed with “GIRO". |
      |  `Flow`   | `str`     | The direction of the link that is the direction in which the link is drawn.                                                                                                                                                                                                                                                                                                                                                                                |
      | `NODE_O`  | `str`     | This is the origin node number. Similar to the `ID` field above, there may be inconsistencies for new links in HASTUS. New nodes do not appear to be created when links are split and there are several records with null Node IDs. New or split links with a GIRO prefix generally have 0 as the Node identifier.                                                                                                                                         |
      | `NODE_D`  | `str`     | This is the destination node number. Similar to the `ID` field above, there may be inconsistencies for new links in HASTUS. New nodes do not appear to be created when links are split and there are several records with null Node IDs. New or split links with a GIRO prefix generally have 0 as the Node identifier.                                                                                                                                    |
      | `SPEED2`  | `int64`   | This appears to be the posted speed of the link.                                                                                                                                                                                                                                                                                                                                                                                                           |
      | `LENGTH`  | `float64` | A SEG_LENGTH field was included in the HASTUS export. This appears to be based on the Street Pro Nav length and does not appear to have been updated for new or split links.                                                                                                                                                                                                                                                                               |

   - Folder name: `2_streetsegment_network`
   - Required shapefiles: Underlying street segments used within HASTUS at different resolutions, covering the whole of Queensland, including .shp files and their related .dbf and .shx files as follows:
       - `production_streetsegment_high.shp` 
       - `production_streetsegment_medium.shp`
       - `production_streetsegment_low.shp` 
   - File naming convention: File names should be kept as listed above. If the received raw file names are different, the user should rename them accordingly.
   - Folder structure:
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
      │          
     ```
3. Stops
   - Description: The Stops information is part of the HASTUS extract, which contains three distinct files providing details on stop locations and whether a stop is active or inactive across the entire state. For the purpose of Transit Analytics Tools, only stop_location files is utilised. 
   - Schema: The raw stop_location data contains several attributes that are not used within the Transit Analytics Tools; below shows only the attributes required by the process the datatypes of each column based on pandas dtypes:
   
      | Attribute                     | Data Type | Description                                                                          |
      |-------------------------------|-----------|--------------------------------------------------------------------------------------|
      | `stp_identifier`              | `str`     | Equivalent to the GTFS stop ID.                                                      |
      | `sloc_description_specified`  | `str`     | Contains the stop name and the associated street name.                               |
      | `loca_longitude`              | `float64` | Longitude of the stop.                                                               |
      | `loca_latitude`               | `float64` | Latitude of the stop.                                                                |
      | `loca_segment_ext_id`         | `str`     | Unique link identifier corresponding to the `ID` field in the HASTUS network.        |
      | `loca_dist_inter1`            | `float64` | Distance from the start/origin of the link.                                          |
      | `loca_dist_inter2`            | `float64` | Distance from the end/origin  of the link.                                           |
      | `loca_segment_side`           | `int64`   | Indicates which side of the road the stop is on, with 0 indicating 'To Destination'. |

  - Folder name: `3_stops`
  - Required files:
      - `stops_location.txt` (required)
      - `stops_main.txt` (optional)
      - `stops_period.txt` (optional)
  - Folder structure:
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


### GTFS data 
  
GTFS data is used to capture scheduled services within the analysis period. GTFS data should be organised by the date it was generated within HASTUS. This data comes in zipped files per region and usually are named as `<region>_GTFS.zip`. 

  - Folder name: `2_cubic\<YYYYMMDD>`
  - File naming convention: `<region>_GTFS.zip`
  {: .note }
  The region names must be explicitly included in the file names, as the process iterates through the regions.  

  - Folder structure:

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
    
  {: .note-title }
  > Cubic GTFS files
  >
  >  Cubic versions of the GTFS feeds contain additional extract files used within the Transit Analytics Tools to update and maintain the reference tables. Specifically, the route_ext and trips_ext files. The former is used to identify school, Express, Flexi, and Night services, while the latter is used to assign direction names (in addition to the direction ID) to trip IDs. 

### Ticketing data  

Ticketing data includes transactions and trip stop timing data, provided in separate files, each containing one month's worth of data. This data captures the details of actual trips and serves as the main source for estimating load and travel time, which are reported at different levels of aggregation in the final visualisation.

Since there is no clear naming convention when the files are received, the user should create a folder named in the format `<YYYYMM>`, corresponding to the data's month and year, and place both files in that folder. The files must then be renamed according to the naming conventions explained below.

  - Folder name: `<YYYYMM>`
  - File naming convention: `transactions_daily_<YYYYMM>.csv` and `tripstoptiming_daily_<YYYYMM>.csv`
  - Folder structure:
      ```sh
      data/
      ├── 1_hastus/
      ├── 2_gtfs/
      ├── 3_ticketing/
      │   └──[YYYYMM]/
      │      ├── transactions_daily_202403.csv
      │      └── tripstoptiming_daily_202403.csv
      ```

The ETL process is designed to validate, read and transform the raw data into the input data. This process is heavily
relying on the data schema that is defined for each data item above. If any change to the content of the raw data is
detected, the schema for that data item needs to be updated accordingly. 

## Reference tables

Data from different sources may use varying conventions to refer to the same service details. For example, the direction names used in the Transactions and Trip Stop Timing Report may differ. Transactions might use Northbound, Southbound, Eastbound, or Westbound, while the Trip Stop Timing data might use North, South, East, and West. Similarly, operators name in the GTFS data may differ from those used in the ticketing data.

To combine scheduling data (GTFS) with ticketing data (transactions and trip stop timing data), reference tables are employed within the ETL process. These tables act as look-up mechanisms that align data from different sources. They also facilitate the exclusion of certain services or invalid ticket types efficiently and transparently. It is important to maintain the reference tables in line with the input ticketing data. This section details the role of the reference tables and highlights key checks to perform when new data sources become available.

Three main reference tables are used in the system, as outlined below:

1. Transaction to GTFS 

    This table is used to join the transaction data and GTFS data for the same route, direction, and operators, even if different names have been used in the two sources to indicate the same direction of travel or operator. Additionally, this table includes two columns that specify whether a service is a school service and whether it should be excluded from analysis. Temporary services, such as rail replacements or services introduced for specific events, are excluded from processing. This exclusion can be adjusted by updating the reference table. Table below shows the required fields in the transaction to gtfs reference file.

    | Attribute          | Description                                                                                     |
    |--------------------|-------------------------------------------------------------------------------------------------|
    | `operator`         | The operator name as it's shown in transaction files.                                           |
    | `route`            | This is the route short name as it appears in the transaction files.                            |
    | `direction`        | This is the route direction as it appears in the transaction files.                             |
    | `angency_id`       | A numeric field corresponding the the agency id in the GTFS.                                    |
    | `route_short_name` | A route short name as it appears in the GTFS.                                                   |
    | `direction_id`     | direction id as it appears in GTFS corresponding the route direction.                           |
    | `type`             | Indicating if a service is a school service or not.                                             |
    | `include`          | A binary field indicating if the service should be included or excluded from further analysis.  |
    
    {: .note-title }
    > Usage:
    >
    > Reference Transaction to GTFS data is used by the `TransactionProcessor` for:
    > 
    > (1) getting the agency id and direction ids of each route, so they can later be joined with the scheduling data.  
    > 
    > (2) excluding temporary routes such as rail replacement services and other irregular services.
    >
    > (3) excluding the school services from the process.
  
2. Trip Stop Timing to GTFS

   Similar to the Transaction to GTFS table, this table is used to join the trip stop timing data with the GTFS data for the same route, direction, and operators, despite differing naming conventions in different sources. It also includes columns to specify school services and whether a service should be excluded from analysis. Temporary services such as rail replacements or event-specific services are excluded from processing, and these exclusions can be modified by updating the reference table. The trip stop timing to gtfs reference file follows an identical structure as the transaction to gtfs reference table with the difference that the content of this file corresponds to the trip stop timing report instead of transactions.
   {: .warning}
   > It is possible for the same combination of operator, route, and direction to refer to completely different services in two different regions. For example, route 600 in SEQ and Bundaberg, both operated by Sunbus. Although services under this operator name in regional QLD are often referred to as Sunbus-NQ, trip stop timing reports may still list them under Sunbus.

3. Ticket Status   

   This table is used to exclude transactions where the boardings or alightings are invalid based on the ticket types. Although invalid boardings or alightings are rare in a typical day, including them in the process could result in outliers in travel time estimation, which would adversely impact the aggregated metrics used in output visualisations. Therefore, it is crucial to exclude these records from the analysis. Table below shows the structure of this reference table: 

    | Attribute         | Description                                                                                             |
    |-------------------|---------------------------------------------------------------------------------------------------------|
    | `ticket_status`   | ticket_status as shown in the transactions.                                                             |
    | `boarding_valid`  | A binary field indicating if the corresponding ticket status is valid to be used as a boarding record.  |
    | `alighting_valid` | A binary field indicating if the corresponding ticket status is valid to be used as a alighting record. |

[setup guide]: {% link docs/Setup_Guide/index.md %}#setting-up-the-raw-data