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

---

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

(For better readability, the outputs are truncated):


|   minutes | steps                                                 | description                                           | ingredients                                           |   average_rating |   health_rating | cuisines                    |
|----------:|:------------------------------------------------------|:------------------------------------------------------|:------------------------------------------------------|-----------------:|----------------:|:----------------------------|
|         5 | ['rinse martini glass with extra dry vermouth', 'a...] | based on a recipe from ray foley's the ultimate li... | ['dry vermouth', 'vodka', 'gin', 'lillet blanc']      |                5 |         9.64705 | european                    |
|        25 | ['combine cinnamon , vanilla , cornstarch , peach ...] | heart healthy                                         | ['cinnamon', 'vanilla extract', 'cornstarch', 'pea...] |                4 |         6.31694 | african                     |
|        15 | ['preheat oven to 350 degrees , and line a baking ...] | i came across these cookies once by accident, and ... | ['natural-style peanut butter', 'sugar substitute'...] |                2 |         6.71192 | canadian                    |
|        50 | ['mix batter ingredients in a 9-1 / 2-inch deep di...] | a quick recipe with only 20 minutes preparation us... | ['cooking spray', 'all-purpose flour', 'fast risin...] |                5 |         7.94085 | european, american, italian |
|        45 | ['pre-heat oven the 350 degrees f', 'in a mixing b...] | this is the recipe that we use at my school cafete... | ['white sugar', 'brown sugar', 'salt', 'margarine'...] |                5 |         1.53134 | canadian                    |


---

**Univariate Analysis**: 

First, I wanted to look at the distribution of cuisines, as is seen below.

<iframe
  src="assets/univar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From this graph, I can tell there is an imbalance in the dataset, with American and European cuisines heavily dominating (around 8000 entries each) while many other cuisines like Korean, Malaysian and Thai have fewer than 100 entries each. This imbalanced distribution suggests that while predicting cuisine from ingredients might be possible, my model would likely show bias towards American and European cuisines so I need to ensure I specifically address the class imbalance issue.

Next, since my model will be predicting the cuisine at least partially based on the ingredients, I want to see the distribution of the most common ingredients as well. 

## INSERT INGREDIENTS UNIVARIATE HERE 

Looking at the distribution of ingredients across recipes, I found that salt is overwhelmingly the most common ingredient with over 10,000 appearances, followed by a cluster of fundamental ingredients like olive oil, butter, onion, and garlic cloves each appearing in around 5,000 recipes. This pattern suggests that these basic ingredients form the foundation of cooking across many cuisines, which could make them less useful as distinguishing features for predicting specific cuisines, and might be something to look out for in creating my model.

---

**Bivariate Analysis**: 

Next, I conducted some bivariate analyses in order to examine relationships between cuisines and other variables in the dataset. First, I looked at if different cuisines had different health ratings, as this was a variable I was planning on training my model with. The graph of the relationship between cuisine and health rating is shown below:

## INSERT HEALTH RATING BIVARIATE HERE 

Examining the average health ratings across cuisines, I found that Pakistani and Turkish cuisines rank highest with scores above 7, while Irish cuisine ranks lowest at 4.19. Interestingly, many Asian cuisines like Japanese, Korean, and Chinese fall in the middle range around 6-7. From my definition and calculation of health rating, lower health ratings indicate healthier dishes, which tells me that some cuisines tend to have healthier dishes than others. Although this distribution is relatively balanced, there are differences between these cuisines that did prove useful in establishing my model. 

Next, I also wanted to look at preparation times for each cuisine - the graph is shown below:


## INSERT MINUTES BIVARIATE HERE 

From this plot, I can see German and Korean cuisines stand out as requiring significantly more preparation time, averaging around 270-280 minutes, while Filipino and Australian dishes are the quickest to prepare at under 50 minutes. This substantial time variation between cuisines (from 50 to 280 minutes) was an important feature for cuisine prediction, because it reflected fundamental differences in cooking techniques and complexity.

Finally, since I predicted that different cuisines would use different ingredients, I also wanted to make a graph to see the most common ingredients for different cuisines. 

## INSERT INGREDIENTS BIVARIATE HERE 

Add in description of graph here!!

---

**Interesting Aggregates**: 

Add in info here later!!!


**Imputation**: 

In our analysis we did not impute any values as all of our columns of interest already had all of the data we needed and had already been cleaned, and thus we felt as though we could just move on with our prediction.

## Framing a Prediction Problem

This is a multiclass classification problem where I aim to predict the cuisine category of a dish (e.g., American, European, Asian, etc.) using features that would be known before the cuisine is identified. My response variable is the `cuisines` column, which I chose because it directly addresses my research question of whether we can identify a dish's culinary origin based on its characteristics.
For model evaluation, I plan to use the F1-score rather than simple accuracy because:

1. My dataset is heavily imbalanced (as shown in my univariate analysis where American/European cuisines dominate)
2. F1-score provides a balanced measure of precision and recall, which is crucial when dealing with imbalanced classes
3. It will help ensure my model performs well across all cuisine types, not just the dominant ones

The features I will use for prediction include:

- Ingredients (as these define the core composition of the dish)
- Minutes (preparation time)
- Health rating (calculated from nutritional information)

I specifically excluded features like user ratings and reviews since these would only be available after a recipe is already categorized with its cuisine. This ensures my model only uses information that would be available at the "time of prediction" - when someone is trying to identify a cuisine based on a new recipe's characteristics.

## Baseline Model

My model uses a Random Forest Classifier with features that include both quantitative and categorical variables:

- Quantitative (1): `minutes` (preparation time)
- Nominal (1): `ingredients` (text data converted to numerical features using TF-IDF vectorization)

To handle these different types of features, I implemented a preprocessing pipeline that:

1. Applies StandardScaler to the 'minutes' feature to normalize the numerical data
2. Uses TF-IDF vectorization for the 'ingredients' feature, converting the text data into a numerical matrix with 5000 features
3. Addresses class imbalance using balanced class weights, which I computed based on the training data distribution

The model achieved an accuracy of 46.3% on the test set, with varying performance across different cuisines. Looking at the classification report:

- Some cuisines like Mexican (F1=0.69), Chinese (F1=0.65), and Indian (F1=0.64) show relatively good prediction performance
- Others like Canadian, Central-American, Hawaiian, and Malaysian have F1-scores of 0, likely due to limited representation in the training data
- The weighted average F1-score is 0.42, reflecting the challenge of predicting across highly imbalanced classes

I would not consider this current model "good" because:

1. The overall accuracy of 46.3% is relatively low for practical applications
2. The model completely fails to predict several cuisines
3. There's significant variation in performance across different cuisines, indicating bias towards better-represented classes

## Final Model
