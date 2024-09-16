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
- `format`: (Optional) For date and date-time columns, this specifies the expected format.
- `required`: Whether the column is mandatory in the table.
- `correction`: (Optional) A transformation function written as a string, which will be applied to preprocess the column
  data upon reading.
- `definition`: (Optional) A brief description of the column's content.
- `note`: (Optional) Any additional explanatory notes for the column.

### Schema Type

The `schema_type` key indicates the type of data structure the schema is designed to validate. For our purposes, this is
typically set to `dataframe`.

Creating and Maintaining Schemas
-------------------------------

1. **Reusing Existing Schemas**: If a new table is similar to an existing one, consider using the existing schema as a
   starting point and then modify as needed.

2. **Testing the Schema**: After creating or modifying a schema, always test it with actual data to ensure it works as
   expected.

3. **Versioning**: Whenever making significant changes to a schema, update the version number and document the changes
   in the docstring of the yaml file. This ensures traceability and clarity about schema evolutions.

4. **Collaboration**: Use a version control system like Git to manage schema files.

5. **Corrections**: When defining corrections in the schema, ensure they are generic and applicable to all rows in the
   table. Test these corrections thoroughly using sample data.
