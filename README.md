# Investigation on the Relationship Between Preparation Time and Rating of Recipes

Authors: Ellen Wrightsman & Erin Li

## Overview

This data science project for DSC80 at UCSD investigates how the preparation time of a recipe correlates with its user ratings.

## Introduction

Food is an integral part of our daily lives, and cooking is a cherished hobby that brings joy and satisfaction to many. According to the Bureau of Labor Statistics, U.S. Department of Labor, The Economics Daily, 57.2 percent of people aged 15 and older in the United States spent time preparing food and drink on an average day in 2022, with the average time spent being just under an hour per day (53 minutes). While some people may prefer quick and easy recipes, others might enjoy more elaborate dishes that require extended preparation. Understanding how these differences impact the perception of a recipe’s quality is crucial. This project investigates the relationship between cooking time and the rating of recipes.

We aim to explore whether the time invested in cooking a recipe influences its rating, as rated by users. To achieve this, we analyze a dataset consisting of recipes and their corresponding ratings posted on food.com since 2008. This dataset, originally compiled for the recommender system research paper, Generating Personalized Recipes from Historical User Preferences by Majumder et al., provides a rich source of information to uncover patterns and insights into culinary preferences. We wonder if users tend to favor recipes that are quick to prepare or if they appreciate the intricacies of more time-consuming dishes. By investigating this relationship, we aim to shed light on the factors that contribute to a recipe’s popularity and help home cooks better understand how cooking time may affect the enjoyment of their culinary creations.

The dataset `recipe`, contains 83,782 rows, each representing a unique recipe, with the following 10 columns:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID (unique to each recipe)                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                                                                       |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The dataset `interactions`, contains 731,927 rows, each representing a user interaction (review) with a specific recipe. The columns included are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

**Given the datasets, we are investigating whether people rate recipes that take less than 50 minutes to prepare and those that take over 50 minutes to prepare on the same scale. To facilitate the investigation of our question, we performed several data cleaning steps and transformations:

## Data Cleaning and Exploratory Data Analysis

To make our analysis of the dataset more efficient and convenient, we conducted the following data cleaning steps.

1. Left merge the recipes and interactions datasets on id and recipe_id.

   - This step helps match the unique recipes with their rating and review.

1. Check data types of all the columns.

   - This step helps us evaluate what data cleaning steps are appropriate for the dataset and if we need to conduct data type conversion.
   - | Column             | Description |
     | :----------------- | :---------- |
     | `'name'`           | object      |
     | `'id'`             | int64       |
     | `'minutes'`        | int64       |
     | `'contributor_id'` | int64       |
     | `'submitted'`      | object      |
     | `'tags'`           | object      |
     | `'nutrition'`      | object      |
     | `'n_steps'`        | int64       |
     | `'steps'`          | object      |
     | `'description'`    | object      |
     | `'ingredients'`    | object      |
     | `'n_ingredients'`  | int64       |
     | `'user_id'`        | float64     |
     | `'recipe_id'`      | float64     |
     | `'date'`           | object      |
     | `'rating'`         | float64     |
     | `'review'`         | object      |

1. Fill all ratings of 0 with `np.nan`.

   - Ratings are typically given on a scale from 1 to 5, with 1 being the lowest and 5 being the highest. Therefore, a rating of 0 is likely indicative of a missing or invalid value rather than an actual rating. To prevent any bias in our analysis, we will treat these zeros as missing values and replace them with `np.nan`.

1. Add column `'average_rating'` containing average rating per recipe.

   - Each recipe can receive multiple ratings from different users. By calculating the average rating for each recipe, we obtain a more comprehensive and reliable measure of its overall quality. This approach smooths out individual biases and provides a clearer picture of how well the recipe is generally received.

1. Convert submitted and date to datetime.

   - These two columns are both stored as objects initially, so we converted them into datetime to allow us conduct analysis on trends over time if needed.

