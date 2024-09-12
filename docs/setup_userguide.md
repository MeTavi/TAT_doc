---
layout: default
title: Setup Guide
nav_order: 3
---

# Seting up the process
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
## Introduction

The overall ETL is composed of multiple stages where the raw data are prepared into inputs and then curated at multiple
stages before producing the outputs that are used for visualisation. This section of the document provides a
step-by-step guide to setting up and running the Transit Analytic ETL process. This guide is intended for new users
setting up this process on a local PC. For detailed
descriptions and additional documentation, please refer to the other relevant documents mentioned herein this document.

The user should follow the below steps where each is further described in this document:

1. Clone the repository.
2. Set up folder structure and place the raw data in the appropriate folders.
3. Set up the python environment and install the required libraries.
4. Check and confirm the configuration files as per the updated folder structure.


This document is not dealing with technical stages involved in data transformation, or the design of the ETL process,
instead it intends to
guide a
new user on how to set up the process for the first time before running the ETL tool for a new analysis period with new
data.

## Prerequisites

1. **Raw Data Files**: Raw data files are not stored within the repository. Ensure you have the access to the raw data
   files required for processing.
2. **Folder Structure**: A template of empty folders in the required structure is created when cloning the repository,
   however, the user should set it up (rename the folders based on the period being analysed). A more description can be
   found in the proceeding section titled "Folder Structure".
3. **Git account**: Access to TMR-TAU Git account.
