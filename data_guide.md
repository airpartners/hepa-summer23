# Data Guide

This document describes the organization of data, code files, and outputs in the Summer 2023 phase of the Air Partners HEPA air purifier pilot. The reliability and continued maintenance of this document cannot be guaranteed after August 4, 2023. The information in this document assumes reasonable familiarity with this project. For further questions regarding this document, feel free to contact [Vedaant Kuchhal](mailto:vedaantk22@gmail.com).

---

## Location Coding
At its very core, the location where data is collected central to categorizing the data, analyzing it, and interpreting insights. For this reason, all files will be coded by a standard set of location codes. The following codes will be used to organize folders, name data files, and refer to locations throughout this repository:

1. CM – Cardinal Medeiros, an elderly care [? Check with team] building in Roxbury.
2. IB – Indigo Block, an affordable housing [? Check with team] building in Roxbury.
3. RH – Individual homes in Roxbury.
4. DC – Daycares in East Boston
5. PS – Pre-schools in East Boston
6. CH – City Hall of Revere
7. HS – High school in Revere
[HAFTRAP later]

## Data Pipeline
The data pipeline for this project can be considered to be made up of four components [maybe redo this as a simple figure later]:

Raw data --> Data Processing --> Summative Figures and Reports --> Final Insight Generation

Each of these components has multiple file types and layers of complexity, and the structure of these is dictated in detail in this document. It’s important to note that, to maintain code modularity, the actual nature of the project and analysis won’t be apparent until the description of component 4. The explanation in each section in this guide assumes knowledge from all preceding sections.

Each component will have its own folder with uniquely named files. It's important to note that these folder may be nested [include final diagram of final structure of four folders once finalized].

Let’s walk through each of the components:

## Raw Data
Raw data is the original, ‘on the ground’ data collected by multiple kinds of sensors as well as people. All files in this component is stored in the `data` folder. This component of the pipeline contains the most files and therefore requires a clear naming system and hierarchical organization structure for best file management. As a result, this component is outlined in two parts:

1. File Categorization and Naming
2. Folder Organization Structure. 

### File Categorization and Naming
#### Sensors
The sensors deployed in various phases of this project are each coded by a single uppercase letter, and they are the following:

1. M – Modulair-PM sensors are the most common air quality monitoring device in this project. They measure concentrations of PM 1, PM 2.5, and PM 10. Installed both indoors and outdoors in Roxbury, East Boston (daycares and preschool), and Revere (high school and city hall) [check with Scott + Francesca].
2. H – HOBO sensors are connected to the air purifiers and detect power usage. They enhance Modulair-PM data since they provide information about when the air purifier was switched on and its fan speed. Installed only in East Boston daycares [is this true?].
3. C – CPC sensors are very important since they detect counts of ultrafine particles (UFP). They supplement data from Modulair-PMs, which cannot detect UFPs. Installed in East Boston preschools, Revere (high school and city hall).
4. A – Modulair [to do later]
[Etc. etc.]

#### Field Notes
N – In addition to the sensor data, field notes documented by people in charge of sensor installation and air purifier deployment provide critical contextual information, such as the date the various sensors were active for and the date the air purifier was installed. Field notes should be a single file per location. [is this true?]

#### Sensor ID 
Each sensor has a unique ID number assigned to it on deployment. The numbers do have some significance, but for now this is not necessary to understand for data organization.

#### Naming Convention
Therefore, raw data files are labelled with the following naming convention:
`LocationCode_SensorCode_ID`

For example, the csv data file for a Modulair-PM sensor with ID number 221 installed in Cardinal Medeiros will have the file name `CM_M_221.csv`.

### Folder Organization Structure
As a reminder, all raw data files are stored in a folder named `data`. Here’s a summary of the organization:

* Each subfolder is organized by location, named after the location code specified in this data guide.
*	Each location folder is further subdivided into folders by sensor type. The folders are named by a sensible lowercase abbreviation of the sensor type (e.g.- `modpm`, `hobo`, `cpc`). The only exception for this is field notes, where there is usually one file for each location and therefore the file will directly be placed in the location folder.
* Therefore, for example, the data for Modulair-PM sensor number 221 for Cardinal Medeiros can be found in `data --> CM --> modpm --> CM_M_221.csv` and its relevant field notes can be found in `data --> CM --> CM_N.csv`
*	(Note that some Modulair-PM sensors will be installed indoors while others are outdoors. Knowing which is installed indoor vs. outdoor is crucial for data analysis, so the specification of which sensor is installed indoor vs. outdoor can be found in the corresponding field notes for that location.)

## Data Processing
This component of the pipeline concerns code files that are used to clean, filter, and summarize the raw data. They are usually in the form of various scripts in R markdown that handle multiple, complex types of raw data. Here is a description of each code file:

* `initial_data_analysis.Rmd`: Is a starter file to quickly build up working filter, cleaning, and analysis code comparing a single indoor and outdoor sensor in CM.

