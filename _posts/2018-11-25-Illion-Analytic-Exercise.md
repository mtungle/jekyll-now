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
