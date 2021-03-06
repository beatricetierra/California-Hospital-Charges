# CA-Hospital-Charges

## Background

Medical debt is now the number one source of debt collections in the United States, affecting 19% of US households and totaling $140 billion in unpaid medical bills in 2020 alone. The $140 billion only accounts for debts that have been sold to collectors, so the money owed to healthcare providers through installments or credit cards raises the national debt even further. 

The biggest reason for this accumulation of debt is the fact that patient have no idea how much they will be charged for healthcare until AFTER their service. Ontop of that, hospitals and clinics are able to charge whatever price on their services and are not required to share that information with their patients. This inhibits patients from shopping around their options and finding affordable procedures. 

As a partial solution, all US hospitals are now required to release pricing data as chargemaster forms as of January 1, 2021 under Centers for Medicare & Medicaid (CMS) law. Although this a great step forward for patient transparency, there are still obstacles getting in the way of public's ability to access basic healthcare prices. Some of these issues include:

   **1. Inconsistent format of chargemasters**

   CMS does not provide a template for hospitals to fill out their chargemasters. As a result, many chargemasters are sloppy and unreadable. 
   
   **2. Chargemasters are not posted easily on hospital website**

   Some hospital do post their chargemasters, but as attached excel or csv files. More frequently, however, hospitals hide their prices in compressed files that patient have to    download and manually find their procedure costs. 
   
   **3. Not all hospitals have submitted their chargemasters and/ or submit chargemasters that do not comply with CMS guidelines**

   As of now, California is the only state that requires hospitals to provide chargemasters to the state and Maryland is the only state to regulate the chargemaster procedures (check validity of listed prices). As a result, many hospitals have not submited chargemasters or do not submit sufficient data reports. 
   
## Intro

To demonstrate how chargemasters could actually be effective and transparent to patients, this project builds a data warehouse containing relevant information and factors that affect a patient's cost for a procedure in California. The key information this data warehouse provides are:

1. Chargemaster data (in a standard form)
2. Hospital profiles (location, license #, etc.)
3. Current Procedural Terminology (CPT) description (Codes hospitals use to bill patients and insurances)
4. Accepted insurances per hospital
5. Cost of in-network vs out-of-pocket expenses 

**Note:** Data is only relevant to California hospitals and insurances because it was the only chargemaster data available from a state government-run website. 

## Description of Raw Datasets 
Most datasets were acquired by downloading from the provided links. If scripts were used acquire raw data, they will be listed under the corresponding description and the scripts can be found in the **Data Extraction** folder of this repository. 

1. Chargemasters Dataset
   
   - Downloaded from https://data.chhs.ca.gov/dataset/chargemasters.
   - Contains all CA hospital chargemasters from 2011-2020.
   - Used to acquire costs of top 25 common procedures of each hospital. The Top 25 Common Procedures was the only standard form submitted by all hospitals. 

2. Hospital Profiles Dataset

   - Download from https://data.chhs.ca.gov/dataset/facility-profile-attributes. 
   - Used to compare nearby hospital costs.
   
3. Insurance Dataset

   - Download from http://www.insurance.ca.gov/01-consumers/110-health/20-look/hcpcarriers.cfm. 
   - Lists all the valid insurances accepted in California.
   - Used to find accepted insurances per hospital in the final dataset.

4. CPT Dataset

   - Download from https://www.cms.gov/Medicare/Medicare-Fee-for-Service-Payment/PhysicianFeeSched/PFS-Relative-Value-Files
   - Used to find procedure codes based on description easier. 
   - Chargemasters usually list CPT code NOT description, so patient won't be able to find the cost of a particular operation. 

5. Patient Expenses Dataset

   - Script: **Data Extraction/ GetInsuranceCost.py**
   - Scraped from https://www.fairhealthconsumer.org/. 
   - Find average cost of each procedure per zip code based on insurance (in-network vs out-of-network)
   - **Note:** Limited access to website tool, so only some hospitals/ cpt codes are listed. 

## Insurance Dataset Transformation

The collected insurance information only contained insurances operating in California. The goal was to find the accepted insurances per hospital. To do this, Selenium was used to do the following procedure by running the **Data Extraction/ GetHospitalInsurances.py** script:

   1. Google "<hospital name> accepted insurances". Ex. "Kaiser Permanente accepted insurances"
   2. Click first result
   3. Loop through list of insurances and check if name was found on page.
   4. If insurance name was found on page, then dataset would list it as an accepted insurance. 

## Azure Pipeline Input Data
   
The final datasets used in the Azure Pipeline are contained in the **Datasets** folder of this repository. An ERD to show the relationships between all the datasets is provided below as well.
   
![alt text](https://github.com/beatricetierra/US-Hospital-Charges/blob/main/ERD.png)

**Figure 1.** Entity-relationship diagram for datasets 
   
## Azure Pipeline
   
Azure was used to scale the data process and build a pipeline to clean and export the datasets to a SQL Database. The basic sequence of the pipeline for each dataset is as follows:
   
1. The local data is transferred to Azure Blob Storage
2. Data is cleaned and transformed using Azure Databricks. Notebooks used are found in the **Data cleaning** folder.
3. Cleaned data is exported back in separate Blob Storage folders as parquet files.
4. Tables are created from parquet files to a SQL Database.
   
![alt text](https://github.com/beatricetierra/CA-Hospital-Charges/blob/main/AzurePipeline.PNG)
   
**Figure 2.** Data pipeline in Microsoft Azure

## Results

The result is a data warehouse of hospital, insurance, and patient expense data that can be used to estimate a patient's cost for a particular procedure of a particular area. Estimates would be more accurate with more available and up-to-date data from healthcare and insurance providers. Despite the current limitations of the current data warehouse, this demonstrates how to implement more transparent and searchable pricing of healthcare services. 
   
![alt text](https://github.com/beatricetierra/CA-Hospital-Charges/blob/main/SQLDatabase.PNG)
   
**Figure 3.** Standardized tables of healthcare datasets in SQL server.

