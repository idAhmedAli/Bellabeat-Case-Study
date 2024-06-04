# Bellabeat Case Study
Data Analysis by R

### Introduction
Bellabeat is a company known for its health-focused products for women founded in 2013 by Urška Sršen and Sando Mur, Bellabeat quickly established itself as a wellness tech company. By 2016, it had expanded globally and diversified its product range. Bellabeat leverages a mix of traditional and digital marketing, with a significant focus on online engagement through platforms like Google, Facebook, Instagram, and YouTube.

Key Figures:
- Urška Sršen: Cofounder and Chief Creative Officer.
- Sando Mur: Cofounder and key executive team member.
- Bellabeat Marketing Analytics Team.

Products:
- Bellabeat App: Tracks health metrics such as activity, sleep, stress, menstrual cycle, and mindfulness.
- Leaf: A wearable wellness tracker.
- Time: A wellness watch combining classic design with smart technology.
- Spring: A smart water bottle that tracks daily hydration.
- Bellabeat Membership: A subscription service offering personalized health guidance.

Project Goal:
Analyze consumer data from FitBit Fitness Tracker to identify trends and provide strategic marketing recommendations. This analysis aims to reveal new growth opportunities and enhance Bellabeat’s market presence.

### Data Source

[FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit/data)
This data set contains personal fitness tracker from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of
personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes
information about daily activity, steps, and heart rate that can be used to explore users’ habits.

ROCCC:
- Reliability: This dataset generated by respondents to a distributed survey via Amazon Mechanical Turk.
- Original: 30 eligible Fitbit users consented to the submission of personal tracker data.
- Comprehensive: The data size is small in terms of the number of participants (about 30 users) and the duration of the recording (only two months).
- Current: The data is from 12th March 2016 to 12th May 2016, so it Outdated.
- Cited:

### The Limitations:
- The dataset includes data from only about 30 users.
- The data lacks information on gender and age.


### Data Preparation and Cleaning:

#### Data Collection:

Install packages:
```R
install.packages("tidyverse")
install.packages("dplyr")
install.packages("ggplot2")
install.packages("readr")
install.packages("lubridate")
install.packages("janitor")

library(tidyverse)
library(dplyr)
library(ggplot2)
library(readr)
library(lubridate)
library(janitor)
```

Upload Files:
Since the data is divided into two folders, each for a different time period (from 12th March to 11th April and from 12th April to 12th May), we will merge the data files.

```R
daily_activity1 <- read.csv("dailyActivity.csv")
daily_activity2 <- read.csv("dailyActivity2.csv")
daily_activity <- bind_rows(daily_activity1, daily_activity2)
str(daily_activity)

weight_log1 <- read.csv("weightLogInfo1_merged.csv")
weight_log2 <- read.csv("weightLogInfo2_merged.csv")
weight_info <- bind_rows(weight_log1, weight_log2)
str(weight_info)

hourly_calories1 <- read.csv("hourlyCalories1_merged.csv")
hourly_calories2 <- read.csv("hourlyCalories2_merged.csv")
hourly_calories <- bind_rows(hourly_calories1, hourly_calories2)
str(hourly_calories)

hourly_intensities1 <- read.csv("hourlyIntensities1_merged.csv")
hourly_intensities2 <- read.csv("hourlyIntensities2_merged.csv")
hourly_intensities <- bind_rows(hourly_intensities1, hourly_intensities2)
str(hourly_intensities)

hourly_steps1 <- read.csv("hourlySteps1_merged.csv")
hourly_steps2 <- read.csv("hourlySteps2_merged.csv")
hourly_steps <- bind_rows(hourly_steps1, hourly_steps2)
str(hourly_steps)

minutes_met1 <- read.csv("minuteMETsNarrow1_merged.csv")
minutes_met2 <- read.csv("minuteMETsNarrow_merged.csv")
minutes_met <- bind_rows(minutes_met1, minutes_met2)
str(minutes_met)

minute_sleep1 <- read.csv("minuteSleep1_merged.csv")
minute_sleep2 <- read.csv("minuteSleep2_merged.csv")
minute_sleep <- bind_rows(minute_sleep1, minute_sleep2)
str(minute_sleep)

sleep_day <- read.csv("sleepDay_merged.csv")
head(sleep_day)
str(sleep_day)
```
#### Data Cleaning:
- Remove duplicates after verifying their presence.
- Clean the column names by converting all letters to lowercase and adding underscores between words.

