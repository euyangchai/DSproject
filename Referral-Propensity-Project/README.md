
# Referral Propensity Project

DISCLAIMER:

## Introduction

Propensity modelling is the application of mathematical models to predict whether users will perform a particular action. 
At Setel, propensity models could help marketers identify who among the audience is most likely to make a fuel or store purchase, top-up, refer a friend (RAF), etc. 
Marketers could then focus efforts and resources such as personalized products, messages and offers to give the identified users the nudge they need to pull the trigger. 
I will be focusing on the referral action in this project.

## Problem Statement

The number of referrers and referees that originated from recent registration months has **decreased**. 
In other words, users from younger registration cohorts refer lesser than users of older registration cohorts.

## Proposed Solution

To increase RaF acquisitions, we can build a machine learning model that predicts whether a user is likely to perform a referral action. 
Each user will be assigned a probability score, making this a binary classification problem.

## Gathering Data

#### Sample Of Users

Firstly, we determine the userid(s) of the 2 groups of Setel customers

    1. Customers who have made at least one referral
    2. Customers who have never referred

with the condition that these customers must have made at least 3 fuel or store purchases of any amount 
because we want to avoid picking users who have just recently registered but haven’t gotten a chance to refer.

#### User Attributes

After having the list of customers, we brainstorm on the characteristics of these users that might affect whether they refer or not.

|Data|Attributes|
|---|---|
|Demograhics|Gender, age, tenure, state, preferred station, fuel grade, vehicle type, payment method, etc.|
|Transactional|First and latest transaction date, frequency of fuel purchase and top-up, ticket size ($) for fuel and top-up, fuel volume purchased, etc.|
|Marketing|Acquisition channels|

#### Feature Engineering

|Attribute|Derived from|
|---|---|
|`active`|`max_transaction_date_gmt8` - `min_transaction_date_gmt8`|
|`avg_days_btw_purchases`|`active` / `fuel_purchase_freq`|
|`avg_days_btw_topups`|`active` / `topup_freq`|
|`avg_fuel_volume`|`total_fuel_volume` / `fuel_purchase_freq`|
|`avg_fuel_spent`|`total_fuel_spent` / `fuel_purchase_freq`|
|`avg_topup_spent`|`total_topup_spent` / `topup_freq`|

## Exploratory Data Analysis (EDA)

#### Missing Values

We noticed that a hefty 35% of records do not have information on age and gender but we cannot afford to drop these rows therefore we have to impute them. 
Age is imputed with the average age of the users from that preferred station. 
Besides, we also introduced a new class for gender - “Undefined” to replace records with missing gender information.
After imputing age and gender, we drop the remaining records with missing values as it is only ~2% of the dataset. 
Despite dropping those records, we still have a balanced dataset for classification.

#### Binning

To prevent overfitting, we attempt to reduce the number of unique values for categorical features especially for `stationid`, `channel`, `vehicle_type`.

Unfortunately, `stationid` consists of too many unique values thus we will be dropping the feature itself.

> For future model improvement, we can try clustering the stations based on latitude and longitude.

For `channel`, we will group channels with fairly similar operations. 
We will also be combining the minority channels into one and name it *Others*. 
The remaining channels will remain as standalone channels.

For `vehicle_type`, we group luxury cars and regular cars as *cars*; premium bikes and regular bikes as *bikes*; pick-up trucks, light commercial vehicles, and heavy trucks are classified as *commercial vehicles*.
