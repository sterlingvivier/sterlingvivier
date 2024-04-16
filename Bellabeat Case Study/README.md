In March of 2023 I completed the Google Data Analytics course which concluded with a choice of a capstone project. Below is my complete 
analysis where I utilized Python to prepare, clean, and visualize my data. 

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
  print(da.dtype)
````

````
  print(wL.head())
  print(wL.shape)
  print(wL.dtype)
````
### Changing Datatypes
  For the following dataframes, I converted the ID column to a str variable and the ActivityDate/Date column(s) to a dateTime variable. 

````
  da['Id'] = da['Id'].astype(str) 
  da['ActivityDate'] = pd.to_datetime(da['ActivityDate'],format='%m/%d/%Y')
````
````
  wL['Id'] = wL['Id'].astype(str)
  wL['Date'] = pd.to_datetime(wL['Date'], format= 'mixed')
````
