#Pokémon GO Trainer Stat Model

**Description:
This repository contains a Python script that models the relationship between a trainer's level and various in-game statistics in Pokémon GO, such as battles won, distance walked, and Pokémon caught from a sample group of trainers I have added playing the game. Using linear regression, this model predicts these stats based on trainer levels, offering insights into how stats might evolve as players level up.

**Key Features:
Data Cleaning: Utilizes the Interquartile Range (IQR) method to remove outliers from the dataset, ensuring more accurate model training.
Data Visualization: Includes scatter plots to visually inspect the relationship between trainer level and various stats post-outlier removal.
Modeling: Implements three separate linear regression models for battles won, distance walked, and Pokémon caught, each trained on cleaned data.
Model Evaluation: Assesses model performance with R-squared scores and Mean Squared Error (MSE) metrics.
Predictions: Offers predictions for a new level (35 in this case) and generates predictions for levels 35 to 50 with variance visualization.