1. Add column `average_minutes` containing average preperation time per recipe.

   - First, the cooking time ('minutes' column) is converted from integer to float to ensure consistency in data types. Next, the average cooking time per recipe is calculated, generating a Series where each entry represents the mean cooking time for a specific recipe. This Series is then renamed to 'average_minutes' and merged back into the original dataset.
   - By averaging the cooking times, we gain a more accurate and comprehensive understanding of the typical preparation duration for each recipe, accommodating variations in reported times and smoothing out any anomalies.

1. Add `above_50min` indicating whether the average preparation time is above 50 minutes.

   - The column `above_50min` is a boolean field that checks if the average preparation time ('average_minutes') for each recipe exceeds 50 minutes. This step categorizes the recipes into two groups: recipes that are quick to prepare just under one hour and those that require more time. This classification allows us to analyze and compare the ratings of recipes based on their preparation time, providing insights into whether longer or shorter cooking times have an impact on user ratings.
   - We decided on the threshold since 50 min is just below 1 hour which is a good indicator of whether the entire cooking process will be under one hour. When we excluded the outliers and plot the distribution of `average_minutes`, we saw that approximately half of the recipe was under 50 min and half of the recipe was over 50 min (see Univariant analysis). Overall, 160036 recipes have preperation time over 50 min and 74392 recipes have preperation time under 50 min.


#### Result
Here are all the columns of the cleaned df.

| Column                  | Data Type      |
| :---------------------- | :------------- |
| `'name'`                | object         |
| `'id'`                  | int64          |
| `'minutes'`             | int64          |
| `'contributor_id'`      | int64          |
| `'submitted'`           | datetime64[ns] |
| `'tags'`                | object         |
| `'nutrition'`           | object         |
| `'n_steps'`             | int64          |
| `'steps'`               | object         |
| `'description'`         | object         |
| `'ingredients'`         | object         |
| `'n_ingredients'`       | int64          |
| `'user_id'`             | float64        |
| `'recipe_id'`           | float64        |
| `'date'`                | datetime64[ns] |
| `'rating'`              | float64        |
| `'review'`              | object         |
| `'average rating'`      | float64        |
| `'average_minutes'`     | float64        |
| `'above_50min'`         | bool           |

Our cleaned dataframe ended up with 234429 rows and 20 columns. Here are the first 5 rows of ~unique recipes of our cleaned dataframe for illustration. Since there is a lot of columns for the merged dataframe, we selected the columns that are most relevant to our questions for display.

| name                                 |     id |   minutes | submitted           |   rating |   average rating | average_minutes| above_50min  |
|:-------------------------------------|-------:|----------:|:--------------------|---------:|-----------------:|---------------:|:-------------|
| 1 brownies in the world    best ever | 333281 |        40 | 2008-10-27 00:00:00 |        4 |              4.0 |           40.0 | False        |
| 1 in canada chocolate chip cookies   | 453467 |        45 | 2011-04-11 00:00:00 |        5 |              5.0 |           45.0 | False        |
| 412 broccoli casserole               | 306168 |        40 | 2008-05-30 00:00:00 |        5 |              5.0 |           40.0 | False        |
| millionaire pound cake               | 286009 |       120 | 2008-02-12 00:00:00 |        5 |              5.0 |          120.0 | True         |
| 2000 meatloaf                        | 475785 |        90 | 2012-03-06 00:00:00 |        5 |              5.0 |           90.0 | True         |



### Univariate Analysis
#### Problem Identification 
Prediction problem:  Predict the rating of recipes using multiclass classification.
Response variable: Rating. When people choose recipes, they usually select based on ratings because it's the easiest way to visualize. 
Metrics: accuracy because accuracy consideres all errors equally, in this study, false positive and false negative contributes equally to the prediction.
Features: number of ingredients, minutes were quantitative values used in this study. Recipes with more ingredients may contribute to better taste and taking less time to cook save time in cooking which should result in high rating 


