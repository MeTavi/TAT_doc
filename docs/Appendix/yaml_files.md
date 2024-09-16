---
layout: default
title: Appendix B
parent: Appendix
nav_order: 1
has_toc: false  # Disable the automatic Table of Contents
---


# Appendix B - Yaml Files
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}


## Main Configuration

```yaml
folder_locations:
  src_path: 'C:/Modelling_Projects/transit_analytics_tools/3_etl/src'
  raw_folder: "../../1_raw_data/"
  input_folder: "../../2_input_data/"
  schema_folder: "../assets/schema_files"
  config_folder: "../assets/config_files"
  output_folder: "../../4_outputs/"

data_items:
  raw:
    - name: 'itinerary'
      relative_path: '1_hastus/{version}/1_itineraries/{region}_{version}_GTFS_Itineraries.csv'
      schema_file_name: 'hastus_raw_itinerary_schema_{schema_version}.yaml'
      column_names: [ 'shape_id','segment','order','seg_direction','seg_id','segment_order' ]
      processed_folder: '3_hastus/{version}/1_itineraries'
      processed_file_name: '{region}_{version}_GTFS_Itineraries.csv'
      default_variables:
        region: 'SEQ'
        version: '20221014'
        schema_version: '2023.0.0' # if did not have a column header

    - name: 'full_network'
      relative_path: '1_hastus/{version}/2_streetsegment_network'
      processed_folder: '3_hastus/{version}/2_streetsegment_network'
      processed_file_name: 'hastus_network.parquet.gz'
      default_variables:
        version: '202310'

    - name: 'stop_location'
      relative_path: '1_hastus/{version}/3_stops/stop_location.txt'
      processed_folder: '3_hastus/{version}/3_stops'
      processed_file_name: 'stop_location.csv'
      default_variables:
        version: '20221014'

    - name: 'transactions'
      relative_path: '3_ticketing/{version}/transactions_daily_{version}.csv'
      schema_file_name: 'raw_transactions_schema_{schema_version}.yaml'
      partition_by: 'period_date'
      processed_folder: '5_transactions/partitioned'  # When the raw transaction file is processed, the partitioned data is saved under this location. This path is relative to the input folder defined in the folder structure.
      processed_file_name: 'transaction.csv'
      default_variables:
        version: '202310'
        schema_version: '2024.0.0'

    - name: 'trip_stop_timing'
      relative_path: '3_ticketing/{version}/trip_stop_timing_daily_{version}.csv'
      schema_file_name: 'trip_stop_timing_schema_{schema_version}.yaml'
      partition_by: 'period_date'
      processed_folder: '6_trip_stop_timing/partitioned'
      processed_file_name: 'tripstoptiming.csv'
      default_variables:
        version: '202310'
        schema_version: '2024.0.0'

  inputs:
    - name: "ref_gtfs_time_periods"
      relative_path: '1_reference_tables/ref_{version}/ref_gtfs_time_periods.csv'
      schema_file_name: "ref_gtfs_time_periods_schema_{schema_version}.yaml"
      default_variables:
        version: '2023.0.0'
        schema_version: '2021.0.0'

    - name: "ref_transaction_ticket_status"
      relative_path: '1_reference_tables/ref_{version}/ref_ticket_status.csv'
      schema_file_name: "ref_ticket_status_schema_{schema_version}.yaml"
      default_variables:
        version: '2023.0.0'
        schema_version: '2021.0.0'

    - name: "ref_transaction_to_gtfs"
      storage_type: local_drive
      file_type: csv
      is_partitioned: false
      relative_path: '1_reference_tables/ref_{version}/ref_transaction_to_gtfs.csv'
      schema_file_name: "ref_transaction_to_gtfs_schema_{schema_version}.yaml"
      default_variables:
        version: '2023.0.0'
        schema_version: '2021.0.0'

    - name: "ref_trip_stop_timing_to_gtfs"
      storage_type: local_drive
      file_type: csv
      is_partitioned: false
      relative_path: '1_reference_tables/ref_{version}/ref_trip_stop_timing_to_gtfs.csv'
      schema_file_name: 'ref_trip_stop_timing_to_gtfs_schema_{schema_version}.yaml'
      default_variables:
        version: '2023.0.0'
        schema_version: '2023.0.0'


    - name: "corridor_definition"
      storage_type: local_drive
      file_type: csv
      relative_path: "2_corridor_definition/corridor_definition_{version}/seq_corridors_segments.csv"
      schema_file_name: 'corridor_definition_schema_{schema_version}.yaml'
      default_variables:
        version: "2023.0.0"
        schema_version: '2024.0.0'


    - name: "transactions"
      is_partitioned: true
      relative_path: "5_transactions/partitioned/Year={year}/Month={month}/Day={day}/transaction.parquet.gz"
      schema_file_name: "transactions_schema_{schema_version}.yaml"
      default_variables:
        schema_version: '2023.0.0'

    - name: "trip_stop_timing"
      storage_type: local_drive
      file_type: csv
      is_partitioned: true
      relative_path: "6_trip_stop_timing/partitioned/Year={year}/Month={month}/Day={day}/tripstoptiming.parquet.gz"
      schema_file_name: "trip_stop_timing_schema_{schema_version}.yaml"
      default_variables:
        schema_version: '2023.0.0'

    - name: "full_network"
      storage_type: "local_drive"
      file_type: "geopackage"
      requires_region: false
      relative_path: "3_hastus/{version}/2_streetsegment_network/hastus_network.gpkg"
      layer_name: 'processed_streetsegment'
      default_variables:
        schema_version: '2023.0.0'

    - name: "itinerary"
      storage_type: "local_drive"
      file_type: "csv"
      requires_region: true
      relative_path: "3_hastus/{version}/1_itineraries/{region}_{version}_GTFS_Itineraries.csv"
      schema_file_name: "hastus_raw_itinerary_schema_{schema_version}.yaml"
      default_variables:
        region: "SEQ"
        version: "20230307"
        schema_version: "2023.0.0"

    - name: "spatial_itinerary"
      storage_type: "local_drive"
      file_type: "parquet"
      requires_region: true
      relative_path: "3_hastus/{version}/4_spatial_itinerary/{region}_{version}_GTFS_Itineraries.parquet.gz"
      schema_file_name: "hastus_spatial_itinerary_schema_{schema_version}.yaml"
      default_variables:
        region: "SEQ"
        version: "20230307"
        schema_version: "2024.0.0"

    - name: "stop_location"
      storage_type: "local_drive"
      file_type: "csv"
      requires_region: true
      relative_path: "3_hastus/{version}/3_stops/stop_location.csv"
      default_variables:
        region: "SEQ"
        version: "20230307"
        schema_version: "2024.0.0"


    - name: "gtfs_feed"
      requires_region: true # Will need a region when we include all the regions in the input folder.
      relative_path: "4_gtfs/2_cubic/{version}/{region}_GTFS.zip"
      default_variables:
        region: "SEQ"
        version: "20230307"



  outputs:
    - name: "stop_to_stop_measure"
      storage_type: "local_drive"
      file_type: "parquet"
      requires_region: true
      relative_path: "1_stop_to_stop_outputs/{version}/region={region}/Year={year}/Month={month}/Day={day}/stop_to_stop_measures.parquet.gz"
      is_partitioned: true
      schema_file_name: 'stop_to_stop_measures_schema_{schema_version}.yaml'
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'

    - name: "link_based_measures"
      storage_type: "local_drive"
      file_type: "parquet"
      relative_path: "2_travel_time_split/{version}/region={region}/Year={year}/Month={month}/Day={day}/link_based_measures.parquet.gz"
      requires_region: true
      schema_file_name: "link_based_measures_schema_{schema_version}.yaml"
      is_partitioned: true
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'

    - name: "appended_corridors"
      storage_type: "local_drive"
      file_type: "parquet"
      relative_path: "3_corridor_explorer/1_append_corridors/{version}/corridor_type={corridor_type}/region={region}/{corridor_id}_tt_estimated.parquet.gz"
      requires_region: true
      #      schema_file_name: "appended_corridors_schema_{schema_version}.yaml"
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'

    - name: "corridors_estimated_link_based_measures"
      storage_type: "local_drive"
      file_type: "parquet"
      relative_path: "3_corridor_explorer/2_measures/{version}/corridor_type={corridor_type}/region={region}/link_based_measures/{corridor_id}_link_based_estimates.parquet.gz"
      requires_region: true
      #      schema_file_name: "corridors_link_based_measures_schema_{schema_version}.yaml"
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'

    - name: "corridors_estimated_stop_based_measures"
      storage_type: "local_drive"
      file_type: "parquet"
      relative_path: "3_corridor_explorer/2_measures/{version}/corridor_type={corridor_type}/region={region}/stop_based_measures/{corridor_id}_stop_based_estimates.parquet.gz"
      requires_region: true
      #      schema_file_name: "corridors_estimated_stop_based_measures_schema_{schema_version}.yaml"
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'

    - name: "bcap_segments_measures"
      storage_type: "local_drive"
      file_type: "parquet"
      relative_path: "4_bcap/1_measures/{version}/region={region}/segments_measures.parquet.gz"
      requires_region: true
      #      schema_file_name: "bcap_segments_measures_schema_{schema_version}.yaml"
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'

    - name: "bcap_stops_measures"
      storage_type: "local_drive"
      file_type: "parquet"
      relative_path: "4_bcap/1_measures/{version}/region={region}/stops_measures.parquet.gz"
      requires_region: true
      #      schema_file_name: "bcap_segment_measures_schema_{schema_version}.yaml"
      default_variables:
        version: "20221014"
        region: 'SEQ'
        schema_version: '2024.0.0'


connections:
  - connection_name: "local_postgres"
    host: "localhost"
    port: 5432
    dbname: "sampledb"
    user: "user"
    password: "password"



```


