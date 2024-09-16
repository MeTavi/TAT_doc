---
layout: default
title: Configuration Settings
parent: Source Code
nav_order: 1
---

# Configuration Settings
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Introduction

Transit Analytics Tools are designed to work with configuration files at various levels. A specific Python process is designed to validate and parse these configuration settings. Below is a summary of the configuration settings used:

1. Main Configuration  
The Main Configuration is essential for establishing access to data at different stages of the process. It must be set up for the ETL process to function correctly. The configuration module is used to parse and validate the main configuration files.
2. Run Configuration  
The Run Configuration sets up the arguments needed to execute one or more steps of the ETL process. While this configuration is optional, it is highly recommended for maintaining transparency within the process. The run_config module is responsible to parse these configuration files.
3. Calculation Configurations
The Calculation Configurations serves as a recipe for the sequential operations applied to incoming data. These configuration require minimal user intervention unless there is a need to update the formulation, change the order of operations, or add new operations. These files are read by the transformer module within the transit_metrics_aggregate module where the data validation occurs as part of the process.

