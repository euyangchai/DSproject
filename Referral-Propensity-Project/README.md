
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

Firstly, we determine the userid(s) of the 2 groups of Setel customers

    1. Customers who have made at least one referral
    2. Customers who have never referred

with the condition that these customers must have made at least 3 fuel or store purchases of any amount 
because we want to avoid picking users who have just recently registered but haven’t gotten a chance to refer.

After having the list of customers, we brainstorm on the characteristics of these users that might affect whether they refer or not.

|Data|Attributes|
|---|---|
|Demograhics|Gender, age, tenure, state, preferred station, fuel grade, vehicle type, payment method, etc.|
|Transactional|First and latest transaction date, frequency of fuel purchase and top-up, ticket size ($) for fuel and top-up, fuel volume purchased, etc.|
|Marketing|Acquisition channels|

