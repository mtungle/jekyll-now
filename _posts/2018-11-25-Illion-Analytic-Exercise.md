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

Maximize model precision.

From the data, the majority of loan performance is GOOD and there is only a very small portion of loan performance is actually BAD. Thus, correctly identifying the BAD loans is much more valuable than the GOOD loans. In practice, if a loan application is labeled as BAD, the application would not be proceeded, thus the lender would neither lose or gain money regardless on the prediction accuracy. However, if a loan application is predicted to be GOOD but turns out to be BAD, the lender would lose the lending money, so we want to eliminate this case. In other words, we want to maximize the probability that when a loan is predicted to be GOOD,  its performance is actually GOOD. The technical term for this probability is precision. Hence, we will maximize the model precision in later analysis.

## Data Preparation

#### Remove irrelevant columns

'Occupation' should not be a factor to determine loan performance. Other reason is that this column has too many non-numeric values so it is not useful for our model anway.

'Assessment outcome' column has only value 'Approved' or missing data. Thus, it will not provide any additional information to our model.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import collections
import sklearn
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC 
from sklearn import neighbors
from sklearn.naive_bayes import GaussianNB
import pydotplus

# Set random seed
np.random.seed(1)

#Pandas options
pd.set_option('display.max_rows',200)
pd.set_option('display.max_columns',20)
pd.set_option('display.max_colwidth',20)

data_frame = pd.read_csv('TestData.csv')

#Drop irrelevant columns
data_frame=data_frame.drop(['assessment_outcome','occupation'],axis=1)
```

#### Drop missing data entry

```python
#Drop missing data entry
data_frame.dropna(inplace=True)
```

#### Drop negative score

Negative scores may be legit and have some meanings. However, due to lack of explaination we do not include negative score in our analysis. 

```python
data_frame=data_frame[data_frame['score']>=0]
```

#### Convert time

Extract the following information as new columns: 'age of applicant', 'year of application', 'month of application'.

```python
#Convert date_of_application and date_of_birth into datetime
new_column=pd.to_datetime(data_frame['date_of_application'])
data_frame['date_of_application']=new_column
new_column=pd.to_datetime(data_frame['date_of_birth'])
data_frame['date_of_birth']=new_column

#Add year_of_application, month_of_application, and age into the data_frame
data_frame['year_of_application']=pd.DatetimeIndex(data_frame['date_of_application']).year
data_frame['month_of_application']=pd.DatetimeIndex(data_frame['date_of_application']).month
data_frame['year_of_birth']=pd.DatetimeIndex(data_frame['date_of_birth']).year
data_frame.loc[data_frame['year_of_birth']>2017,'year_of_birth']-=100
data_frame['age']=(2017 - data_frame['year_of_birth'])

#Drop redundance date_of_application, date_of_birth, year_of_birth
data_frame=data_frame.drop(['date_of_application', 'date_of_birth', 'year_of_birth'],axis=1)
```
#### Convert 'age_asset' to integer, remove the '+' sign

```python
#Convert age_asset to integer, remove the '+' sign
data_frame.loc[data_frame['age_asset']=='New','age_asset']='0'
data_frame['age_asset'].replace(regex=True,inplace=True,to_replace='\+',value='')
data_frame['age_asset']=pd.to_numeric(data_frame['age_asset'],errors='coerce')
```

#### Fix inconsistent data type

```python
#Fix typo in data entry

#data_frame['residential_type'].unique()
#Out[173]: 
#array(['BOARD', 'OWN YOUR OWN HOME', 'RENT', 'SUPPLIED BY EMPLOYER',
#       'LIVE WITH RELATIVES', 'OTHER', 'OWN', 'OWN YOUR OWN HOUSE'],
#      dtype=object)
data_frame['residential_type'].replace(inplace=True,to_replace=['OWN','OWN YOUR OWN HOME'],value='OWN YOUR OWN HOUSE')

#data_frame['years_at_residence'].unique()
#Out[177]: 
#array(['1 - 3 YEARS', 'MORE THAN 5 YEARS', 'LESS THAN 1 YEAR',
#       '3 - 5 YEARS', '1', '4', '3 YEARS', '5 YEARS OR MORE',
#       '1 - 2 YEARS', '1 YEAR', 'OVER 4 YEARS', 'OVER 1 YEAR',
#       'LESS THAN ONE YEAR', '1 YEAR OR MORE', '5 YEARS OR OVER',
#       '1-3 YEARS', '3-5  YEARS', '3-5 YEARS'], dtype=object)
data_frame['years_at_residence'].replace(inplace=True,to_replace=['5 YEARS OR MORE','5 YEARS OR OVER'],value='MORE THAN 5 YEARS')
data_frame['years_at_residence'].replace(inplace=True,to_replace=['3 YEARS','OVER 4 YEARS','3-5  YEARS','3-5 YEARS','4'],value='3 - 5 YEARS')
data_frame['years_at_residence'].replace(inplace=True,to_replace=['1','1 - 2 YEARS','1 YEAR','OVER 1 YEAR','1 YEAR OR MORE','1-3 YEARS'],value='1 - 3 YEARS')
data_frame['years_at_residence'].replace(inplace=True,to_replace=['LESS THAN ONE YEAR'],value='LESS THAN 1 YEAR')

#data_frame['occupation_type'].unique()
#Out[180]: 
#array(['FULL TIME', 'RETIRED', 'SELF EMPLOYED', 'PART TIME', 'OTHER',
#       'HOMEMAKER', 'STUDENT', 'FULLTIME', 'FULL'], dtype=object)
data_frame['occupation_type'].replace(inplace=True,to_replace=['FULLTIME','FULL'],value='FULL TIME')

#data_frame['occupation_years'].unique()
#Out[181]: 
#array(['2 YEARS - 4 YEARS', 'MORE THAN 4 YEARS', 'LESS THAN 6 MONTHS',
#       '6 MONTHS - 2 YEARS', '2 - 4 YEARS', '3 YEARS', '2 YEARS',
#       '5 YEARS', '1 YEAR', 'OVER 4 YEARS', 'LESS THAN 1 YEAR',
#       '1 YEAR OR OVER', '1 YEAR OR MORE', '5 YEARS OR MORE',
#       '6 MOTHNS - 2 YEARS', 'LESS THEN 6 MONTHS'], dtype=object)
data_frame['occupation_years'].replace(inplace=True,to_replace=['5 YEARS','OVER 4 YEARS','5 YEARS OR MORE'],value='MORE THAN 4 YEARS')
data_frame['occupation_years'].replace(inplace=True,to_replace=['2 - 4 YEARS','3 YEARS','2 YEARS'],value='2 YEARS - 4 YEARS')
data_frame['occupation_years'].replace(inplace=True,to_replace=['1 YEAR','LESS THAN 1 YEAR','1 YEAR OR OVER','1 YEAR OR MORE','6 MOTHNS - 2 YEARS'],value='6 MONTHS - 2 YEARS')
data_frame['occupation_years'].replace(inplace=True,to_replace=['LESS THEN 6 MONTHS'],value='LESS THAN 6 MONTHS')
```

#### Convert non-numeric data into binary

```python
#One-hot encode data
labels=data_frame['loan_performance']
data_frame=data_frame.drop('loan_performance',axis=1)
data_frame=pd.get_dummies(data_frame)
data_frame['loan_performance']=labels
```

## Data Visualization

We scatterplot 'score', 'age', 'time as customer', 'age asset'. The big dots are BAD loans and the small dots are GOOD loans.

![score](https://mtungle.github.io/images/Illion-Analytic-Exercise/score.png)









