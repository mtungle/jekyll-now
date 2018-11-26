---
layout: post
description: ou have been given a file containing information on a number of loan applicants.
tags: Machine-Learning Python
---

You have been given a file containing information on a number of loan applicants. Take the provided data set and attempt to identify what factors are useful in predicting loan performance. Report back on your findings, including any issues/concerns with the data.

## Summary

Best machine learning model: Decision tree.

Model accuracy: 76.0%

Model precision: 98.5%

Feature importance (from high to low): Score, age, time as customer, and age asset.

Issues with the data:

* Imbalanced data. After cleaning, BAD loan records are only 4.25% (74/1740) of the whole data set.
* Many missing and inconsistent data entries.
* Some unreliable score values (negative values).

## Data overview

The data has 3923 rows and 15 columns originally. A sample is as follow
```
  date_of_application date_of_birth  loan_financed_amount assessment_outcome  \
0          22/12/2014           NaN               5349.0            Approved   
1          11/12/2014           NaN              23350.0            Approved   
2           9/01/2015           NaN              10497.0            Approved   
3           5/12/2014     18-Jul-48               2920.0                 NaN   
4           9/12/2014     22-Jan-70              19466.0            Approved   
5           5/12/2014      5-Jan-68              25350.0                 NaN   
6           5/12/2014     13-Dec-79              18850.0                 NaN   
7          15/12/2014     19-Aug-73              36240.0            Approved   
8           7/12/2014     13-Nov-67              29240.0                 NaN   
9           8/12/2014     28-Aug-70              35750.0                 NaN   

   time_as_customer age_asset   residential_type years_at_residence  \
0             130.0       New                NaN                NaN   
1              28.0       New                NaN                NaN   
2              27.0       New                NaN                NaN   
3               NaN       New               RENT   LESS THAN 1 YEAR   
4              41.0       New                NaN                NaN   
5               NaN        8+  OWN YOUR OWN HOME        1 - 3 YEARS   
6               NaN       New  OWN YOUR OWN HOME        1 - 3 YEARS   
7              28.0       New              BOARD        1 - 3 YEARS   
8               NaN       New  OWN YOUR OWN HOME  MORE THAN 5 YEARS   
9               NaN       New               RENT        1 - 3 YEARS   

   number_of_credit_cards         occupation occupation_type  \
0                  0.0                   NaN             NaN   
1                  0.0                   NaN             NaN   
2                  0.0                   NaN             NaN   
3                  NaN               RETIRED         RETIRED   
4                  1.0                   NaN             NaN   
5                  NaN               ADVISOR       FULL TIME   
6                  NaN            TECHNICIAN       FULL TIME   
7                  0.0     CUSTOMER SERVICES       FULL TIME   
8                  NaN      TICKET INSPECTOR       FULL TIME   
9                  NaN              LABOURER       FULL TIME   

     occupation_years  previous_defaults  score loan_performance  
0                 NaN                  0    519             GOOD  
1                 NaN                  0    650             GOOD  
2                 NaN                  0    648             GOOD  
3   MORE THAN 4 YEARS                  0    715              NaN  
4                 NaN                  0    717             GOOD  
5   MORE THAN 4 YEARS                  0    777              NaN  
6  6 MONTHS - 2 YEARS                  0    682              NaN  
7   2 YEARS - 4 YEARS                  0    561              BAD  
8   MORE THAN 4 YEARS                  1    523              NaN  
9   2 YEARS - 4 YEARS                  0    576              NaN  
```
From the data, we would like to predict the loan performance to be either GOOD or BAD.

## The Question

From the data, the majority of loan performance is GOOD and there is only a very small portion of loan performance is actually BAD. Thus, correctly identifying the BAD loans is much more valuable than the GOOD loans. In practice, if a loan application is labeled as BAD, the application would not be proceeded, thus the lender would neither lose or gain money regardless on the prediction accuracy. However, if a loan application is predicted to be GOOD but turns out to be BAD, the lender would lose the lending money, so we want to eliminate this case. In other words, we want to maximize the probability that when a loan is predicted to be GOOD,  its performance is actually GOOD. The technical term for this probability is precision. Hence, we will maximize the model precision in later analysis.









