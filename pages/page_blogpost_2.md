# Predicting Sale Price for homes in Kings County Seattle

**Description:** In this post I want to review one technique I used that drastically improved the performance of my model, feature engineering using the **dependent** variable. This can be a powerful tool, but when used improperly it can lead to [data leakage](https://medium.com/@gurupratap.matharu/data-leakage-in-machine-learning-390d560f0969). This essentially means that our model gets trained on part of the testing data, which defeats the purpose of the split and may lead to poor model generalization. The solution is to create the test/train split before feature engineering, and then create our new features only using the training data. We can then re-create our test/train splits down the road using the indexes of our original splits the ensure they are identical. 

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

### Create test/train split and merge
```python
# Create our X and y
y0 = dataframe['sale_price']
X0 = dataframe.drop(columns=['sale_price'])

# Train/Test Split
X_train0, X_test0, y_train0, y_test0 = train_test_split(X0, y0, test_size = 0.25, random_state = 42)

# Create DataFrame of training data only
train_data0 = pd.merge(X_train0, y_train0, left_index = True , right_index = True)
```
