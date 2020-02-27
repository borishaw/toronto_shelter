# Objective

The objective of this project is to build a prediction model that can predict if a shelter will be full based on the charateristics of a shelter program combining with corresponding weather data.

# Data Sources

The data in this project comes from 3 different sources. 

The primary data is from the **Daily Shelter Occupancy** data from **Open Data Toronto**, a open data portal maitained by the City of Toronto. The data set contains various daily information about a shelter program since 2017-01-01 including the *date*, *address*, *postal code*, *occupancy*, *capacity*, and etc. And the data is updated daily.

The first supplementary data is the historical weather data from Environment Canada. It contains corresponding daily weather information taken from the weather station located at *43°40'00.000" N* and *79°24'00.000" W* (near University of Toronto St. George Campus). The data set contains various weather indicators including *date*, *daily max*, *daily min*, *precipitation*, and etc.

The second supplementary data is from Google Maps Geocoding API. It is used to retrieve geographical information by using the the postal codes of the shelters. The information contains *latitude*, *longitude*, *sublocality (Old Toronto, North York, Scarborough, and etc.)*, and etc.

# Process
## Data Cleaning

As expected, the data did not come in a usable format. Therefore, several cleaning measures were taken to ensure the usability of the data.

### Shelter Data
<ul>
    <li>Dropping "id" column, which is unique to a row and does not contribute to the overall data structure.</li>
    <li>Filling out capacity values with maximum value of occupancy where capacity is 0 or null and occupancy is not.</li>
    <li>Dropping rows with null or zero capacity.</li>
    <li>Converting date format from datetime to date. (ex. "2019-04-20T00:00:00" -> "2019-04-20")</li>
    <li>Filling out rows with empty postal codes by searching Google Maps with corresponding addresses.</li>
    <li>Excluding shelter programs not related to homelessness. (i.e. programs for new immigrant & refugee settlement).</li>
</ul>

### Weather Data
<ul>
    <li>Keeping only <i>daily mean</i> and <i>precipitation</i></li>
    <li>Removing rows where <i>daily mean</i> or <i>precipitation</i> are null.</li>
</ul>

## Feature Engineering
There are additional features were able to be derived from existing features. 
<ul>
    <li><b>Daylight Length</b>: An external Python package <i>Astral</i> was used to calculate daylight length of every date in the data sets.</li>
    <li><b>Day of Week</b>: The day of week is deducted from the date of each row by using the Python built-in datetime package.</li>
    <li><b>Occupancy Rate</b>: The occupancy rate of each row can be calculated by dividing the occupancy by capacity.</li>
    <li><b>Occupancy Level</b>: This is a categorical value the models try to predict. Its value is set to be 1 if a row with an occupancy rate equals to or above 100%, otherwise it is set to 0.</li>
</ul>

## Time Series vs. Non Time Series
The original plan was to treat the data set as a time series problem since the data is ordered chronologically. However, after extensive feature engineering, the data set contains enough features (daily temperature, daylight length, etc.) to be treated a non-time series problem. When dealing with relatively large amount of features, it is more efficient to take the non-time series approach since the autoregression itself is not very accurate in predicting long term result let alone combining with other features.

## Model Fitting and Outcome
This is a **two-class** (whether a shelter program is full or not) classifying problem. 

The **y** is the **occupancy level** (either with a value of 1 or 0).
The **X** is every numeric feature other than *sector*, *sublocality*, and *week day*. The latter three were turned into dummy features. The train/test data set is randomly split into 70/30.

The distribution of **y** values is 61% of 1 and 39% of 0, which means that 61% of the records with full capacity and 39% with non-full.

The first model fitting was done on a **Logistic Regression** with default hyperparameter setting. The accuracy of this model is **64%** (which is only fractionally higher than pure chance) and and F1 score of **0.74**. This model is used as a baseline for comparison with other models.

The second model fitting was done on an **SVM** model with standard scaling. It is able to achieve a accuracy of **66%** and F1 score of **0.75**, which is a minor improvement.

Later a PCA analysis was done on the scaled data set. It is discoverd that the first **15** pricipal components cover 90% of the significance.

Among all the models being fitted with **Grid Search**, the best model is a **Random Forest** model with a number of trees of **10** and max depth of **14**, with data decomposed with **5** principal components, yields the best result. It is able to achieve an **F1** score of **0.80** and accuracy rate of **74%**.

# Future Plans
## More model fittings
Although the Random Forest model fitted is able to achieve an accuracy rate of 74% (which is higher than expected), only 3 different types of models were fitted. It is possible to fit other classifier models to find better accuracies. Instead of classifying, the data set can also treated as a regression problem (to predict the number of occupancy).

## Pipeline
Since the data is updated daily, it is possible to make it a pipeline application to retrieve daily data, to validate against the prediction made before, and to refit models with the latest data. 

## Data Visualization
The data can be visualized as a time series heat map by showing the occupancy rate as time elapses.