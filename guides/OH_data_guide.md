---
title: Data Guide for Olin's HAFTRAP Deployments (OH)
---

This document describes the organization of data, code files, and outputs in the Summer 2023 phase of analyzing Olin's HAFTRAP deployments. **If you haven't already, read the [main data guide](../data_guide.html) to understand the overall pipeline structure** (this link may only work if opened in browser).

The reliability and continued maintenance of this document cannot be guaranteed after August 4, 2023. The information in this document assumes reasonable familiarity with this project. For further questions regarding this document, feel free to contact [Vedaant Kuchhal](mailto:vedaantk22@gmail.com).


## Overview
The Olin HAFTRAP deployments are part of the HAFTRAP study focusing on participants living along the highway I-93 in Somerville. The study is conducted in a clinical trial format, which means the following things for the air quality data:

* The participants are coded by anonymized participant IDs
* The air purifier was deployed in two one-week segments. In one week, the participants got a fake purifier that did not do anything (`sham`), whereas the other, they got a real, working HEPA air purifier (`hepa`).
* For each participant, there was one set of sensors sampling indoors, and an equivalent set sampling outdoors.
* For Olin HAFTRAP in particular, two types of sensors were deployed: low-cost Modulair- PM (Mod-PM) sensors measuring particles between 0.35-10 microns, and more expensive Condensation Particle Counter (CPC) sensors measuring ultrafine particles (UFPs) less than 0.35 microns in diameter.


*All* files that are associated with Olin HAFTRAP should have `OH_` prefixed to their name.

## Pre-requisites
To run the code in `cleaning`, `analysis`, and `insight`, two things are required:

