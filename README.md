# Final-Project-Statistical-Modelling-with-Python

## Project/Goals
The goal of the project is to connect to CityBike, Yelp, and Foursquare APIs
in order to extract data from a location of our choosing and then to demonstrate a relationship between 
the number of bikes in a particular location and characteristics of the POIs of that location.
This is done through using an OLS multivariable regression model.
The logistic regression is transformed into a classification model using the data gathered.

## Process

# city_bikes
# A CityBikes API was used to access public bike systems in Toronto.
### CityBike url: http://api.citybik.es/v2/
A request was sent and data was collected and parsed through.
A lambda function was used to collect the latitude and longitude and put the values
together into a new column, 'll', to be used for making Foursquare API requests.
The table was saved to a file, 'bikes.csv', with the columns
['empty_slots', 'free_bikes', 'name', 'latitude', 'longitude', 'll'].

# AuthenticateYelpAPI
The Yelp and Foursquare API requrests were done in separate files.
The Yelp request was done in the file 'AuthenticateYelpAPI.ipynb'.
First a class was made called 'yelp_api_request':
'''
yelp_api_request(API_KEY, SEARCH_PATH, DEFAULT_TERM, DEFAULT_LOCATION, SEARCH_LIMIT, LATITUDE, LONGITUDE)
'''
Which was made to take the API key, the search path, ('/v3/businesses/search'), the default term 'park',
the location, 'Toronto', and the search limit was set to 10.
The API key was set by the environment variable, and a loop was constructed to cycle through the latitude and longitude.
The bikes.csv was imported into a dataframe to provide the latitude and longitude locations in the loop.
The yelp_api_request used it's search function to realize an object 'response' using the coordinates provided,
which were then put into a new dataframe.
All the data was then parsed through and the necessary data was stored into a new file, 'yelp_park_data.csv'.

# The yelp_foursquare_EDA file
Similar to the AuthenticateYelpAPI file, another class was made to request data from the Foursquare API.
The bikes.csv was stored into a dataframe to be used by the foursquare_api_request class in another loop.
'''
foursquare_api_request(foursquare_id, CATEGORYID, SEARCH_LIMIT, ll)
'''
The class has fewer variables needed to be input.
The foursquare_id was the API key taken from the environment variable.
CATEGORYID = 16000, which was 'Landmarks and Outdoors' for Foursquare.
SEARCH_LIMIT was set to 10
ll was taken from the bikes dataframe to be used to the loop.
The data extracted was saved into the file 'foursquare_outdoor_data.json'.
The data was parsed through and some nested objects in the categories column was extracted 
and put into a new table called 'fs_id_cat_df.csv'.  This table now has all the catagory names
extracted and fsq_id as a foreign key to merge with 'foursquare_outdoor_data.json'.
The catagorical data from Foursquare and the parent database were joined
and duplicate rows and columns were filtered out.
The data was saved into "fs_outdoor_data_with_categories".

# Comparing Results of APIs
Instead of a monthly allowance, like Foursquare, Yelp provides a daily allowance.
An allowance which refreshes each day may be useful if a query does not return information
which one would want, or something goes wrong with data collection.  In this case,
the timeframe to review errors and collect new information could be done the following day.
However, Foursquare did allow for more data collection than Yelp provided, and returned more
locations given similar categories.  Yelp provided fewer categoreies and the access limit was
also reached.
Yelp had some good information concerning park review and rating scores for the POI set,
but in terms of the categorical information, (the specific categoreies in that location name),
more results were returned for Foursquare.  So while the quantity of information is greater 
with Foursquare(F.S.), the quality of information can be slightly better with Yelp (Yl).
E.g. F.S. can provide more categoreis like "Park", "Hiking Trail", for one location, while
Yl would only provide "Park", but Yl also provides rating, review_count, image_url/url/ etc.
More categorical location information was gathered for Foursquare than for Yelp.

Since the access limit was reached extracting outdoor parks and locations, the top 10 restaurants
were not able to be rated.  Instead, the top 10 outdoor locations was identified, 
and review_count was also included in sort_values() because because higher reviews
are more accurate with a larger sample size.

# joining_data
Before joining dataframes, column values were selected in the yelp_foursquare_EDA file, and
duplicate rows were also filtered out in the yelp_foursquare_EDA file.

Several tables were made using the already constructed files,
These tables are displayed at the beginning of the joining_data file.

