# Investigation on the Relationship Between Cooking Time and Rating of Recipes

Authors: Ellen Wrightsman & Erin Li

## Overview

This data science project for DSC80 at UCSD investigates how the cooking time of a recipe correlates with its user ratings.

## Introduction

Food is an integral part of our daily lives, and cooking is a cherished hobby that brings joy and satisfaction to many. According to the Bureau of Labor Statistics, U.S. Department of Labor, The Economics Daily, 57.2 percent of people aged 15 and older in the United States spent time preparing food and drink on an average day in 2022, with the average time spent being just under an hour per day (53 minutes). While some people may prefer quick and easy recipes, others might enjoy more elaborate dishes that require extended preparation. Understanding how these differences impact the perception of a recipe’s quality is crucial. This project investigates the relationship between cooking time and the rating of recipes.

We aim to explore whether the time invested in cooking a recipe influences its rating, as rated by users. To achieve this, we analyze a dataset consisting of recipes and their corresponding ratings posted on food.com since 2008. This dataset, originally compiled for the recommender system research paper, Generating Personalized Recipes from Historical User Preferences by Majumder et al., provides a rich source of information to uncover patterns and insights into culinary preferences. We wonder if users tend to favor recipes that are quick to prepare or if they appreciate the intricacies of more time-consuming dishes. By investigating this relationship, we aim to shed light on the factors that contribute to a recipe’s popularity and help home cooks better understand how cooking time may affect the enjoyment of their culinary creations.

The first dataset, `recipe`, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
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

The second dataset, `interactions`, contains 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |
