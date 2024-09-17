---
layout: default
title: Data Schema
parent: Setup Guide
nav_order: 2
---
# Data Schema
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Introduction

This section provides an overview of the YAML data schema files used to validate and preprocess data tables within the Transit Analytics Tools (specifically used by the DataReader module). 
Having these YAML schemas ensure data consistency, integrity, and correctness by specifying the expected structure, data
types, and other constraints for each table.

## Structure of the YAML Schema


### Docstring

At the top of the YAML file, there's an optional docstring section. This section provides verbose information about the
data table, its source, use case, or any other details that can be beneficial for users or developers. The docstring is
not used by the code but serves as a reference for anyone examining the schema.

### Metadata Section

The schema starts with an optional `metadata` section that provides additional details about the schema:

- `author`: The person who created or last modified the schema.
- `last_modified`: The date of the last modification.
- `notes`: Any relevant notes or comments about this version of the schema.

### Version

The schema has a `version` key to track its version. This version is used in the file names and is also used by the configuration module to facilitate accessing the data with various data structure in different years. 
The format of our version numbers follows the [Semantic Versioning](https://semver.org/) convention. That is a versioning scheme for software (and, by extension, any kind of digital product) that aims to
convey meaning about the underlying changes with each new release. Here's a breakdown of how it works:

Format:
The version number is typically in the format of YEAR.MAJOR.MINOR, e.g., 2024.1.3.

Components:

- YEAR version: This is referring to the year where the schema is initially introduced, and may not necessary align with the content of the data. 
- MAJOR version: This is incremented when there are incompatible changes that require the user to change something about
  their setup. In our context of the YAML schema, this could mean a complete redesign of the schema, removing essential
  columns, or any change that would break existing systems relying on the previous version. This might happen when new data for new analysis period is provided and the format and structure of the data has changed.
- MINOR version: This is incremented when new features are added in a backward-compatible manner. For our schema, this
  could mean adding new optional columns, introducing new data validation rules that still fit within the previously
  defined structure, adding annotations, or refining validation rules that were previously too strict or too lenient.. This is most likely to be changing over time.


### Columns

The `columns` section defines the specifications for each column in the table:

- `dtype`: The expected data type of the column. This can be `int`, `float`, `str`, `date`, `date_time`, etc.
- `format`: For date and date-time columns, this specifies the expected format.
- `required`: Whether the column is mandatory in the table.
- `correction`: A transformation function written as a string, which will be applied to preprocess the column
  data upon reading.
- `rename_to`: A string to rename the column to. 
- `new_column`: Only used in conjunction with correction. When it is provided, the result of correction is stored under a new column. This is used for raw transactions to extract the stop_id from the boarding and alighting stops. 
- `definition`: A brief description of the column's content.
- `note`:  Any additional explanatory notes for the column.


## Creating and Maintaining Schemas


1. **Reusing Existing Schemas**: If a new table is similar to an existing one, consider using the existing schema as a
   starting point and then modify as needed.  

2. **Using Pandera**: If a totally new table, read it in pandas dataframe, convert the datatype as required in pandas. Use the infer_schema method from the Pandera library to generate the schema based on your DataFrame. Save the inferred schema to a YAML file locally. Review the generated schema to ensure it is correct and meets your requirements.

3. **Corrections and Renames**: When defining a new schema, corrections and renames need to be added under appropriate columns manually. Ensure they are generic and applicable to all rows in the
   table. Test these corrections thoroughly using sample data.

4. **Testing the Schema**: After creating or modifying a schema, always test it with actual data to ensure it works as
   expected.

5. **Versioning**: Whenever making any changes to a schema, update the version number and document the changes
   in the docstring of the yaml file. This ensures traceability and clarity about schema evolutions.

6. **Collaboration**: Use a version control system like Git to manage schema files.


