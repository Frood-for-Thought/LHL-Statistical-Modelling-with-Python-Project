# Final-Project-Statistical-Modelling-with-Python

## Project/Goals
(fill in your description and goals here)

## Process

city_bikes
### A CityBikes API was used to access public bike systems systems in Toronto.
### CityBike url: http://api.citybik.es/v2/
A request was sent and data was collected and parsed through.
A lambda function was used to collect the latitude and longitude and put the values
together into a new column, 'll', to be used for making Foursquare API requests.
The table was saved to a file, 'bikes.csv', with the columns
['empty_slots', 'free_bikes', 'name', 'latitude', 'longitude', 'll'].

AuthenticateYelpAPI
The Yelp and Foursquare API requrests were done in separate files.
The Yelp request was done in the file 'AuthenticateYelpAPI.ipynb'.
First a class was made called 'yelp_api_request':
yelp_api_request(API_KEY, SEARCH_PATH, DEFAULT_TERM, DEFAULT_LOCATION, SEARCH_LIMIT, LATITUDE, LONGITUDE)
Which was made to take the API key, the search path, ('/v3/businesses/search'), the default term 'park',
the location, 'Toronto', and the search limit was set to 10.
The API key was set by the environment variable, and a loop was constructed to cycle through the latitude and longitude.
The bikes.csv was imported into a dataframe to provide the latitude and longitude locations in the loop.
The yelp_api_request used it's search function to realize an object 'response' using the coordinates provided,
which were then put into a new dataframe.
All the data was then parsed through and the necessary data was stored into a new file, 'yelp_park_data.csv'.

The yelp_foursquare_EDA file
Similar to the AuthenticateYelpAPI file, another class was made to request data from the Foursquare API.
The bikes.csv was stored into a dataframe to be used by the foursquare_api_request class in another loop.
foursquare_api_request(foursquare_id, CATEGORYID, SEARCH_LIMIT, ll) had fewer variables needed.
The foursquare_id was the API key taken from the environment variable.
CATEGORYID = 16000, which was 'Landmarks and Outdoors' for Foursquare.
SEARCH_LIMIT was set to 10
ll was taken from the bikes dataframe to be used to the loop.
The data extracted was saved into the file 'foursquare_outdoor_data.json'.
The data was parsed through and some nested objects in the categories column was extracted 
and put into a new table called 'fs_id_cat_df.csv'.  This table



Comparing Results
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

Joining data
Before joining dataframes, column values were selected in the yelp_foursquare_EDA file, and
duplicate rows were also filtered out in the yelp_foursquare_EDA file.


In this case the fsq_id can be used as the primary key IN THE 'foursquare_outdoor_data.json' file.
A new primary key should be constructed for 'fs_id_cat_df.csv'. 
The categorical data in 'fs_id_cat_df.csv' was merged with 'foursquare_outdoor_data.json' on the fsq_id key, 
and and duplicate rows and columns were filtered out, with citybike_index as the foreign key to bike_df.
The categorical data in 'fs_id_cat_df.csv' was merged with 'foursquare_outdoor_data.json' on the
fsq_id key, and the combined data 

Reset index was used in city_bikes in order to be used as a unique primary key called 'citybike_index'.
When importing data from the Yelp and Foursquare API, on top of the latitude and longitude data
provided, the citybike_index was also stored into each file as a foreign key.

## Results

There are three OLS Regression Results.  The results between the last two are very similar but there are some minor differences.
The first OLS regression contained all the potential independent variables.  The p-values were all greater than
zero except for empty_slots.  Those with the highest p-values were discarded one-by-one, and different combinations were
attempted until the variable p-values were all below the confidence interval 0.05. 
Two OLS results have variables with p-values lower than 0.05, and a Prob (F-statistic) approaching zero, so the regression coefficients are not zero.
A regression model with empty_slots, num_parks, and num_plygrd as variables was selected for three factors:
	- The adjusted R Squared value was greater with 0.34 so more of the dependent variable is explained by changes in the independent variables.
	- The skew was closer to 0, so the data is more symmetric.
	- The Durbin-Watson measurement is closer to 2, so the data has less heteroskedasticity.

The linear regression model looks like:
ln(y) = b0 - b1(x1) - b2(x2) - b3(ln(x3))
with b0 to b3 being the regression coefficients,
y = the number of bikes
x1 = empty bike slots
x2 = number of parks
x3 = number of playgrounds
Solving for y: y = [exp(b0 - b1(x1) - b2(x2))]/(x3^b3)
So the function takes on a Poisson GLM.
The negative coefficients mean that as the variables increase, the number of free bikes available decreases.

In the logit classification model, the LLR p-value approaches zero, so including all the variables improves model fit, 
compared to the intercept-only null model.  All the p-values are below the confidence interval of 0.05 and are statistically significant.
The Pseudo R Squared value of 0.008 is low, which indicates the likelihood of a fitted model to be low.

(fill in what you found about the comparative quality of API coverage in your chosen area and the results of your model.)

## Challenges 
Initially constructing the classes to extract the data using an API request proved difficult but ended up working in the end.
Storing the initial Yelp data into a CSV file was a mistake because some nested objects in the columns were then saved as strings.
I was lucky to get all the data I needed from them when I saved the data into the initial file.
I made sure to save the Foursquare data into a JSON file in order to extract any more nested objects.

One of the main challenges with the model was transforming the dependent and independent variables into a normal distribution.
Outliers, or even a high amount of zeros in the distributions made it difficult to normalize even after taking the natural logarithm,
or taking the natural logarithm of the maximum value in the range minus the value of the datapoint.
(discuss challenges you faced in the project).

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


## Future Goals
If I had more time I would not make the review counts and review scores
independent variables, and instead normalize them into a weighted sum column.
This way the magnitude of the score will also be proportional to the number
of reviews given, (e.g. a 5 star rating with more reviews than another 5 star
rating will have a higher magnitude).

There wasn't enought time to construct a confusion matrix for the MNLogit Regression classification model.

(what would you do if you had more time?)
