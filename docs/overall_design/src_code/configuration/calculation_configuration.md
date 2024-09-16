---
layout: default
title: Calculation Configuration
parent: Configuration Settings
nav_order: 3
---

# Calculation Configuration
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## YAML Structure Overview

The YAML file defines a series of operations that are applied to the incoming DataFrame, specifically targeting key metrics required to be produced for either of the Corridor Explorer or BCAP dashboards. The file is composed of a list of operations, each specifying the type of operation (calculation, custom transformation, or aggregation) along with the necessary details like formulas and aggregation levels.

### Key Elements of the YAML

1. Operation Type (`type`): 

   Each operation is classified as one of the following:
      - **calculation**: A mathematical or logical operation that creates a new column or modifies an existing column. The formulas usually follow Pandas syntax and operate at the row level. In the backend these calculation is performed using pandas eval operator for increased efficiency, therefore this operation type is limited to what eval can do. For more custom operation use the custom transform.
      
      - **custom_transform**: A user-defined transformation, usually applied through custom functions where the pandas eval operation is not working. These transformations can filter rows, modify values, or introduce complex logic.
      
      - **aggregation**: An operation that groups data by one or more columns and applies aggregation functions (e.g., sum, mean, or custom functions) to calculate summary statistics. 
   
2. Operation Name (`name`):

   A human-readable name for each operation, describing its function.

3. Formula (`formula`):

   The formula or expression used for calculations and custom transformations. In some cases, the formula refers to a custom function.

4. Group By (`group_by`): 

   For aggregation operations, this specifies the columns used to group the data. The result is aggregated at the level of the specified columns.

5. Aggregation Details (`aggregations`): 

   For aggregation operations, defines the metrics calculated during aggregation, including:

   - **Column**: The column on which the aggregation function is applied.

   - **Function**: The aggregation function used (e.g., sum, mean, median).

   - **Params**: Any additional parameters passed to the aggregation function (optional).
   
   - **Transformation Flag (`transform`)**: For aggregations, this flag indicates whether the result of the aggregation is applied to each row within the group. If set to `true`, the aggregated value is repeated for each row in the group (this mimics how Pandas `transform()` works). If not present or set to `false`, the result is produced at the group level only.


 {: .important-title }
 >    Maintaining the Calculation Configuration YAML File
 > 
 >    Operations are executed sequentially, so the order in the YAML file matters. Make sure transformations happen before any aggregations that rely on transformed data.
 >    Use clear and descriptive names for each operation to make the YAML file easy to read and maintain.
 >    After adding new operations, it is important to test the changes by running the ETL pipeline and verifying that the operations work as expected.