```R
c(daily_activity = sum(duplicated(daily_activity)))
daily_activity <- clean_names(daily_activity)

c(weight_info = sum(duplicated(weight_info)))
weight_info <- weight_info %>% 
  distinct()
weight_info <- clean_names(weight_info)

c(hourly_calories = sum(duplicated(hourly_calories)),
  hourly_intensities = sum(duplicated(hourly_intensities)),
  hourly_steps = sum(duplicated(hourly_steps)))
hourly_calories <- hourly_calories %>% 
  distinct()
hourly_intensities <- hourly_intensities %>% 
  distinct()
hourly_steps <- hourly_steps %>% 
  distinct()

hourly_calories <- clean_names(hourly_calories)
hourly_intensities <- clean_names(hourly_intensities)
hourly_steps <- clean_names(hourly_steps)

c(minutes_met = sum(duplicated(minutes_met)))
minutes_met <- minutes_met %>% 
  distinct()
minutes_met <- clean_names(minutes_met)

c(minute_sleep = sum(duplicated(minute_sleep)))
minute_sleep <- minute_sleep %>% 
  distinct()
minute_sleep <- clean_names(minute_sleep)

c(minute_sleep = sum(duplicated(sleep_day)))
sleep_day <- sleep_day %>% 
  distinct()
sleep_day <- clean_names(sleep_day)
```

#### Data Transformation:
Convert the data types of the date and time columns to a suitable data type.

```R

daily_activity <- daily_activity %>% 
  mutate(activity_date = mdy(activity_date))

weight_info <- weight_info %>% 
  separate(date, into = c("date", "time"), sep = " (?=\\d{1,2}:)")
weight_info <- weight_info %>% 
  mutate(date = mdy(date),
         time = parse_time(time)
  )


hourly_calories <- hourly_calories %>%
  separate(activity_hour, into = c("activity_date", "activity_hour"), sep = " (?=\\d{1,2}:)")
hourly_calories <- hourly_calories %>%
  mutate(activity_date = mdy(activity_date),
         activity_hour = parse_time(activity_hour))

hourly_intensities <- hourly_intensities %>% 
  separate(activity_hour, into = c("activity_date", "activity_hour"), sep = " (?=\\d{1,2}:)")
hourly_intensities <- hourly_intensities %>% 
  mutate(activity_date = mdy(activity_date),
         activity_hour = parse_time(activity_hour))

hourly_steps <- hourly_steps %>% 
  separate(activity_hour, into = c("activity_date", "activity_hour"), sep = " (?=\\d{1,2}:)")
hourly_steps <- hourly_steps %>%  
  mutate(activity_date = mdy(activity_date),
         activity_hour = parse_time(activity_hour))

minutes_met <- minutes_met %>%
  separate(activity_minute, into = c("activity_date", "activity_time"), sep = " (?=\\d{1,2}:)") %>%
  mutate(
    activity_date = mdy(activity_date),
    activity_time = parse_time(activity_time)
  )

minute_sleep <- minute_sleep %>%
  separate(date, into = c("sleep_date", "sleep_time"), sep = " (?=\\d{1,2}:)") %>%
  mutate(
    sleep_date = mdy(sleep_date),
    sleep_time = parse_time(sleep_time)
  )

sleep_day$sleep_day <- as.Date(sleep_day$sleep_day, format = "%m/%d/%Y")
```
#### Data Validation:

```R
summary(daily_activity)
summary(weight_info)
summary(hourly_calories)
summary(hourly_intensities)
summary(hourly_steps)
summary(minutes_met)
summary(minute_sleep)
summary(sleep_day)
```
Since the date columns range between 2016-03-12 and 2016-05-12, it means the merging process was successful, and the data types of the date and time columns were successfully converted. Additionally, the column names have been appropriately formatted.

### Analayzing
Check the number of users for each table

```r
n_distinct(daily_activity$id)
n_distinct(weight_info$id)
n_distinct(hourly_calories$id)
n_distinct(hourly_intensities$id)
n_distinct(hourly_steps$id)
n_distinct(minutes_met$id)
n_distinct(minute_sleep$id)
n_distinct(sleep_day$id)
```
The daily_activity, hourly_calories, hourly_intensities, hourly_steps, and minutes_met tables contain data for 35 users, while the weight_info table contains data for only 13 users. The minute_sleep and sleep_day tables contain data for 25 and 24 users, respectively.

```r
activities_summary <- daily_activity %>%
  summarise(
    logg_record = sum(logged_activities_distance > 0),
    tracker_record = sum(tracker_distance > 0),
    zero_record = sum(total_distance == 0),
    both_record = sum(tracker_distance > 0 & logged_activities_distance > 0),
    
    total_record = n(),
    logg_percentage = round((logg_record / total_record) * 100, 2),
    tracker_percentage = round((tracker_record / total_record) * 100, 2),
    zero_percentage = round((zero_record / total_record) * 100, 2),
    both_percentage = round((both_record / total_record) * 100, 2)
  )
print(activities_summary)
```
The total number of distance records in the daily_activity table is 1,397. Out of these:
- 56 records contain only logged_activities_distance, making up 4.01% of the total records.
- 1,253 records contain only tracker_distance, accounting for 89.69% of the total records.
- 52 records contain both logged_activities_distance and tracker_distance, representing 3.72% of the total records.
- 141 records do not contain any distance data, constituting 10.09% of the total records.






















