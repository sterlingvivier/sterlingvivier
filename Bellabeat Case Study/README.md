In March of 2024 I completed the Google Data Analytics course which concluded with a choice of a capstone project. Below is my complete 
analysis where I utilized Python to prepare, clean, and visualize my data. 
# Links 
* Link to the full [kaggle/jupyter notebook](https://www.kaggle.com/code/sterlingvivier/final-bellabeat/notebook) 
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
  sleep = pd.read_csv('/kaggle/input/fitbit/mturkfitbit_export_4.12.16-5.12.16/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv')
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

````
  print(sleep.head())
  print(sleep.shape)
  print(sleep.dtypes)

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
````
  sleep['Id'] = sleep['Id'].astype(str)
  sleep['SleepDay'] = pd.to_datetime(sleep["SleepDay"], format= 'mixed')
  sleep.dtypes
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
  print(wL.isna().sum())
  print(sleep.isna().sum())
````

````
  print(da['Id'].unique().size)
  print(wL['Id'].unique().size)
  print(sleep['Id].nunique())
````

````
  wL.drop('Fat', axis= 1)
````

````
  sleep[sleep.duplicated()]
  sleep.drop_duplicates(inplace = True)
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

  Adding conditions on how active one is, taking into account a healthy amount of time to sleep/rest during the day
````
  con1 = [da['SedentaryMinutes'] <=700 , (da['SedentaryMinutes']>700) & (da['SedentaryMinutes'] <=1100),
                da['SedentaryMinutes'] > 1100]
  values1 = ['Very Active', 'Active', 'Sedentary']
  da['ActiveClass'] = np.select(con1, values1)
````

  Adding columns to our sleep dataframe for day of the week and how long an individual slept for in terms of hours. 

````
  sleep['DayOfWeek'] = sleep['SleepDay'].dt.day_name()
  sleep['Hours'] = sleep['TotalMinutesAsleep']/60
````


### Dropping Columns/Merging Data
  I created a new dataframe that contained only columns that I would use for my analysis/visualations. Once created, I merged this dataframe with my weightLogInfo data.

````
  da2 = da.drop(['TrackerDistance','LoggedActivitiesDistance', 'VeryActiveDistance', 'ModeratelyActiveDistance',
             'LightActiveDistance', 'SedentaryActiveDistance', 'VeryActiveMinutes', 
              "FairlyActiveMinutes", 'LightlyActiveMinutes'], axis = 1 )
````

  Now, I combined all three dataframes together into one in order to be able to group together data from different files in my analysis. 
  
````
  res = pd.concat([da2, wL, sleep])
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

* This first figure shows that user's are below the average on Thursday and Sunday and above average the rest of the week. Saturday and Tuesday have the highest values, indicating that user's are burning more calories on these days. 

````
  plt.figure(figsize= (10,5))
  plt.bar(Day, Steps, color = np.random.rand(len(Day), 3))
  plt.axhline(da2['TotalSteps'].describe().loc['mean'], c= 'black', label = "%.2f" % da2['TotalSteps'].describe().loc['mean'])
````
<a href="https://ibb.co/xhwWZvP"><img src="https://i.ibb.co/K6BdnZR/StepDay.png" alt="StepDay" border="0"></a>

* Once again, the days that fall below the average line are Sunday and Thursday. Additionally, Friday and Wednesday are below the average, with only Monday, Tuesday, and Saturday being higher. 
````
  meanLine= ((da2['TotalActiveMinutes'].describe().loc['mean'])/1440)*100
  plt.figure(figsize= (10,5))
  plt.bar(Day, MinutesActivePerc, color = np.random.rand(len(Day), 3))
  plt.axhline(y= meanLine, c= 'black', label = "%.2f" % meanLine)
````

<a href="https://ibb.co/hFRW6P6"><img src="https://i.ibb.co/DfDCJxJ/Active-Day.png" alt="Active-Day" border="0"></a>

* Our last figure follows the same trend seen in the first two charts. Saturday and Tuesday are above the average line. Although the amount of steps were lower on friday, the % of time spent active is above the mean line. This indicates that user's may still be active on this day however may be doing other workouts/activity besides just walking. 

### Totals vs Total Calories
  I next wanted to see how different totals were related to the amount of calories being burned by users. To do this, I compared the total amount of steps taken, the total distance, and the total time spent active. Below are the code snippets and graphs produced: 
  
````
   snb.scatterplot(data = da2, x = 'TotalSteps', y = 'Calories',
                  hue = 'ActiveClass', palette = 'crest').set_title('Total Steps vs Total Calories')
````


<a href="https://ibb.co/tYZzF9y"><img src="https://i.ibb.co/Jrxqg9X/Steps-Cal-1.png" alt="Steps-Cal-1" border="0"></a>

````
  snb.scatterplot(data = da2, x = 'TotalDistance', y = 'Calories',
                  hue = 'ActiveClass', palette = 'crest').set_title('Total Distance vs Total Calories')
````

<a href="https://ibb.co/51FSz9n"><img src="https://www.kaggleusercontent.com/kf/172605520/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..far1QiPSwnA8X7FDUeagHQ.NSI6pwogg90SDLdAwP73K7HpK71N0Eh3cPeevC57OagV5RyzetWiMbFL3nL7Tlw0KfOYktMsra6Fy2MRPMcxtuwMjru9cq1T231ZKVqYMrAbav6TpONbVSKeA7SaCNV9f_CGtl_b1hQTEAxsM6T2NN_m8u-qpWCg3KpDEpgS6cd0dgf3eiKhWZ37H5ZWqTIn7bxL40f6votwVDXJZWV07BjDPrdBrpiTscyLk0Jc07leDE3BBjjCBYsn8TWMZDzm__MVgGM9Mr2YfdpvIAtxqLcLDziw21glMP0HFGA84PdMYuIqW8cINeNQg4A2jO2qvCSdJsNNrufKLNP_wZDLQXBzvTyPrx82PceEfEokT89BzysT9BciGAu5Tk2C39TkzRkjZcpGJNMkcE6MRHUGgfKyjbJ3_SDL42gDVCWGJr1ENTss7xJcfE0sX0IV3U8hXjkXmfpDgo1M3nHnDntE6e_tbEc2h1nr4LNDVP4XEhD37BOehaFIpRf6wrbCnxF2C5a-vsyC7j_SizTNwnCjybKJ03Lzwilz8jIAHfswiziNetKliZUl9mh3nF2SkAuZHAga_3MKjF8a_aACp8mLAW5oLlGwif80nL2BeTdkjv82kU7QIiVK6T-N46zJHWZTwujX1Seic5e4PMOmWaHKQg.LAP0O7o2ut8ND2V4bmCclA/DistanceCal.png" alt="Distance-Cal-1" border="0"></a>

````
  snb.scatterplot(data = da2, x = 'TotalActiveMinutes', y = 'Calories',
                  hue = 'ActiveClass', palette = 'crest').set_title('Total Active Minutes vs Total Calories')
````
<a href="https://ibb.co/GcQVft4"><img src="https://i.ibb.co/nzMDqnN/Minutes-Cal-1.png" alt="Minutes-Cal-1" border="0"></a>

### Individual User Avg vs User's Avg Calories

````
  for i in range (len(result['TotalStepsAvg'])):
      snb.regplot(data = result, x = "TotalStepsAvg", y = "CaloriesAvg",
          scatter_kws = {"color": np.random.rand(len(result['TotalStepsAvg']), 3)},
          label = f"USER {i+1}", ci = None)
````

<a href="https://ibb.co/MMkv9rn"><img src="https://www.kaggleusercontent.com/kf/172605520/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..x3X_MNqYiE5y_0WHT71M6Q.271c5lpfr4NCd33pvbYvWBSjKyXhpLiXcTYeXZ_12iwbKkyFiMzJroYYmJ2SQdRP7GUGvxTcOMCuNH2P0BvHtWpf9Yt8bjY1FuUVu4JcDFr6185GHf0n9HnbazT1hVIYxBlaxgQgy8pZIHJTWrbHtM3BU9T5ELKNDy99iK3vjcDh8CiOzvPemGbnOmo_JPy90Kvlw8fcNCQIs4S4u4Fl7dB7SSlgU0-NHz7PGnN0ehLLZ2WpQJySOdUr2LBSQ8iPwvyfH21vBo4OzeQpoXDnncDDoVirPSnzhaeM35xd2l2bg0eJ8814SFEK3Y-WefInw-DrxF0AbX00w2XU1E6uOY2Q1ay2nhPcWZ-Tzm_6-0ITcB6J7iWIMDMblzLmvT29VPI4nrQqmu8-PGlov9jCaRgifdl29B9K2URIo01oDGevvvn3x2TnRioVE59SVLcwKVLBfLSUNKbzBO8tRCwwES3B9f1FeSRAD7h2oGFfgWbWHIFwD7WVJA-RfKRahRPDClqegPjw1SfWhP0HwDqnXBt87FIjEokl1lbL0np0Gopn7Q8Ian2X0I-BPkjVhBB229emNAvt3k7aFeyU2unf8O5rdn4x3zT4YxOPe5mdzb26ceIKn5DiHghl2ovb6kHHJsvV-V09JUB5frds-EbtJw.t_gU9cMFbfEd-ngkhrnKiQ/AvgStepsUser.png" alt="Avg-Steps-User-2" border="0"></a>

````
  for i in range (len(result['TotalMinAvg'])):
      snb.regplot(data = result, x = "TotalMinAvg", y = "CaloriesAvg",
          scatter_kws = {"color": np.random.rand(len(result['TotalMinAvg']), 3)},
          label = f"USER {i+1}", ci = None)
  ````

<a href="https://ibb.co/60gytyQ"><img src="https://www.kaggleusercontent.com/kf/172605520/eyJhbGciOiJkaXIiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0..hpx8QnqDigSGXyyEkbzaFw.2qB9WDlqlWf8EbEahOWfSZQG-ZdgHOaovP1jV3q3iWoYCtCJjnSa--2LPoNIsEqZVmxPKAdrclufX-y08dMGzlMOBQUNzRed10-hWTZdG8XUiXc-SZYQH5LB8N82fWczNzGfYLMBPYyXTpM0-nm_G0n5cEIHKnVfRRW1Ff5AgiI6KfsDSXkya6G7SjRQKj_30ZMx_BptcoQoBB6b2MqeRmh6kaAkIJWqLDFpB4rXkfUQpVMdBr4g6uVE8aW3U_BZ4kZRBPmw5fISYH4x9BdGGESmP3O9W8vypYP6AosxObVW3T_N8lHXnS2eZcmXoXPg96-pK6WJX260RJUBzVdXdv6ex--7NFVVm3QfwT4nqEr69jvB5LIEHSyWApiCa-18V2V-byxU_Iko8iI8dELXl8Div9dWt9C8xVmDmqql2OD4xr6vsKYXILTtWcMdBO_rSt7Cf4kXsbZoaRf4yzjgLs7QSXPyCrpFQf_T8xZAyRpRtSMudqPpRUetXGnnJeHDaN6FFwcKGnisKJMhEG9vHDdseVWW_obXb68HBmzDwFd7t6Hyvip64vjhFYOD5MJPKoE2g3pdLcxu27VyQTa_eDX-Bw4_o9bPlfs9qoQrzVm225muaL2gRqMRpSfFO2rH4-Y0efCj_YcMQ4ckyBF8Yw.jYOTh_Jag7X3axrLg35XKQ/AvgMinsUser.png" alt="Avg-Mins-User-1" border="0"></a>

* Both charts demonstrate a somewhat positive correlation between the avg quantities and the amount of calories burned by each user. Due to the size of our data and not having that many distinct user's in our dataset, we are unable to determine an accurate conclusion. Many users that took more steps/were more active did tend to burn more calories on avg, however, the datasets still contained a few outliers. The amount of calories burned is also dependent on a variaty of different factors including one's height, age, muscle mass, etc. 

### Sleep Analysis 

````
  plt.bar(lab, counts, color = np.random.rand(len(lab), 3))
  plt.ylabel('User Count')
  plt.title('User Distribution of Time Asleep')
````
<a href="https://ibb.co/xYJKbD2"><img src="https://i.ibb.co/5kBX361/barSleep.png" alt="barSleep" border="0"></a>

````
  plt.pie(counts, labels =lab, colors = snb.color_palette('pastel'), autopct='%.0f%%')
  plt.show()
````

<a href="https://ibb.co/TMj7dfz"><img src="https://i.ibb.co/zV9BDMK/piechart.png" alt="piechart" border="0"></a>

* The graphs and charts show that just about half of the user's in this dataset are obtaining a healthy amount of sleep on avg. Getting the proper amount of sleep can have many health benefits and may be a factor in why other datasets have outliers. The min in the dataset was 1.02, meaning one of the users is severely under the recommended time. The amount of sleep can have many affects on an individual physically and mentally. 

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
