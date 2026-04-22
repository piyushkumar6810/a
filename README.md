# Power Demand Forecasting

## What project does

I tried to predict the next hour's electricity demand on the national grid using past data. So basically if we know the demand till now, can we guess what it will be in the next hour. That is the goal.



## Files I used

- PGCB_date_power_demand.xlsx - this has hourly electricity demand data
- weather_data.xlsx - this has temperature, humidity, rain etc for each hour
- economic_full_1.csv - this has yearly economic data like GDP, electricity access etc



## What I did step by step

## Loading and cleaning demand data

I loaded the demand file and noticed some rows had :30 timestamps (like 5:30, 6:30) along with the normal hourly ones. So I combined them into one hourly value using a weighted average where I gave 2/3 weight to the hourly reading and 1/3 to the half hourly one.

Then I found some hours were completely missing so I just filled them using the previous hour value (forward fill).

I also removed extreme outliers using the IQR(interquartile range) method. Basically any value too far from normal range I replaced with NaN and then filled using interpolation.

## Loading weather data

The weather file had 3 rows of metadata at the top so I skipped them. Then I renamed the columns to simple names(like as they also showed _mm and degree ) and made it hourly.

## Loading economic data

This file had yearly data in wide format where each year was a column. I picked 3 indicators that seemed useful to me - electricity access, power consumption per person and GDP. Then I converted it to long format and merged it with the hourly data by matching the year.

## Feature engineering

 Since XGBoost does not understand time on its own I had to manually create features that capture time patterns.

I added hour, day of week, month, and whether it is a weekend or not.

I also added cyclical features for hour using sin and cos because hour 23 and hour 0 are actually close to each other and normal numbers would not capture that.

Then I added lag features which are just past demand values. Like what was the demand 1 hour ago, 2 hours ago, 24 hours ago, 1 week ago etc. These are very useful because demand usually follows a pattern.

I also added rolling averages like average demand in last 3 hours, 24 hours, and 1 week.

I added temperature squared because both very hot and very cold weather increases electricity demand due to AC and heating, so the relationship is not linear.

I also added how much demand changed in last 1 hour and last 24 hours.

The target column is just next hour demand which I created by shifting demand by -1.

## Train test split

I trained the model on data from 2015 to 2023 and tested it on 2024. I did not shuffle the data because time series data should always be split in order otherwise you are using future data to predict the past .

## Model

I used XGBoost which is a tree based model. I used 1000 trees with a low learning rate of 0.03 so it learns slowly but better. I also added some regularization to avoid overfitting.

## Results

The model gives MAPE score on 2024 test data. MAPE is mean absolute percentage error .I got mape as 4.97%.

I also plotted actual vs predicted for the first 7 days of 2024 and a feature importance chart showing which features the model used the most.



## lib needed to be downloaded.


pip install pandas numpy matplotlib xgboost scikit-learn openpyxl.


Then just run the notebook cell by cell.
