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

**Given the datasets, we are investigating whether people rate sugary recipes and the non-sugary recipes on the same scale.** To facilitate the investigation of our question, we separated the values in the `'nutrition'` columns into the corresponding columns,`'calories (#)'`, `'total fat (PDV)'`, `'sugar (PDV)'`, etc. PDV, or percent daily value shows how much a nutrient in a serving of food contributes to a total daily diet. Moreover, we calculated the proportion of sugar in terms of calories out of the total calories of a given recipe and stored the information in a new column, `'prop_sugar'`. because sugary in here will be referring to the recipes with value `'prop_sugar'` higher than the average `'prop_sugar'`. The most relevant columns to answer our question are `'calories(#)'`, `'sugar (PDV)'`, `'prop_sugar'`, described above, `'rating'`, which is the rating that user gave on a recipe, and `'average rating'`, which are the average of the ratings on each unique recipes.

By seeking an answer to our question, we would have an insight on people’s preference on sugary recipes, which could help contributors on Food.com revise and improve their recipes to align with the public’s interests. In addition, the new pieces of information could lead to future work on diving deeper into how much awareness people have on the negative health effects of sweets.

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

1. Convert cooking time from int to float, then find the average cooking time per recipe, and merge this data back into the recipes dataset.

   - First, the cooking time ('minutes' column) is converted from integer to float to ensure consistency in data types. Next, the average cooking time per recipe is calculated, generating a Series where each entry represents the mean cooking time for a specific recipe. This Series is then renamed to 'average_minutes' and merged back into the original dataset.
   - By averaging the cooking times, we gain a more accurate and comprehensive understanding of the typical preparation duration for each recipe, accommodating variations in reported times and smoothing out any anomalies.

1. Convert submitted and date to datetime.

   - These two columns are both stored as objects initially, so we converted them into datetime to allow us conduct analysis on trends over time if needed.

1. Add `above_50min` indicating whether the average cooking time is above 50 minutes.

   - The column `above_50min` is a boolean field that checks if the average cooking time ('average_minutes') for each recipe exceeds 50 minutes. This step categorizes the recipes into two groups: those that take more than 50 minutes to prepare and those that take less. This classification allows us to analyze and compare the ratings of recipes based on their preparation time, providing insights into whether longer or shorter cooking times have an impact on user ratings.


