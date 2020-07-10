# Project 2: Ames, Iowa Housing Data

## Problem Statements

> **Problem 1 - For Kaggle: What data cleaning, feature engineering, and modelling techniques minimize the root mean squared error (RMSE) of predicted home sales prices in the Ames, Iowa housing dataset?**  
>
> **Problem 2 - Further Inquiry: If the aforementioned model is accurate enough, can prediction inaccuracies be said to reflect valuation errors rather than modeling errors? If so, can I build a pipeline to return undervalued homes in a user-navigable widget?**


## Data

> The two datasets provided by Kaggle (train.csv, test.csv), contain housing data from homes sold in Ames, Iowa between 2006 and 2010, inclusive. The train dataset contains 2051 unique homes and 81 features such as lot square footage, number of half bathrooms, number of cars the garage can hold, etc. The test dataset contains 878 unique homes and 80 features, the same features as the train set less one, 'SalePrice'. The sale price feature will serve as the target variable for this modeling project. 


## Data Cleaning and Feature Engineering

> project_2/data_cleaning_and_engineering.ipynb
>
> **My data cleaning approach aimed to preserve as many features as possible, but stringently reject outliers**. Missing values were imputed with logical replacement values as to preserve those features for regression modeling. Several catergorical features were one-hot-encoded and others were converted to ordinal values in order to suit regression modeling. For outlier detection, I calculated the euclidean distance between all points in the feature_x(standardized) vs SalePrice(standardized) 2-dimensional plane, and rejected observations whose nearest neighboring point was significantly farther than the mean distance. My idea was to reject observations that radically differed from the trend of that 2d relationship, not simply reject values that were extreme within a single dimension. 2nd degree polynomial features were created for non-binary numeric columns.
>
> Several permutations of this cleaning and engineering pipeline were tested. For a synopsis of each dataset contained in the project_2/datasets folder, refer to the project_2/dataset_dictionary.ipynb notebook
>
> For basic summary statistics and details of the specific transformations conducted on each feature, refer to the project_2/data_dictionary.ipynb notebook


## Problem 1 - Kaggle Modeling

> project_2/modeling_and_validation_kaggle.ipynb
>
> The best performing model proved to be a ridge regularized multiple regression model, trained on train_mod05.csv (outliers excluded, polynomial vars included). The target variable was log transformed as to normalize the distribution of target values. Then the entirety of train dataset features less 'PID', 'ID', and 'SalePrice' were standardized and fit to a ridge regression model. The size of the feature space precludes having a particularly interpretable model, but there seems to be a benefit in model fit. The regression lambda value was optimized via the sklearn.linear_model.RidgeCV method. **Train data was intentionally not train-test split** This allowed me to train my model on a larger amount of data, which I felt was advisable given the relatively small size of the datasets. Instead of measuring bias/variance on a hold-out dataset, I confirmed the generalizability by calculating cross validated R2 scores. Sales price values were then predicted for the 878 test dataset observations, exponentiated to return them to their original scale, and submitted to Kaggle

> These techniques resulted in mean cross validated **R2 scores of approximately 0.92**. My lowest **RMSE score reached 20208.78**, which is roughly equivalent to an average housing price prediction miss of $20208.78. This value was stable across train and test sets 


## Problem 2 - Further Inquiry

> project_2/modeling_and_validation_presentation.ipynb
>
> Using the same model described above, I predicted sales prices for the train dataset and compared them with the actual sales prices. Given the accuracy of my model, I believe it is fair to say that some amount of the variance of these residual values is attributable to poor valuations rather than poor modeling. Although I recognize that it is difficult to disentangle the two, and the logic is somewhat circular. Regardless, I found 219 homes that sold for less than 90% of their theoretical value predicted by the model. After calculating a few descriptive statistics and comparing the undervalued and not-undervalued data subsets, I created a widget so that non-technical users might be able to navigate the 219 undervalued homes. The code to build that widget was almost entirely borrowed from: https://medium.com/@marekermk/guide-to-interactive-pandas-dataframe-representation-485acae02946 However the DataTables object it is built on presents lots of opportunity for further iteration.


## Future Directions:
* Engineer more features, create features more delibrately rather than relying on regularization
* Use LASSO regression for feature selection (attempted, without success)
* Conduct a more thorough analysis of undervalued homes - disentangle valuation error from model error
* Add drop down menu to dataframe widget


### Slide Deck
> For a presentation on Problem 2, tailored to a lay audience, see project_2/Project2_AmesIA.pptx
