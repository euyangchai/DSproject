
# Referral Propensity Project

DISCLAIMER:

## Introduction

Propensity modeling is the application of mathematical models to predict whether users will perform a particular action. 
At Setel, propensity models could help marketers identify who among the customers are most likely to make a fuel or store purchase, top-up, refer a friend (RAF), etc. 
Marketers could then focus efforts and resources such as personalized products, messages and offers to give the identified users the nudge they need to pull the trigger. 
I will be focusing on the **referral action** in this project.

## Problem Statement

The number of referrers and referees that originated from recent registration months has **decreased**. 
In other words, users from younger registration cohorts refer lesser than users of older registration cohorts.

## Proposed Solution

To increase acquisitions through RAF, I can build a machine learning model that predicts whether a user is likely to perform a referral action. 
Each user will be assigned a probability score, making this a binary classification problem.

## Gathering Data

#### Sample Of Users

Firstly, I identified the userid(s) of the 2 groups of Setel customers

    1. Customers who have made at least one referral
    2. Customers who have never referred

with the condition that these customers must have made at least 3 fuel or store purchases of any amount 
because I want to avoid picking users who have just recently registered but haven’t gotten a chance to make a referral.

#### User Attributes

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

I noticed that a hefty 35% of records do not have information on `age` and `gender` but I cannot afford to drop these rows therefore I imputed them. 
`Age` is imputed with the average age of the users from that preferred station. 
Besides, I also introduced a new class for `gender` *Undefined* to replace records with missing gender information.

After imputing `age` and `gender`, I dropped the remaining records with missing values as it is only ~2% of the dataset. 
Despite dropping those records, the dataset is still balanced for classification.

#### Binning

To prevent overfitting, I attempted to reduce the number of unique values for categorical features especially for `stationid`, `channel`, `vehicle_type`.

Unfortunately, `stationid` consists of too many unique values thus I dropped the feature itself.

> For future model improvement, I can try clustering the stations based on latitude and longitude.

For `channel`, I grouped channels with fairly similar operations. 
I also combined the minority channels into one and name it *Others*. 
The remaining channels remained as standalone channels.

For `vehicle_type`, I grouped luxury cars and regular cars as *cars*; premium bikes and regular bikes as *bikes*; pick-up trucks, light commercial vehicles, and heavy trucks are classified as *commercial vehicles*.

## Description Of Features

This is the final dataset that I used for the training and testing of machine learning models.

|Feature|Data Type|Description|
|---|---|---|
|`refer`|integer|Binary indication of whether the user has ever referred|
|`age`|float|User’s age|
|`gender`|object|User’s gender|
|`tenure`|integer|Number of days elapsed since registration date|
|`channel`|object|Acquisition channel attributed to the user|
|`state`|object|State that the user lives in (mode)|
|`fuel_grade`|object|User’s preferred fuel grade (mode)|
|`vehicle_type`|object|User’s predicted vehicle type (mode)|
|`paymentmethod`|object|User’s preferred payment method (mode)|
|`avg_days_btw_purchases`|float|Average number of days between purchases|
|`avg_fuel_volume`|float|Average fuel volume per transaction|
|`avg_fuel_spent`|float|Average RM spent on purchasing fuel|
|`avg_topup_spent`|float|Average RM spent on top-up|


## Data Splitting

The dataset is to be divided into 2 subsets, with the first subset being used to fit the machine learning model (train dataset) and the second subset being used as input to the model 
in which the predictions made are compared to the expected values (test dataset).

For this project, I used a split percentage of 70%-30% (70% train, 30% test). 
This resulted in train and test datasets of sizes 180,185 and 77,223 respectively.

## Data Preprocessing

After obtaining the train dataset, I transformed both numerical features and categorical features from the train dataset to better fit some of the machine learning models.

Numerical features are standardized (rescaled to ensure mean and standard deviation to be 0 and 1 respectively) using Scikit-learn’s `StandardScaler()` function.

Categorical features are One-Hot Encoded which each category value is converted into a new column and assigned a 0 (false) or 1 (true) using Scikit-learn’s `OneHotEncoder()` function.

## Model Fitting

I experimented with multiple baseline machine learning algorithms below:
* Logistic Regression
* Support Vector Machines
* k-Nearest Neighbours
* Naive Bayes
* Extreme Gradient Boosting (XGBoost)
* Categorical Gradient Boosting (CatBoost)
>  The CatBoost model is the only model that was trained using the non-pre-processed train dataset.

## Model Performance

|Model|Precision|Recall|f1-score|Training Time|
|---|---|---|---|---|
|Logistic Regression|0.66|0.66|0.66|~ 1 minute|
|Support Vector Machines|0.69|0.69|0.69|~ 1 hour|
|k-Nearest Neighbours|0.65|0.65|0.65|~ 1 second|
|Naive Bayes|0.62|0.62|0.61|~ 1 second|
|Extreme Gradient Boosting|0.69|0.69|0.69|~ 1 minute|
|Categorical Gradient Boosting|0.71|0.70|0.70|~ 5 minutes|

## Model Evaluation

Categorical Gradient Boosting (CatBoost) seems like the best performing model with a reasonable model training time. 
CatBoost eliminates the need for data pre-processing because transforming categorical features into numeric features (one-hot-encoding) is not needed. 
Thus, I will be able to use the dataset in its original format, which is simpler and more conducive for deployment.

||Predicted:0|Predicted 1|
|---|---|---|
|Actual: 0|25,200 (65%)|13,393 (35%)|
|Actual: 1|9,463 (24%)|29,167 (76%)|

**Sensitivity**: 29,167 / (29,167 + 9,463) = 0.75 (75%)

**Specificity**: 29,167 / (29,167 + 13,393) = 0.69 (69%)

In conclusion, I'm able to engage with 75% of customers who will make a referral but missing out 25% for sure. 
Out of all the customers who are predicted to refer, 31% are incorrectly predicted to refer. 
If the goal is to send out special incentives such as credits or loyalty points to encourage referral, 
it’s fine even to engage with those who are unlikely to refer as it does not cause any negative problem.
