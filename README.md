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
| `ingredients`    | The ingredients used in the recipe                                                               |
| `n_ingredients`  | The total number of ingredients used in the recipe                                               |

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


## Data Cleaning and Exploratory Data Analysis

**Data Cleaning**:  

1. In order to prepare the data for our analysis, we first left merged the recipes and reviews on the recipes dataset to create one complete dataset, named `merged`. 
2. We then filled all ratings of 0 with `np.Nan` because there is no option to rate a recipe as a 0, so a 0 value for the rating implies that the reviewer did not leave a rating for this recipe. Therefore, it makes more sense to change it to `np.Nan` so that the 0 values did not skew our later calculations for the average ratings for the recipe.
3. Next, we dropped all of the columns we did not believe were relevant for our analysis. These included `contributor_id` `submitted`, `n_steps`, `n_ingredients`, `user_id`, `recipe_id`, `date`, `review`. Since we were focusing on cuisines in particular, we did not think any of these columns were relevant. 
4. After removing irrelevant columns, we calculated the average rating for each recipe. To do this, we grouped the dataset by the recipe ID (`id`) and took the mean of the ratings for each recipe. We then added this average rating back to the dataset as a new column, `average_rating`.
5. The `nutrition` column in the dataset was originally stored as a string representation of a list containing various nutritional values (e.g., `[calories, total fat, sugar, sodium, protein, saturated fat, carbohydrates]`). To make these values usable for analysis, we converted this string into an actual list using `ast.literal_eval` and then split it into individual columns for each nutritional component. This created new columns such as `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, and `carbohydrates`.
6. We then calculated a custom `health_rating` for each recipe. The health rating was computed based on the nutritional values of the recipes, with higher calories, saturated fat, sugar, sodium, and carbohydrates reducing the score, while higher protein content increased the score. In our equation, we divided each of these nutritional values by the mean of the nutritional value across all fo the recipes in the dataset (for e.g: calories was divided by the mean calories for all recipes which was added to total fat divded by the mean of all total fat values) This normalization ensured that recipes with healthier nutritional profiles received higher scores.
7. The `tags` column, which contained various descriptive tags about each recipe, was initially stored as a string representation of a list. We converted it into an actual list format using `ast.literal_eval` to enable easier analysis. We then cross-referenced the tags with a predefined list of cuisines stored in a separate CSV file. Using this, we created a new column, `cuisines`, which contained only the relevant cuisine tags for each recipe.
8. Then, we dropped columns that were no longer needed for our analysis, such as the original `nutrition` column, individual nutritional components (`calories`, `total_fat`, etc.), and `tags`. Additionally, we removed rows where the `cuisines` column was empty or missing values to ensure that our dataset only included relevant and complete data for analysis. Ultimately, this left us with 8 columns and 72,240 rows. 
9. Finally, we groupby for `name` so we had unique recipes with their associated cuisines in our dataset, leaving us with 25,797 rows, or in other words, 25,797 unique recipes.

By the end of this cleaning process, the dataset was reduced to include only the essential columns required for our analysis: `minutes`, `description`, `steps`, `ingredients`, `average_rating`, `health_rating`, and `cuisines`. This cleaned dataset provided a strong foundation for conducting exploratory data analysis and building predictive models. The head of our final cleaned `merged` dataframe is shown below:

(For better readability, the outputs are truncated)

|   minutes | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | description                                                                                                                                                                                                                                                                                                                                                                                      | ingredients                                                                                                                                                                                                                      |   average_rating |   health_rating | cuisines                            |
|----------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------:|----------------:|:------------------------------------|
|         5 | ['rinse martini glass with extra dry vermouth' '...']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | based on a recipe from ray foley's the ultimate little martini book...                                                                                                                                                                                                                                                                                        | ['dry vermouth', 'vodka', 'gin', 'lillet blanc']                                                                                                                                                                                 |                5 |         9.64705 | ['european']                        |
|        25 | ['combine cinnamon , vanilla , cornstarch , peach nectar , and pineapple...']                                                                                   | heart healthy                                                                                                                                                                                                                                                                                                                                                                                    | ['cinnamon', 'vanilla extract', 'cornstarch', '...'] |                4 |         6.31694 | ['african']                         |
|        15 | ['preheat oven to 350 degrees , and line a baking tray with baking parchment', '...']                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | i came across these cookies once by accident... | ['natural-style peanut butter', 'sugar substitute', 'ground flax seeds']                                                                                                                                                         |                2 |         6.71192 | ['canadian']                        |
|           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |        50 | ['mix batter ingredients in a 9-1 / 2-inch deep dish pie plate that', '...']                                                                                                                                                                                                                                                                                                                                                                                                                                  | a quick recipe with only 20 minutes preparation using fleishchmann's rapid rise yeast!                                                                                                                                                                                                                                                                                                           | ['cooking spray', 'all-purpose flour', 'fast rising yeast', '...']                                      |                5 |         7.94085 | ['european', 'american', 'italian'] |
|        45 | ['pre-heat oven the 350 degrees f', '...'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies...                                                                                                                                                           | ['white sugar', 'brown sugar', 'salt', '...']                                                                      |                5 |         1.53134 | ['canadian']                        |

