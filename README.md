# Final-Project-Statistical-Modelling-with-Python

## Project/Goals
(fill in your description and goals here)

## Process
### A CityBikes API was used to access public bike systems systems in Toronto.
### CityBike url: http://api.citybik.es/v2/


### (your step 2)

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
