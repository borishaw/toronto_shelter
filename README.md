# Data Sources

The data in this project comes from 3 different sources. 

The primary data is from the **Daily Shelter Occupancy** data from **Open Data Toronto**, a open data portal maitained by the City of Toronto. The data set contains various daily information about a shelter program since 2017-01-01 including the *date*, *address*, *postal code*, *occupancy*, *capacity*, and etc. And the data is updated daily.

The first supplementary data is the historical weather data from Environment Canada. It contains corresponding daily weather information taken from the weather station located at *43°40'00.000" N* and *79°24'00.000" W* (near University of Toronto St. George Campus). The data set contains various weather indicators including *date*, *daily max*, *daily min*, *precipitation*, and etc.

The second supplementary data is from Google Maps Geocoding API. It is used to retrieve geographical information by using the the postal codes of the shelters. The information contains *latitude*, *longitude*, *sublocality (Old Toronto, North York, Scarborough, and etc.)*, and etc.

# Objective

The objective of this project is to build a prediction model that can predict if a shelter will be full based on the charateristics of a shelter program combining with corresponding weather data.