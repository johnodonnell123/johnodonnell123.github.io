# Predicting Sale Price for homes in Kings County Seattle

**Description:** In this post I want to review one technique I used that drastically improved the performance of my model, feature engineering using the ***dependent*** variable. This can be a powerful tool, but when used improperly it can lead to [data leakage](https://medium.com/@gurupratap.matharu/data-leakage-in-machine-learning-390d560f0969). This essentially means that our model gets trained on part of the testing data, which defeats the purpose of the split and may lead to poor model generalization. The solution is to create the test/train split ***before*** feature engineering, and then create our new features only using the training data. We can then re-create our test/train splits down the road using the indexes of our original splits the ensure they are identical. 

### Data Overview:
The data was provided by Flatiron School, originally sourced from Kaggle. Features include:

- Sale Price (dependent variable)
- Sale Date
- Bedrooms
- Bathrooms
- Sqare footage (living, basement, lot)
- Floors
- Waterfront (Boolean)
- Number of views
- Condition
- Grade
- Year Built
- Year Renovated
- Zipcode
- Latitiude / Longitude
- Square Footage of 15 nearest neighbors (house/lot)

The feature engineering will be grouping features by zipcode, such as the median sale price of the zipcode. If we used all of the data in the entire set, we would be providing the dependent to the training set as it would be included in that median value. 

### Methodology:
1) Create a test/train split, yielding X_test0, y_test0, X_train0, y_train0
2) Merge the X_train0 and y_train0 to create a DataFrame consisting of only training data
3) Create a new DataFrame populated with the features grouped by `zip_code`
4) Add these new columns back to the original DataFrame
5) Perform other feature engineering/transformations/standardizations
6) Use our previous splits to create new ones for modelling
7) View results

### Create test/train split 
```python
# Create our X and y
y0 = dataframe['sale_price']
X0 = dataframe.drop(columns=['sale_price'])

# Train/Test Split
X_train0, X_test0, y_train0, y_test0 = train_test_split(X0, y0, test_size = 0.25, random_state = 42)
```

### Create DataFrame of training data only
```python
train_data0 = pd.merge(X_train0, y_train0, left_index = True , right_index = True)
```


### Create new DataFrame populated with metrics GroupedBy `zip_code`:
```python
# Create DataFrame to populate with GroupBy data
zipcode_dataframe = pd.DataFrame()

# Create a column in the new DataFrame for each feature grouped by zipcode
# We are adding data to our zipcode DataFrame, but only extracting data from our train set
for col in ['sale_price','sqft_living','grade','yr_built']:                
    zipcode_dataframe[f'median_{col}_in_zip'] = train_data0.groupby('zipcode')[col].median()
```


### Add column to original DataFrame
```python
dataframe = dataframe.merge(zipcode_dataframe, left_on = 'zipcode', right_index = True)
```


### Perform other feature engineering/transformations/standardizations
- I will not go into the detail of these operations here as they are not relevant to the lesson of the post
- I simply log transformed distributions that were log-normally distributed and standardized features 


### Use our previous splits to create new ones for modelling, test to ensure they are the same
```python
# Define X and y
y = dataframe['sale_price_log']
X = dataframe.drop(columns=['sale_price_log'])

# Create splits using the indexes from the previousy defined splits to ensure they are identical
X_train = dataframe[dataframe.index.isin(X_train0.index)].drop(columns=[sale_price_log])
X_test = dataframe[dataframe.index.isin(X_test0.index)].drop(columns=[sale_price_log])
y_train = dataframe[dataframe.index.isin(y_train0.index)][sale_price_log]
y_test = dataframe[dataframe.index.isin(y_test0.index)][sale_price_log]

# Test to ensure that our splits are the same as before
test1 = set(X_train.index) == set(X_train0.index)
test2 = set(X_test.index) == set(X_test0.index)
test3 = set(y_train.index) == set(y_train0.index)
test4 = set(y_test.index) == set(y_test0.index)

if np.all([test1, test2, test3, test4]):
    print('pass')
else:
    print('fail')
```


### View Results
- Here we view the results of the model when features are feature engineered and not feature engineered, as well as log transformed and not log transformed. 

<img src="/images/flatiron_blog_posts/model_performance_features_engineered_log_transformed.PNG?raw=true" width="70%" height="70%">


# Conclusion:
As we can see from the plot above, the feature engineering provides a significant increase in model performance, even more so that the log transform. Ensuring that we don't train a model on testing data required more effort but is very important. We can improve our models performance materially and keep our model performing stronly on unseen data! 
