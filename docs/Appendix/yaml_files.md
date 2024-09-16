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

### Link Measures - Corridor Explorer

```yaml
# This YAML configuration file is used to define operations for calculating aggregate measures from
# the link-based measures dataframe that are already appended to the corridor definitions.


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

  - type: calculation
    name: estimating segment speed (km/hr)
    formula: "actual_speed = seg_length* 3.6/ link_actual_travel_time"
    
  - type: custom_transform
    name: remove outlier speed
    formula: "df = df[(df['actual_speed']<100) & (df['actual_speed']>=5)]"


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
    name: calculating timetable delay per link (in seconds)
    formula: "calculate_timetable_delay(df)"

  - type: custom_transform
    name: calculating estimated delay per link (in seconds)
    formula: "calculate_link_estimated_delay(df)"

  - type: calculation
    name: timetable delay per km (min per km)
    formula: "timetable_delay_per_km = (link_timetable_delay/60) / (seg_length/1000)"

  - type: calculation
    name: estimated delay per km (min/km)
    formula: "link_estimated_delay_per_km = (link_estimated_delay/60) / (seg_length/1000)"

  - type: aggregation
    name: estimating aggregate measures
    group_by: [ "region", "hour", "corridor_type", "corridor_desc",  "corridor_id","corridor_direction",
                "corridor_section", "corridor_seg_order",  "seg_id", "seg_direction" ]
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
      total_stop_load_per_link_hourly:
        column: "stop_load"
        function: "sum"
      count_services_link_hour:
        column: "service"
        function: "nunique"
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

  - type: calculation
    name: convert corridor length to km
    formula: "corridor_length = corridor_length/1000"

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
    name: estimating segment speed (km/hr)
    formula: "seg_speed = seg_length* 3.6/ travel_time_link_avg_hourly"

  - type: calculation
    name: estimating hourly travel time per km on links (minutes/km)
    formula: "travel_time_per_km_hourly_link = (travel_time_link_avg_hourly/60)/ (seg_length/1000)"

  - type: aggregation
    group_by: [ "region",  "hour", "corridor_type" , "corridor_id", "corridor_direction" ]
    name: estimating aggregate hourly average travel time on corridors
    aggregations:
      travel_time_corridor_avg_hourly:
        column: travel_time_link_avg_hourly
        function: "sum"
    transform: true

  - type: calculation
    name: converting the travel_time_corridor_avg_hourly to minutes
    formula: "travel_time_corridor_avg_hourly = travel_time_corridor_avg_hourly/60"

  - type: calculation
    name: converting the travel_time_link_avg_hourly to minutes
    formula: "travel_time_link_avg_hourly = travel_time_link_avg_hourly/60"

  - type: calculation
    name: converting the travel_time_link_Median_hourly to minutes
    formula: "travel_time_link_Median_hourly = travel_time_link_Median_hourly/60"

  - type: calculation
    name: converting the StdDevNo0_estimated_travel_time to minutes
    formula: "StdDevNo0_estimated_travel_time = StdDevNo0_estimated_travel_time/60"

```


### Stop Measures - Corridor Explorer

```yaml
# This YAML configuration file is used to define operations for calculating aggregate stop based measures from
# the link-based measures dataframe that are already appended to the corridor definitions.


operations:

  - type: custom_transform
    name: filtering to stops
    formula: "df = df.loc[df['is_stop'] == 1]"

  - type: calculation
    name: extracting hour
    formula: "hour = scheduled_start.dt.hour"

  - type: custom_transform
    name: creating service column
    formula: "df.loc[:, 'service'] = df['trip_id'] + '-' + df['scheduled_start'].astype(str)"


  - type: aggregation
    name: estimating aggregate measures
    group_by: [ "region", "date", "hour", "corridor_id", "corridor_desc", "corridor_direction", "corridor_type", "segment_order", "is_stop","stop_id" ]
    aggregations:
      seg_length:
        column: 'seg_length'
        function: 'first'
      count_services_hourly_per_date:
        column: "service"
        function: "non_null_count_distinct"
      estimated_boarding_stop:
        column: "boarding"
        function: "sum"
      estimated_alighting_stop:
        column: "alighting"
        function: "sum"
      estimated_load:
        column: "stop_load"
        function: "sum"
      count:
        column: 'stop_load'
        function: "size"

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

```



