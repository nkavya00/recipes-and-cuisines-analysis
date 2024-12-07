# Analyzing Recipes and Cuisines 
#### Analysis of cuisines and other factors from a large recipes dataset


## Introduction

Cooking is not just a necessity but a universal language that bridges cultures and traditions. From haphazardly learning to make mac n' cheese as an underfunded college student to making large Thanksgiving dinners for the whole family, the art of cooking has always bound us all together. In the age of globalization, understanding the relationships between cuisines and recipe components (e.g., ingredients, preparation time, health considerations) can reveal fascinating insights into how food connects us all. This project explores a dataset of recipes to answer one central question:

**"Can we predict the cuisine of a dish based on its ingredients and preparation details?"**

Understanding this question has implications for multiple audiences:
- **For home cooks**, it can help suggest dishes based on available ingredients.
- **For culinary enthusiasts**, it reveals patterns in global cuisines.
- **For researchers and developers**, it supports the creation of smarter recommendation systems for food-related applications.

---

### Dataset Overview

The datasets we used contain a subset of recipes and user reviews collected from Food.com, including only recipes and reviews postedsince 2008, given that the original dataset was quite large. These datasets offer a comprehensive view of recipe characteristics and user preferences, making it ideal for our exploration.  

**Key statistics about the datasets**:  
- **RAW_recipes.csv**: Contains recipes and certain notable characteristics such as the ingredients, nutritional value, and time to make. The original dataframe contains 82, 782 rows and 12 columns.
- **RAW_interactions.csv**: Contains reviews and ratings for the recipes in RAW_recipes.csv. The original dataframe contains 731,927 rows and 5 columns. 

In our exploration, we merged both datasets to create one dataset with the recipes and their respective reviews.

**Relevant columns from each of the datasets**:

For the Recipes Dataset:

| Column           | Description                                                                                       |
|------------------|---------------------------------------------------------------------------------------------------|
| `name`           | Recipe name                                                                                      |
| `id`             | Recipe ID                                                                                        |
| `minutes`        | Minutes to prepare recipe                                                                        |
| `contributor_id` | User ID who submitted this recipe                                                                |
| `submitted`      | Date recipe was submitted                                                                        |
| `tags`           | Food.com tags for recipe                                                                         |
| `nutrition`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV),     |
|                  | protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for "percentage of daily    |
|                  | value"                                                                                           |
| `n_steps`        | Number of steps in recipe                                                                        |
| `steps`          | Text for recipe steps, in order                                                                  |
| `description`    | User-provided description                                                                        |

For the Reviews/Ratings Dataset:

| Column       | Description           |
|--------------|-----------------------|
| `user_id`    | User ID               |
| `recipe_id`  | Recipe ID             |
| `date`       | Date of interaction   |
| `rating`     | Rating given          |
| `review`     | Review text           |

---

### Why Should You Care?

The dataset and the central question are highly relevant for anyone interested in food, technology, or cultural exploration. Imagine a system that can recommend dishes from diverse cuisines based on the ingredients in your fridge! Beyond individual convenience, we hope our analyis contributes to cross-cultural appreciation and dietary innovation.

Stay tuned as we explore this dataset and uncover the secrets of global cuisines through data analysis and machine learning!