## Calculations Configuration

### Link Measures - CE 

```yaml


operations:
  - type: calculation
    name: extracting hour
    formula: "hour = scheduled_start.dt.hour"
    
  - type: custom_transform
    name: dropping null estimated travel time
    formula: "df = df[df['link_actual_travel_time'].notnull()]"

  - type: custom_transform
    name: dropping any possible case of zero travel time
    formula: "df = df[df['link_actual_travel_time']>0]"

  - type: custom_transform
    name: creating service column
    formula: "df.loc[:, 'service'] = df['trip_id'] + '-' + df['scheduled_start'].astype(str)"

  - type: aggregation
    name: calculating free flow travel time
    group_by: [ "region",  "hour", "corridor_type",   "corridor_desc", "corridor_id","corridor_direction","corridor_section", "corridor_seg_order", "seg_id", "seg_direction" ]
    aggregations:
      free_flow_travel_time:
        column: link_actual_travel_time
        function: "quantile"
        params:
          q: 0.15
    transform: true

  - type: custom_transform
    name: calculating timetable delay per link
    formula: "calculate_timetable_delay(df)"

  - type: custom_transform
    name: calculating estimated delay per link
    formula: "calculate_link_estimated_delay(df)"

  - type: calculation
    name: estimating timetable delay per km
    formula: "timetable_delay_per_km = link_timetable_delay / seg_length"

  - type: calculation
    name: estimating timetable delay per km
    formula: "link_estimated_delay_per_km = link_estimated_delay / seg_length"

  - type: aggregation
    name: estimating aggregate measures
    group_by: [ "region", "hour", "corridor_type", "corridor_desc",  "corridor_id","corridor_direction", "corridor_section", "corridor_seg_order",  "seg_id", "seg_direction" ]
    aggregations:
      seg_length:
        column: "seg_length"
        function: "first"
      count_date:
        column: "date"
        function: "nunique"
      travel_time_link_avg_hourly:
        column: "link_actual_travel_time"
        function: "non_zero_mean"
      travel_time_link_Median_hourly:
        column: "link_actual_travel_time"
        function: "non_zero_median"
      StdDevNo0_estimated_travel_time:
        column: "link_actual_travel_time"
        function: "non_zero_std"
      CountNonNull_estimated_travel_time:
        column: "link_actual_travel_time"
        function: "non_null_count"
      count_services_link_hour:
        column: "service"
        function: "nunique"
      total_stop_load_per_link_hourly:
        column: "stop_load"
        function: "sum"
      std_stop_load_per_service_hourly:
        column: "stop_load"
        function: "non_zero_std"
      timetable_delay_per_km:
        column: "timetable_delay_per_km"
        function: "median"
      link_estimated_delay_per_km:
        column: "link_estimated_delay_per_km"
        function: "median"


  - type: aggregation
    name: estimating corridor length
    group_by: [ "region", "hour", "corridor_type", "corridor_id", "corridor_direction" ]
    aggregations:
      corridor_length:
        column: seg_length
        function: "sum"
    transform: true

  - type: aggregation
    name: estimating corridor length threshold
    group_by: [ "region", "hour", "corridor_type", "corridor_id",  "corridor_direction" ]
    aggregations:
      corridor_length_threshold:
        column: corridor_length
        function: "mode"
    transform: true

  - type: custom_transform
    name: assigning accept_hour
    formula: "assign_accept_hour(df)"

  - type: calculation
    name: estimating segment speed
    formula: "seg_speed = seg_length* 3.6/ travel_time_link_avg_hourly"

  - type: calculation
    name: estimating hourly travel time per km on links
    formula: "travel_time_per_km_hourly_link = travel_time_link_avg_hourly/ seg_length"

  - type: aggregation
    group_by: [ "region",  "hour", "corridor_type" , "corridor_id", "corridor_direction" ]
    name: estimating aggregate hourly average travel time on links
    aggregations:
      travel_time_corridor_avg_hourly:
        column: travel_time_link_avg_hourly
        function: "sum"
    transform: true

```