The dataframe from 'bikes.csv' used citybike_index as it's primary key.
Reset index was used in the bike dataframe in order to be used as a unique primary key called 'citybike_index'.
When importing data from the Yelp and Foursquare API, on top of the latitude and longitude data
provided, the citybike_index was also stored into each file as a foreign key.
A new primary key is constructed for the 'fs_id_cat_df.csv' table, 
while the foreign key, 'fsq_id', connects to foursquare_outdoor_data.json.
The duplicate rows are removed so now fsq_id is used as the primary key IN THE 'foursquare_outdoor_data.json' dataframe,
and citybike_index is the foreign key to the bikes_df.
The categorical data in 'fs_id_cat_df.csv' was merged with 'foursquare_outdoor_data.json' on the fsq_id key, 
and and duplicate rows and columns were filtered out, with citybike_index as the foreign key to bike_df,
then stored into a file, 'fs_outdoor_data_with_categories.csv'.
The yp_index is now the primary key of the yelp_park_df and citybike_index is the foreign key for bike_df.

A database was created called "LHL-Statistical-Modelling-with-Python.db" and stored in the data folder.
The tables previously mentioned were successfully stored into the database.

Several tables were joined to be saved into the file 'bike_yelp_fs_outdoor_db.csv',
which contained the columns citybike_index, empty_slots, free_bikes, review_count, 
rating, category_name, and distance.

# model_building
The newly created file contained all the data gathered to be used in in the model building process.
NULL values were identified and determined how they would impact the final results of the regression model.
The top three categories most counted near bikes were identified.
The distances to the locations were plotted, outliers were removed, and histograms of the average distance
to the locations became more normalized.  
A count of the number of the top 3 locations near bikes were also plotted in histograms, and some skewed 
distributions were normalized by taking the natural logarithm.
The dependent variable, number of bikes was also skewed, and was then normalized by taking the natural logarithm.
A join plot comparing review count to rating was constructed, and also showed a skewed distribution.
Taking the natural logarithm of review count normalized the data more but both distributions were still skewed.
Lambda functions were applied to the dataframe to more normalize the skewed distributions by taking the natural logarithm.
A correlation heat map was constructed.
A scatterplot grid was constructed.

The results of the regression model are discussed below.

## Results

The top three categories most counted near bikes were identified to be number of parks, number of playgrounds,
and number of sports and recreation areas.
The distances to the locations were plotted and saved in the picture 'distance_to_top_three_unfiltered'.
The outliers were removed, and histograms of the average distance to the locations became more normalized.
The normalized plots were saved in the picture 'distance_to_top_three_normal'.
The number of the top three locations found near bikes were also plotted in a histogram, and saved to the picture
'count_of_top_three_unfiltered'.
The count of number of playgrounds and number of sports and recreation areas were more normalized by taking the
natural logarithm, and save to the picture 'count_of_top_three_normal'.

The dependent variable, number of bikes was also skewed when taking a histogram, and the distribution was saved to the
picture 'dep_var_bikes_not_norm'.
The distribution was then normalized by taking the natural logarithm, 
and passed the Shapiro-Wilk test for normalization.
The distribution was save to the picture 'dep_var_bikes_norm'.

A join plot comparing review count to rating was constructed, and also showed a skewed distribution,
and was saved to the picture 'rc_rat_joinplot'.
Review count was skewed to zero but also contained very large outliers.
Rating was skewed with a median of around 4.
The natural logarithm of the number of reviews given was taken and this can be seen in the picture 'num_rev_log_rating'.

All the independent and dependent variable data gathered in a dataframe was reviewed using the .describe() function.
Nothing looked too out of place after normalization and the descripitive statistics are saved in the picture, 'var_desc_stat'.

A correlation heat map was constructed and saved to the picture 'corr_heatmap'.
The heatmap shows that the highest correlation is between the distance to the park and the distance
to sports and recreation areas.  The lowest negative correlation was between number of bikes and number of empty slots (for the bikes).
There was also a relatively larger negative correlation between number of parks and playgrounds and number of reviews.
A scatterplot grid was created and saved to the file 'scatterplot_grid'.
This plot showed relatively more normalized distribution of data points around the line of best fit for variables which
had greater correlation.