1. Install R and RStudio
2. Set up Github and clone [this project's repository](https://github.com/airpartners/hepa-summer23/)

Optionally, it will make life vastly easier for you if you can get [Github working through RStudio](https://happygitwithr.com/rstudio-git-github.html). Any other requirements (especially additional packages to install) will be described in the relevant part of the pipeline.

## Data Pipeline
Unlike the main data guide, this description of the pipeline focuses on *understanding the files and getting the code to run*. Before we dive in, look at the following figure that outlines all the files in the entire pipeline:

<img src="../.img/.OH_files.jpg"/>

Click [here]() to see an isolated version you can zoom into.
## Raw Data (`data`)
**This component has by far the most files, and it is the largest and most important of the six components to read to understand how files are organized and new files can be added.**

Raw data is the original, ‘on the ground’ data collected by multiple kinds of sensors (or in case of field notes, people!) 

All files in this component are stored in the `data` folder. Crucially, *this folder is not tracked by Git*. Since committing multiple large data files would greatly slow down Git and is generally considered bad practice, this folder is instead hosted on Google Drive for Air Partners 2023. 

To get or update to the latest version of the data:

1. **Go to the provided [Data for Codebase](https://drive.google.com/drive/folders/1J6w_h6FFlxgXWv3k7CkSchYMkhryRF8n) Google Drive folder.**
2. Download and extract the `data` folder inside it. Deal with zipped folder(s) appropriately.
3. Move the extracted `data` folder to the root of your `hepa-summer23` respository clone, i.e. the same location as this data guide. 
4. Confirm that the folder has the expected four subfolders and is named `data`. 

The folder should be untracked automatically by the `.gitignore` so replacing it locally shouldn't make a difference on your git log.

**To add/delete/reorganize files in this folder, always do it in the Google Drive and download to PC. Do *not* modify your local copy of this folder. It will not be reflected anywhere else and result in out-of-sync versions.**


### Subfolders
Each location folder is further subdivided into folders by sensor type. The folders are named by a sensible lowercase abbreviation of the sensor type (e.g.- `modpm`, `hobo`, `cpc`). The only exception for this is [field notes](#field-notes), where the file is directly placed in relevant the location folder.

### File Naming
Data files are labelled with the following naming convention:
`LocationCode_SensorCode_ID_suffix`

You should already have looked at the [standard location codes](#location-coding). Now, let's familiarize ourselves with the sensor codes.

#### Sensor Codes
The sensors deployed in various phases of this project are each coded by a single uppercase letter:

<img src="../.img/.sensor_codes.png" width="400" />

In case the data is merged between different sensors, all relevant sensor codes are used (e.g.- `MC` for a file that contains Mod-PM + CPC data).

#### Field Notes
In addition to the sensor data, field notes documented by people in charge of sensor installation and air purifier deployment provide critical contextual information, such as the date the various sensors were active for and the date the air purifier was installed. Field notes should be a single file per location, coded as `LocationCode_notes`. Some might have an additional 'reformatted' version that is a distilled, code-parseable format code in `analysis` to filter data.

#### Sample Explanations

* `OH_M_412711_sham_indoor.csv`: data from the Modulair-PM sensor for participant 412711 from Olin's deployments in the HAFTRAP study. The data is from a sensor installed indoors when the participant had a sham air purifier placed in their room. Found in `data/HAFTRAP/OH/modpm/`

* `CM_M_104_before_indoor.csv`: data from the Modulair-PM sensor installed indoors in apartment 104 in Cardinal Medeiros. This is before the air purifier was switched on in that home. Found in `data/Roxbury/CM/modpm/`

* `MF_notes`: field notes from the collaboration with Mother's Out Front group in East Boston. Found in `data/EastBoston/MF/`

## Cleaning (`cleaning`)
This component is pretty self-explanatory. Implementations vary, but the overall aims are the same:

1. Ingest raw data files
2. Select and/or compute the relevant columns
3. Filter out incorrect readings
4. Deal with date-times (trust me this is big)
5. Add grouping variables (e.g. - before vs. after) based on time
6. Calculate indoor/outdoor ratios if relevant

The output should be a set of cleaned, usually [tidy](https://tidyr.tidyverse.org/articles/tidy-data.html) dataframes that contains all the variables required for further analysis. These aren't saved as CSV files but as `.RData` dataframes **untracked by git** (again, since the file sizes are large). Typically, there should be one dataframe containing data for all participants for one sensor type (e.g.- Mod-PM, or CPC).


## Initial Analysis (`analysis`)
The analysis component can contain many code files, but it uses the cleaned data to do essentially two broad things:

1. Calculate summary statistics of the data to use for further analysis
2. Plot various graphs of the data to analyse patterns and derive findings

Summary statistics include values such as the mean, median, and 5th, 25th, 75th, and 95th percentiles. Additionally, percentage reductions in these values (from with the HEPA purifier to without) are also calculated. Aside from this, correlation coefficients between variables may also be computed to determine agreement between different sensor measurements. All calculations are done by participant as well as over all the data. Output from this section is stored in `summary` (described soon).

The plotting part of the pipeline can include many kinds of plots depending on the project's analysis needs. These could range from boxplots to diurnal plots, and much more. Importantly, plots generated in this component are saved into the corresponding folder in the `artifacts` component.

Each location has unique data processing needs, and therefore,a unique set of analysis files. As with all other components, the analysis files are described separately for each location (you may start to notice some common patterns between locations).

## Summary Data (`summary`)
Summary data is the output of the code in `analysis`. It contains summary statistics, any other overall values relevant to the given analysis (e.g.- correlation coefficients).

Summary data follows the [same naming convention as Raw Data](#file-naming)!

There are two key differences with Summary Data compared to Raw Data:

1) The `summary` folder *is* tracked by Git. This is because the summary data files are much tinier than the raw data they are produced from. (E.g.- OH raw data is ~2.8 GB, OH summary data is ~580 KB)

2) While the files follow the same file naming structure, they are prepended with the prefix `s_` to distinguish them as summary data files. Additionally, since the data is summarized (no longer separate files for indoor/outdoor, for example, a different set of suffixes are used. Once again, specifics depend on the location, but here are some examples:

* `quants` contains summary statistics such as mean, median, 5th, 25th, 75th, and 95th percentile.
* `corr` contains the correlation matrix for the dataset analyzed

Additionally, summary data may also contain temporary `.RData` dataframes *untracked by Git* that are used in supporting final calculations in `insight`. Once again, these too depend on the analysis needs of the specific location.

## Final Insights (`insight`)
By this component, the data has been analysed and summarized to a point where only 1-2 files are needed to deep dive into collecting and confirming any insights that may have arisen from the output of `analysis`. The code in this component will used the data from `summary` to generate some final graphs and/or tables which succinctly show the key insights from this dataset. Importantly, the output from this section will also be stored in `artifacts`.

## Artifacts (`artifacts`)
This folder is essentially a dump of the outputs from the `analysis` and `insight` components and completely dependens on the location, but there are two things to keep in mind:

1. The entire folder is *untracked by git*. There is no copy on an external drive, so to get the code output artifacts you'll need to, well, run the code.
2. The folder will be organized into reasonably named subfolders (e.g. - the diurnal plots go into a folder named `diurnals`). The assumption is that by the time you are dealing with this folder, you already have a good enough understanding of the code and its outputs.



#### The CPC Exception - TODO Move into OH_data_guide.
There is one exception to the [data pipeline](#data-pipeline) and therefore the folder structure. You will see 'raw data' CSV files in the `cpc` folders, but in reality these files have been pre-processed from actual sensor output.

A CPC sensor outputs a new text file of data for *every day* it runs. These raw text files are stored in a subfolder of `cpc` (elegantly named `very_raw_data`). A pre-processing script found in `analysis` - location described in [analysis](#initial-analysis) - parses through the text files and outputs one CSV per participant, per environment (indoor/outdoor) which contains the **relevant variables** from the raw data. No other cleaning/filtering is done in pre-processing.

For all intents and purposes, only the correctly named CSV files found directly in the `cpc` folder will be used for all analysis. The true sensor output text files are only present for debugging and completeness in documentation.

All the other components are much simpler and therefore have much shorter explanations!

### OH (Olin's deployments for HAFTRAP) - TODO Move to OH_data_guide
* `initial_data_analysis.Rmd`: Is a starter file to quickly build up working filter, cleaning, and analysis code. Kind of like a play testing war zone, ignore this file if you're trying to comprehend clean, logical code.

HAFTRAP:

* `haftrap_OH_single_summary.Rmd`: The DR1 stands for "Design Review One". It initially contained scrappily-written code for a design review with Doug Brugge, but now should have a clean pipeline to compute summary statistics from one participant's deployment with sham and true sensors.
