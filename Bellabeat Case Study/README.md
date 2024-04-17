In March of 2024 I completed the Google Data Analytics course which concluded with a choice of a capstone project. Below is my complete 
analysis where I utilized Python to prepare, clean, and visualize my data. 
# Links 
* Link to the full kaggle/jupyter notebook file 
* Link to a dashboard created in Tableu

# Introduction
  Bellabeat is a high-tech manufacturer of health-focused products for women. Since 2013, they have been successful in this market, however, they have the ability to grow into a global-player. The co-founder believes that analyzing smart device fitness data could help unlock new growth opportunities for the company. It has been asked to analyze one of Bellabeat's products in order to gain more insights for the company.

# Ask
## Business Task
  - Analyze consumers use of an existing competitor to identify potential opportunities for growth and recommendations for the Bellabeat marketing strategy
## Problem
  - How can these trends found in smart-device usage be incorporated into Bellabeat's products to help inform the companies marketing strategies and decisons?

# Data used
  For this project I used the **FitBit Fitness Tracker** dataset which was made available by Mobius on the website Kaggle. There are 18 different csv files in our dataset following 30 different users. For my analysis, I looked at the dailyActivty_merged csv which combined data from multiple different files into one. Upon further inspection during the analysis process, the csv was found to contain information from 33 distinct users. 
  
  The data sheets contain data tracking heart rate, sleep time, weight, daily steps, daily activity and more. The data analyzed was collected between 03/12/2016–05/12/2016.

# Analysis
  For my case study, I specifically utilized the csv files labeled 
  * DailyActivities_merged.csv
  * WeightLogInfo_merged.csv
  * minuteSleep_merged.csv
  
# Project
### Loading Libraries
  I utilized the following libraries for my cleaning, analysis, and visualations:
  * numpy
  * scipy
  * pandas
  * matplotlib
  * seaborn
````
  import numpy as np
  import scipy as sp
  import pandas as pd
  import matplotlib.pyplot as plt
  import seaborn as snb
  
  #SETTING A STYLE FOR PLOTS
  plt.style.use("ggplot")
````
### Importing Files
  The following csv files were read into 3 dataframe variables using the pandas library:
````
  da = pd.read_csv('/kaggle/input/fitbit/mturkfitbit_export_4.12.16-5.12.16/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv')
  wL = pd.read_csv('/kaggle/input/fitbit/mturkfitbit_export_4.12.16-5.12.16/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv')
````

### Inspecting our Data
````
  print(da.head())
  print(da.shape)
  print(da.dtypes)
````

````
  print(wL.head())
  print(wL.shape)
  print(wL.dtypes)
````
### Changing Datatypes
  For the following dataframes, I converted the ID column to a str variable and the ActivityDate/Date column(s) to a dateTime variable. 

````
  da['Id'] = da['Id'].astype(str) 
  da['ActivityDate'] = pd.to_datetime(da['ActivityDate'],format='%m/%d/%Y')
  da.dtypes
````
````
  wL['Id'] = wL['Id'].astype(str)
  wL['Date'] = pd.to_datetime(wL['Date'], format= 'mixed')
  wL.dtypes
````

### Additional Cleaning/Analysis Prep
  Further cleaning of the data included checking for duplicated values, null values, the count of unique values, and dropping irrelevant columns that would not be used for further analysis. 
  
````
  da[da.duplicated()]
````

````
  wL[wL.duplicated()]
````

````
  print(da.isna().sum())
  print(' ')
  print(wL.isna().sum())
````

````
  print(da['Id'].unique().size)
  print(wL['Id'].unique().size)
````

````
  wL.drop('Fat', axis= 1)
````
### Adding Columns
  I want to add a column in my DailyActivity dataframe (da) that shows which day of the week the activity was logged along with a number assigned to the day. 
  * 0 = Monday, 6= Sunday

````
  da['DayOfWeek'] = da['ActivityDate'].dt.day_name()
  da['NumDayOfWeek'] = da['ActivityDate'].dt.weekday 
````

  Additionally, I wanted to add a column in this dataframe that added up all the columns of activity time. 
  * TotalActiveMinutes = LightlyActiveMinutes+FairlyActiveMinutes+VeryActiveMinutes

````
  da['TotalActiveMinutes'] = da['LightlyActiveMinutes']+da['FairlyActiveMinutes']+da['VeryActiveMinutes']
````

### Dropping Columns/Merging Data
  I created a new dataframe that contained only columns that I would use for my analysis/visualations. Once created, I merged this dataframe with my weightLogInfo data.

