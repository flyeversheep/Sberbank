# SberBank House Price Prediction

The goal of this competition is using machine learning approaches to accurately predict the housing prices in Russia. This can help Sberbank gain better understandings of the assets’ value. The training data contains two parts: features of the houses (almost 300 features) and time series of macro data (World Bank data that captures the trend of Russia economy). 

## 1.Data Exploration:
### 1.1    
The housing transactions in the training data have two types: Investment and Owner Occupier. There are more than 5 % outliers in the Investment type.
### 1.2
There are a lot Nan in the dataset. Some important features such as life_sq (living area in square meters), num_room (number of rooms) and build_year (year when the house was built) have almost 30% missing values
### 1.3    
Some records have wrong feature values. E.g. living area is either way too large or smaller than the building area, build year is unrealistically too old or in the feature.
### 1.4    
The prices of the house are not normally distributed. The distribution has a long tail on the high end.

## 2. Data Cleaning and Feature Engineering:
Based on the observations in data exploration, we did the followings to clean the data:
### 2.1    
Remove the outliers in the investment type transactions.
### 2.2    
Since there are about 300 features, it is inefficient to clean each feature individually. We got the feature importance by running a XGBoost regression model. Then we carefully fixed the Nan and wrong values for the top 25 important features. The rest Nan are fixed using the median.
### 2.3    
Logarithm transformation was performed to the house price so that the distribution is close to normal distribution.
### 2.4    
One-hot-encoding was applied to categorical features and the data is normalized by subtracting the mean and then being divided by the standard deviation.
### 2.5    
We found adding the raw macro data to the training set makes prediction worse. This might be due to the linear dependence of features in macro data. Dimension reduction was performed on the raw macro data by principal component analysis (30 principal components, 99.9% explained variance) 


## 3. Model Training:

## 3.1    XGBoost (XGB):
XGB is one of the most popular and successful tree based models in Kaggle competitions. Sometimes, training the model with default hyperparameters could give reasonable results. However, fine tuning the hyperparameters to improve the model is not trivial. Tree-based models tend to overfit the training data. In order to decrease the variance of our model, 5-fold cross validations were performed to choose the correct bootstrap and regularization hyperparameters.

Once an XGB model is trained, it exports the importance of each feature. Using this as a feature selection methods turned out to make our model better. (This can be done by first training a preliminary model with all the features and get the relative importance of each feature. Then the final model is trained using features whose importance is greater than the threshold. The threshold is determined by grid search with cross validation.). 

## 3.2    Random Forest (RF) and Gradient Boosting Regression Tree (GBRT)
5-fold cross validation and grid search were used to determine the number of trees, bootstrap strategies and the criteria of tree-splitting (random forest).

## 4.    Ensemble of the Base Models
Two ways to make an ensemble out of the base models (XGB, RF and GBRT) were performed.
1.    Doing a simple average over all the base models
2.    A ridge linear model is trained on the outputs of the base models. The final prediction is done using the linear model.
