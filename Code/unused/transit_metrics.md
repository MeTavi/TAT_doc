

## Estimating Transit Metrics  

   After the data is ingested by the data preparation stage above, a series of data cleaning and transformation operations are applied to estimate key transit metrics, such as travel time, load, boarding, and alighting. This phase is handled by the **Transit Metrics Module**, which first calculates the metrics at the stop-to-stop level and then maps them onto the corresponding road network. To generate these metrics, the Jupyter notebook `1_produce_curated_data.ipynb` is used. This notebook instantiates the **Transit Metrics** class and runs two primary methods:
   - `produce_stop_to_stop_measures()`
   - `produce_link_measures()`    

   The `produce_stop_to_stop_measures()` method calculates the transit metrics between stops. When this method is called, it saves its output (the stop-to-stop metrics) locally in the `4_outputs\1_stop_to_stop_outputs`, under a structured partition based on the analysis period, region, year, month, and day.   
   Once the stop-to-stop metrics are saved, the `produce_link_measures()` method can be called. This method reads the outputs saved by the `produce_stop_to_stop_measures()` method from the local drive. It uses these stop-to-stop metrics as input to calculate the link-based measures, mapping them onto the road network. The link-based measures are then saved in the `4_outputs\2_travel_time_split` folder, with a similar partitioning structure as the stop to stop outputs.
   Each method instantiates additional backend classes to handle specific data processing tasks. The **Ticketing Module** processes the ticketing data, while the **GTFS Module** generates the scheduled timetable data from the GTFS input. These two modules independently process their respective data, which are then combined in the `StopToStopMeasures` class within the **Transit Metrics Module**.
   Once combined, the stop-to-stop measures are integrated with the spatial itineraries using the **SegmentLink** class, also within the **Transit Metrics Module**.
   Below is the folder structure where the outputs of the transit metrics are saved:
       
   ```shell
   |   +-- 4_outputs
   |   |   +-- 1_stop_to_stop_output
   |   |   |   +-- [YYYMMDD]
   |   |   |   |   +-- region=SEQ
   |   |   |   |   |   +-- Year=[YYYY]
   |   |   |   |   |   |   +-- Month=[MM]
   |   |   |   |   |   |   |   +-- Day=[dd]
   |   |   |   |   |   |   |   |   +-- stop_to_stop_measures.parquet.gz
   |   |   +-- 2_travel_time_split
   |   |   |   +-- [YYYMMDD]
   |   |   |   |   +-- region=SEQ
   |   |   |   |   |   +-- Year=[YYYY]
   |   |   |   |   |   |   +-- Month=[MM]
   |   |   |   |   |   |   |   +-- Day=[dd]
   |   |   |   |   |   |   |   |   +-- link_based_measures.parquet.gz
  
   ```