````
  da2 = da.drop(['TrackerDistance','LoggedActivitiesDistance', 'VeryActiveDistance', 'ModeratelyActiveDistance',
             'LightActiveDistance', 'SedentaryActiveDistance', 'VeryActiveMinutes', 
              "FairlyActiveMinutes", 'LightlyActiveMinutes'], axis = 1 )
````

````
  res = pd.concat([da2, wL])
````

## Charts/Analysis
### Day of Week
  I wanted to first see how averages of columns such as Calories, TotalSteps, and TimeActive changed throughout the days of the week. To do this, I grouped the day by each day of the week and calculated the different averages of each group. The code used and graphs produced are shown as follows: 

````
  plt.figure(figsize= (10,5))
  plt.bar(Day, Cal, color=np.random.rand(len(Day), 3))
  plt.axhline(y=da2['Calories'].describe().loc['mean'], c= 'black', label = "%.2f" % da2['Calories'].describe().loc['mean'])
````

<a href="https://ibb.co/mNyL9B0"><img src="https://i.ibb.co/CP9kbt6/Cal-Day-1.png" alt="Cal-Day-1" border="0"></a>


````
  plt.figure(figsize= (10,5))
  plt.bar(Day, Steps, color = np.random.rand(len(Day), 3))
  plt.axhline(da2['TotalSteps'].describe().loc['mean'], c= 'black', label = "%.2f" % da2['TotalSteps'].describe().loc['mean'])
````
<a href="https://ibb.co/xhwWZvP"><img src="https://i.ibb.co/K6BdnZR/StepDay.png" alt="StepDay" border="0"></a>

````
  meanLine= ((da2['TotalActiveMinutes'].describe().loc['mean'])/1440)*100
  plt.figure(figsize= (10,5))
  plt.bar(Day, MinutesActivePerc, color = np.random.rand(len(Day), 3))
  plt.axhline(y= meanLine, c= 'black', label = "%.2f" % meanLine)
````

<a href="https://ibb.co/hFRW6P6"><img src="https://i.ibb.co/DfDCJxJ/Active-Day.png" alt="Active-Day" border="0"></a>

### Totals vs Total Calories
  I next wanted to see how different totals were related to the amount of calories being burned by users. To do this, I compared the total amount of steps taken, the total distance, and the total time spent active. Below are the code snippets and graphs produced: 

<a href="https://ibb.co/tYZzF9y"><img src="https://i.ibb.co/Jrxqg9X/Steps-Cal-1.png" alt="Steps-Cal-1" border="0"></a>
<a href="https://ibb.co/51FSz9n"><img src="https://i.ibb.co/XW8Kd43/Distance-Cal-1.png" alt="Distance-Cal-1" border="0"></a>
<a href="https://ibb.co/GcQVft4"><img src="https://i.ibb.co/nzMDqnN/Minutes-Cal-1.png" alt="Minutes-Cal-1" border="0"></a>

### Individual User Avg vs User's Avg Calories

<a href="https://ibb.co/MMkv9rn"><img src="https://i.ibb.co/LndFvmS/Avg-Steps-User-2.png" alt="Avg-Steps-User-2" border="0"></a>

<a href="https://ibb.co/60gytyQ"><img src="https://i.ibb.co/B2qf6fk/Avg-Mins-User-1.png" alt="Avg-Mins-User-1" border="0"></a>

### Sleep Analysis 

### Weight Log 

| Id         |   WeightPoundAvg |   BMIAvg | BMIClass   |
|------------|------------------|----------|------------|
| 1503960366 |          115.963 |  22.65   | average    |
| 2873212765 |          125.663 |  21.57   | average    |
| 6962181067 |          135.702 |  24.028  | average    |
| 4558609924 |          153.53  |  27.214  | overweight |
| 4319703577 |          159.504 |  27.415  | overweight |
| 8877689391 |          187.714 |  25.4871 | overweight |
| 5577150313 |          199.959 |  28      | overweight |
| 1927972279 |          294.317 |  47.54   | obese      |

# Summary
  1. The charts produced for each day of the week consistently show that user's tend to be most active on Tuesdays' and Saturday's, with Sunday's being the least active day. From this data one recommendation for Bellabeat would be to send notifcations on this day to be active/complete some health goal. Additionally, Bellabeat should create incentives for users who do meet their activity goals and to help promote usage with the app/motivation on these "lazier" days.
  2. User's that take more steps tend to burn more calories. One app reminder can be to take a quick pause from whatever one is doing whether it be working, watching tv, etc, and to go on a brisk 5-10 minute walk.
  3. Monitering sleep and ensuring user's are not sleeping too little/too much in order to ensure a healthy balance between sedentary time and time spent active. One way of doing this may be to have the user's create sleep goals and to send out reminder's of when a user should be going to bed with a suggested alarm time. 