There are three OLS Regression Results.
Two results have different combinations of variables and are very similar but there are some minor differences.
The first OLS regression contained all the potential independent variables.  
med_review had a passing p-value but was not included because it was negatively correlated with num_parks,
so due to multicollinearity when combined, med_review had too large a p-value.
Those with the highest p-values were discarded one-by-one, and different combinations were
attempted until the variable p-values were all below the confidence interval 0.05. 
Two OLS results have variables with p-values lower than 0.05, and a Prob (F-statistic) approaching zero, so the regression coefficients are not zero.
A regression model with empty_slots, num_parks, and num_plygrd as variables was selected for three factors:
	- The adjusted R Squared value was greater with 0.34 so more of the dependent variable is explained by changes in the independent variables.
	- The skew was closer to 0, so the data is more symmetric.
	- The Durbin-Watson measurement is closer to 2, so the data has less heteroskedasticity.
The regression results selected are saved into the picture 'OLS_reg_results'.

The variables were tested so that they had a linear relationship through removing outliers and normalizing the data.
The heatmap checked to make sure there was lower multicollinearity.
The scatterplot showed that the more correlated data had a more normalized distribution of data points around the line of best fit.
The OLS regression results which had less heteroskedasticity was chosen.

The linear regression model looks like:
'''
ln(y) = b0 - b1(x1) - b2(x2) - b3(ln(x3))
'''
with b0 to b3 being the regression coefficients,
y = the number of bikes
x1 = empty bike slots
x2 = number of parks
x3 = number of playgrounds
Solving for y: 
'''
y = [exp(b0 - b1(x1) - b2(x2))]/(x3^b3)
'''
So the function takes on a Poisson GLM.
The negative coefficients mean that as the variables increase, the number of free bikes available decreases.


The logistic regression is transformed into a classification model to determine if the variables gathered 
can be used to identify if a park is in the vicinity.
This is because the highest count in categories was 'Park', so instead of looking at the number of bikes
in the regression model, a new column can be changed into a boolean to determine if a location is a park or not.
In the logit classification model, the LLR p-value approaches zero, so including all the variables improves model fit, 
compared to the intercept-only null model.  All the p-values are below the confidence interval of 0.05 and are statistically significant.
The variables are empty_slots, free_bikes, review_count, rating, and distance, with rating being the only positive coefficient.
The Pseudo R Squared value of 0.008 is low, which indicates the likelihood of a fitted model to be low.
The results were saved into the picture 'MNLogit_reg_results'.


## Challenges 
Initially constructing the classes to extract the data using an API request proved difficult but ended up working in the end.
The challenge was sorting through what data from each sites' informaiton to include in the functions inside the class and to get
them to work together.
Storing the initial Yelp data into a CSV file was a mistake because some nested objects in the columns were then saved as strings.
I was lucky to get all the data I needed from them when I saved the data into the initial file.
I made sure to save the Foursquare data into a JSON file in order to extract any more nested objects.

The most time was spent on parsing through the data to identify which information to gather and which tables to put it in.
And to create keys to know how to fully join all the necessary informaiton.

One of the main challenges with the model was transforming the dependent and independent variables into a normal distribution.
Outliers, or even a high amount of zeros in the distributions made it difficult to normalize even after taking the natural logarithm,
or taking the natural logarithm of the maximum value in the range minus the value of the datapoint.

After removing all other variables, the adjusted r^2 increased because, 
'''Adjusted R Squared = 1 – (1 – R2) * ((n – 1) / (n – k – 1))''',
if ((n – 1) / (n – k – 1)) = A,
Adjusted R Squared(ARS) = R2 + 1/A - 1
where Where:
    n – Number of points in the data set.
    k – Number of independent variables in the model.
Removing the other variables reduces k from the denominator, which in turn increases the value of A,
so for an ARS increase to make sense, the residual sum of squares would have to decrease, which increases
the R Squared value to compensate for the increase in A.
Figuring out which variables to include based on p-value and R Squared took some trial and error.


## Future Goals
If I had more time I would not make the review counts and review scores
independent variables, and instead normalize them into a weighted sum column.
This way the magnitude of the score will also be proportional to the number
of reviews given, (e.g. a 5 star rating with more reviews than another 5 star
rating will have a higher magnitude).

More work should be done to better prepare the data for linear regression, and to better normalize the data.

There wasn't enought time to construct a confusion matrix for the MNLogit Regression classification model.

I would also look for more POI and include locations where the count was highest when adding them to a
regression model.