### Link Measures - BCAP

```yaml
# This YAML configuration file is used to define operations for calculating aggregate measures for BCAP from
# the link-based measures dataframe that are already appended to the corridor definitions.

operations:
  - type: calculation
    name: extracting hour
    formula: "start_hour = scheduled_start.dt.hour"

  - type: custom_transform
    name: data cleaning  - dropping null estimated travel time
    formula: "df = df[df['link_actual_travel_time'].notnull()]"

  - type: custom_transform
    name: data cleaning  - dropping any possible case of zero travel time
    formula: "df = df[df['link_actual_travel_time']>0]"

  - type: custom_transform
    name: data cleaning  - dropping any possible duplications
    formula: "df = df.drop_duplicates()"

  - type: calculation
    name: estimating segment speed (km/hr)
    formula: "actual_speed = seg_length* 3.6/ link_actual_travel_time"

  - type: custom_transform
    name: remove outlier speed
    formula: "df = df[(df['actual_speed']<100) & (df['actual_speed']>=0)]"

  - type: custom_transform
    name: assign time period
    formula: "assign_time_period(df)"

  - type: aggregation
    group_by: [ "seg_id", "seg_direction" ]
    aggregations:
      q1:
        column: "link_actual_travel_time"
        function: "quantile"
        params:
          q: 0.25
    transform: true

  - type: aggregation
    group_by: [ "seg_id", "seg_direction" ]
    aggregations:
      q3:
        column: "link_actual_travel_time"
        function: "quantile"
        params:
          q: 0.75
    transform: true

  - type: calculation
    name: calculate_upper_outlier_bound
    formula: "upper_outlier = 1.5 * (q3 - q1) + q3"

  - type: calculation
    name: calculate_lower_outlier_bound
    formula: "lower_outlier = q1 - 1.5 * (q3 - q1)"


  - type: custom_transform
    name: assign_outlier_flag
    formula: "assign_outlier_flag(df)"

  - type: custom_transform
    name: remove outlier travel time
    formula: "df = df[df['outlier_flag']==0]"

  - type: calculation
    name: estimating actual travel time in minutes per km
    formula: "actual_travel_time_per_km = link_actual_travel_time/ (seg_length*0.06)"

  - type: aggregation
    group_by: [ 'seg_id','seg_direction' ]
    name: estimating 95 percentile of actual speed
    aggregations:
      actual_speed_q95_all:
        column: actual_speed
        function: "quantile"
        params:
          q: 0.95
    transform: true

  - type: aggregation
    group_by: [ 'seg_id','seg_direction' ]
    name: concatenating list of route-short-names
    aggregations:
      route_short_name:
        column: route_short_name
        function: "list_of_unique_strings"
    transform: true

  - type: aggregation
    name: getting unique date
    group_by: [ 'region' ]
    aggregations:
      date_count:
        column: 'date'
        function: 'non_null_count_distinct'
    transform: true

  - type: aggregation
    name: getting count of segments with speed below 20 km/hr
    group_by: [ 'seg_id','seg_direction' ]
    aggregations:
      actual_speed_below20:
        column: "actual_speed"
        function: "count_below_threshold"
        params:
          threshold: 20
    transform: true


  - type: aggregation
    name: getting count of segments with speed below 20 km/hr
    group_by: [ 'seg_id','seg_direction' ]
    aggregations:
      actual_speed_above0:
        column: "actual_speed"
        function: "count_above_threshold"
        params:
          threshold: 0
    transform: true

  - type: calculation
    name: calculate_Total_speed_below_20
    formula: "Total_speed_below_20 = 100*actual_speed_below20/actual_speed_above0"

  - type: aggregation
    name: estimating aggregate measures
    group_by: [ 'region', 'seg_id','seg_direction','time_period' ]
    aggregations:
      seg_length:
        column: "seg_length"
        function: "first"
      date_count:
        column: "date_count"
        function: "first"
      Total_speed_below_20:
        column: "Total_speed_below_20"
        function: "first"
      actual_travel_time_min:
        column: "link_actual_travel_time"
        function: "min"
      actual_travel_time_max:
        column: "link_actual_travel_time"
        function: "max"
      actual_travel_time_mean:
        column: "link_actual_travel_time"
        function: "mean"
      actual_travel_time_count:
        column: "link_actual_travel_time"
        function: "count"
      actual_travel_time_median:
        column: "link_actual_travel_time"
        function: "median"
      actual_travel_time_std:
        column: "link_actual_travel_time"
        function: "non_zero_std"
      actual_travel_time_q5:
        column: "link_actual_travel_time"
        function: "quantile"
        params:
          q: 0.5
      actual_travel_time_q95:
        column: "link_actual_travel_time"
        function: "quantile"
        params:
          q: 0.95
      actual_travel_time_per_km_mean:
        column: "actual_travel_time_per_km"
        function: "mean"
      actual_travel_time_per_km_std:
        column: "actual_travel_time_per_km"
        function: "std"
      actual_speed_min:
        column: "actual_speed"
        function: "min"
      actual_speed_max:
        column: "actual_speed"
        function: "max"
      actual_speed_mean:
        column: "actual_speed"
        function: "mean"
      actual_speed_median:
        column: "actual_speed"
        function: "median"
      actual_speed_std:
        column: "actual_speed"
        function: "non_zero_std"
      actual_speed_q5:
        column: "actual_speed"
        function: "quantile"
        params:
          q: 0.5
      actual_speed_q95:
        column: "actual_speed"
        function: "quantile"
        params:
          q: 0.95
      actual_speed_below15:
        column: "actual_speed"
        function: "count_below_threshold"
        params:
          threshold: 15
      actual_speed_below20:
        column: "actual_speed"
        function: "count_below_threshold"
        params:
          threshold: 20
      departing_passengers_sum:
        column: "stop_load"
        function: "sum"
      route_short_name:
        column: "route_short_name"
        function: 'first'
      actual_speed_q95_all:
        column: "actual_speed_q95_all"
        function: 'first'

  - type: calculation
    name: SpeedBelow20
    formula: "SpeedBelow20 = 100* actual_speed_below20/ actual_travel_time_count"

  - type: calculation
    name: JTVariability # does not matter what is the unit of travel time per km. It can be second or hour
    formula: "JTVariability = actual_travel_time_per_km_std/ actual_travel_time_per_km_mean"


  - type: calculation
    name: CongestionIndex
    formula: "CongestionIndex = actual_speed_q95_all/ actual_speed_mean"

  - type: custom_transform
    name: ExcessPassDelay,
    formula: "df['ExcessPassDelay'] = (((df['actual_travel_time_q95'] - df['actual_travel_time_mean'])/60)* (df['departing_passengers_sum'] / df['date_count']))/(df['seg_length']/1000)"

  - type: custom_transform
    name: set negative ExcessPassDelay to zero,
    formula: "df.loc[df['ExcessPassDelay']<0,['ExcessPassDelay'] ] =0"

  - type: calculation
    name: AvePassDelay # this is actually the AveVehicleDelay min per km
    formula: "AvePassDelay = ((actual_travel_time_mean - actual_travel_time_q5)/60)/ (seg_length/1000)"

  - type: custom_transform
    name: set negative AvePassDelay to zero,
    formula: "df.loc[df['AvePassDelay']<0,['AvePassDelay'] ] =0"

  - type: custom_transform
    name: TotalPassDelay,
    formula: "df['TotalPassDelay'] = df['AvePassDelay']* (df['departing_passengers_sum'] / df['date_count'])"
  #    formula: "df['TotalPassDelay'] = (((df['actual_travel_time_mean'] - df['actual_travel_time_q5'])/60)* (df['departing_passengers_sum'] / df['date_count']))/(df['seg_length']/1000)"

  - type: custom_transform
    name: set negative TotalPassDelay to zero,
    formula: "df.loc[df['TotalPassDelay']<0,['TotalPassDelay'] ] =0"

  - type: custom_transform
    name: data cleaning  - dropping empty records
    formula: "df = df[df['actual_travel_time_count']>0]"

  - type: custom_transform
    name: reformatting the outputs
    formula: "df = (df.set_index(['seg_id','seg_direction','seg_length','route_short_name','Total_speed_below_20','time_period']).unstack().reset_index())"
  #
  - type: custom_transform
    name: renaming columns
    formula: "df.columns = ['_'.join(col).rstrip('-') for col in df.columns ]"

  - type: custom_transform
    name: renaming columns
    formula: "df.columns = [col.rstrip('_') for col in df.columns]"

  - type: custom_transform
    name: getting the date count
    formula: "df['date_count'] = df[['date_count_am','date_count_op','date_count_pm']].max(axis=1, skipna=True)"

  - type: custom_transform
    name: fill_na_required fields
    formula: "df[[f'{c}_{tp}' for c in ['actual_travel_time_count','departing_passengers_sum' ,'ExcessPassDelay', 'TotalPassDelay', 'actual_speed_below20'] for tp in ['am', 'op', 'pm']]] = df[[f'{c}_{tp}' for c in ['actual_travel_time_count', 'departing_passengers_sum' ,'ExcessPassDelay', 'TotalPassDelay', 'actual_speed_below20'] for tp in ['am', 'op', 'pm']]].fillna(0)"

  - type: custom_transform
    name: convert data types of departing_passengers_sum to int
    formula: "df[[f'departing_passengers_sum_{tp}' for tp in ['am', 'op', 'pm']]] = df[[f'departing_passengers_sum_{tp}' for tp in ['am', 'op', 'pm']]].astype('int')"

  - type: calculation
    name: Bus_count_total_daily
    formula: "Bus_count_total_daily = (actual_travel_time_count_am + actual_travel_time_count_op + actual_travel_time_count_pm) / date_count"

  - type: calculation
    name: Passengers_total_daily
    formula: "Passengers_total_daily = (departing_passengers_sum_am + departing_passengers_sum_op + departing_passengers_sum_pm) / date_count"

  - type: calculation
    name: Excess_passenger_delay_daily
    formula: "Excess_passenger_delay_daily = (ExcessPassDelay_am + ExcessPassDelay_op + ExcessPassDelay_pm)"

  - type: calculation
    name: Total_passenger_delay_daily
    formula: "Total_passenger_delay_daily = (TotalPassDelay_am + TotalPassDelay_op + TotalPassDelay_pm)"

  - type: custom_transform
    name: create unique seg identifier
    formula: "df['seg_unique'] = df['seg_id'] +'-' + df['seg_direction'] "

  - type: custom_transform
    name: assign JTV_flag
    formula: "assign_flag(df, 'JTVariability')"

  - type: custom_transform
    name: assign CI_flag
    formula: "assign_flag(df, 'CongestionIndex')"

  - type: custom_transform
    name: assign SpeedBelow20 flag
    formula: "assign_flag(df, 'SpeedBelow20')"

  - type: custom_transform
    name: assign ExcessPassDelay flag
    formula: "assign_flag(df, 'ExcessPassDelay')"

  - type: custom_transform
    name: assign TotalPassDelay flag
    formula: "assign_flag(df, 'TotalPassDelay')"

  - type: custom_transform
    name: assign AvePassDelay flag
    formula: "assign_flag(df, 'AvePassDelay')"
```