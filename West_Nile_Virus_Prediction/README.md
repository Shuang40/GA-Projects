# Project 4 : Predicting the West Nile Virus Prediction in Chicago

---

## Content

- [Problem Statement and Context](#Problem-Statement-and-Context)
- [EDA, Cleaning and Preprocessing](#EDA,-Cleaning-and-Preprocessing)
- [Modelling](#Modelling)
- [Consequences of Model Error](#Consequences-of-Model-Error)
- [Cost-Benefit Analysis](#Cost-Benefit-Analysis)
- [Recommendations](#Recommendations)
- [Conclusion](#Conclusion)
- [Next Steps](#Next-Steps)

---

## Problem Statement and Context

- To predict the when and where the West Nile Virus will occur in mosquitos by taking into account a range of variables (e.g. location, temperature etc.), and using three classification models for the prediction:
    - Linear regression, K-nearest neighbours, Random forest
- We will use the Receiver Operating Characteristic (ROC) Area Under Curve (AUC) score to evaluate our models. The higher the score, the better.
- A more accurate method of predicting outbreaks of West Nile virus in mosquitos will help the City of Chicago and Chicago Department of Public Health (CPHD) more efficiently and effectively allocate resources towards preventing transmission of this potentially deadly virus.
---

## EDA, Cleaning and Preprocessing

We have performed exploratory data analysis, data cleaning, and preprocessing on the following datasets:
### [Training and Test Set of the Main Dataset](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/code/01_train_test.ipynb)
- There were no null values in both train and test data.
- For both the test and train datasets, we:
    - removed columns relating to address since the longitude and latitude are sufficient in pinpointing locations
    - converted all column names to lowercase for easier reference
    - converted ‘date’ to day of week, month, and year
    - converted categorical variables to dummies (one-hot encoding) for processing by model later
- The more mosquitos in the trap, the  likely WNV is present.
![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/wmvpresent.png?raw=true)
- WNV is only transmitted by Culex Pipiens and Culex Restuans.
![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/species.png?raw=true)
- Across all years in the train dataset, the number of mosquitoes peaked in June.
- Across all years in the train dataset, 2007 had the highest number of mosquitos.
![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/month_year.png?raw=true)
- We see that the first occurrences of WNV appeared in the south of Chicago in July. By August, WNV can be found throughout Chicago. The occurrences of WNV began to subside in September, and was greatly reduced by October.
    ![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/2007.png?raw=true)

### [GIS data of spraying efforts in 2011 and 2013](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/code/02_spray.ipynb)
- Aside from changing the Date type from object to datetime, we have also identify 2 latitudes that have duplicates which we subsequently removed.
- Next, we realized that there are only 10 unique spray dates so, we decided to plot that out and take a look.
![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/spray_locations.png?raw=true)
- We see that the spray took place twice in 2011 and 8 times in 2013.
- Looking at the visualization, no pattern was observed. And due to inconsistent reporting period, limited spray data and not available in test set, we decided that we will not incorporate this spray dataset into our modeling.

### [Weather Data from 2007 to 2014](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/code/03_weather.ipynb)
- Cleaning
    - Changing `Date` data type from object to datetime.
    - Add dates, breakdown by Year, Month, Week and Day
    - Fill values in column `PrecipTotal` labelled `T` and `M` with zero.
- Feature Engineering
    - Add  daily temperature range. `Tmax` minus `Tmin`
    - Add relative humidity
    - Add number of days since it last rained
    - Introduce 14 days lagged weather feature
        - Based on mosquito's lifecycle, a mosquito takes about 7 to 14 days to be full grown
---

## [Modelling](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/code/04_modelling.ipynb)

### Baseline

We can evaluate the model based on the following metrics:
- Accuracy = (TP + TN) / (TP + TN + FP + FN)
- Sensitivity = TP / (TP + FN)
- Specificity = TN / (TN + FP)
- Precision = TP / (TP + FP)
- AUC ROC

Accuracy should not be used in this imbalanced dataset, where only 5% of the observations actually has West Nile virus. Hence, a naive guess will most likely be 95% accurate, but the 5% wrong guess will be devastating.

AUC ROC is superior because it will give us a score across all False Positive rates that we choose and therefore it is more robust (rather than a specific False Positive rate score e.g. for a high recall).

We should achieve an AUC ROC score of more than the baseline of 0.5,  where the baseline occurs when the predictor makes random guesses which is half correct, half wrong.

### Consequences of Model Error

- False Positive - where we claimed WNV was present when it really wasn't
- False Negative - where we claimed WNV was not present, when in reality it was present.

- A False Negative has greater costs than a False Positive, since people would be at higher risk of contracting the virus without any mitigating factors.

- On the other hand, the costs of a False Positive appear less severe e.g. are that people may get more annoyed, stay in at night during the summer as opposed to going out, or perhaps use more bug spray than usual. It may also encourage city authorities to spray more than they normally would.

### Evaluation

- The following classifiers are used:
    - Logistic Regression
    - K-Nearest Neighbors
    - Random Forest
- Certain hyperparameters for all models are optimized using RandomizedSearchCV, Pipeline, and StandardScaler

||Logistic Regression|K-Nearest Neighbors|Random Forest|
|---|---|---|---|
|Accuracy|0.69|0.93|0.66|
|Recall|0.75|0.07|0.87|
|Precision|0.12|0.18|0.12|
|ROC AUC|0.78|0.71|0.83|

- The sensitivity (recall) of the models to detect WNV could range from detecting 7% to detecting 87%.
- For imbalanced dataset, LogisticRegression and RandomForest are likely to predict better, given they have the option to introduce weights to each class, i.e. a heavier weight to be given to data where the virus is present since it’s only about 5% of the whole dataset.
- K-Nearest Neighbors doesn’t have the option, hence its ROC AUC is lowest out of the 3 @ 0.71.
- In this case, RandomForest is the best model @ 0.83 and hence we will use this model for further analysis and interpretation.

![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/roc.png?raw=true)
- Middle Chart: Precision means the percentage of your results which are relevant. On the other hand, recall refers to the percentage of total relevant results correctly classified by your algorithm.
- AUC under the ROC curve is ~83% so the model is robust enough to allow flexible choice of thresholds ( choosing the most fitting recall vs precision rates)
- The model is based on the assumption that the number of mosquitos is not a valid info that could be collected and used for prediction. The model’s performance would change if we include the NumMosquitos variable. At this scenario we can use all other features to predict the number of mosquitos, and then use that prediction as a new feature in a new classification model, to make a new prediction for the WNV occurrences. We can expect that the performance of this model would be higher given an additional feature which is highly correlated to whether virus is present.
- The data could be enriched further with publicly available data, e.g. Implementing data on locations of exposed water reservoirs, lakes and other bodies of fresh water which might be correlated to increased mosquito population.

### Feature Importance
- Out of so many features, let’s choose the ones with the strongest effect on West Nile Virus response
- We chose a certain threshold to display (to avoid showing all features)
![png](https://git.generalassemb.ly/junkaitoh/project_4/blob/master/images/feature_importances.png?raw=true)
- Whether the specie is `CULEX_PIPIENS` or `CULEX RESTUANS` or not is important, as well as the week / month (especially June) in the year (e.g. peak of summer) is also, unsurprisingly important
- Most important features are engineered weather features
- From weather data, temperature seems most important (probably affect breeding of mosquitoes)
- Wind speed possibly played a part (`ResultSpeed_lag_14`)
- Finally, some years have more occurrences then other years


---

## Cost-Benefit Analysis

We analysed two separate studies:
- “Initial and Long-Term Costs of Patients Hospitalized with West Nile Virus Disease”: This  study showed that WNV costs the government about \\$21,000 per person.
- “Economic cost analysis of West Nile virus outbreak, Sacramento County, California, USA, 2005”: This study showed that in 2015, WNV in Sacramento County cost approximately \\$2.28 million for medical treatment and patients' productivity loss, whereas vector control cost approximately \\$701,790. In addition, only 15 cases of West Nile neuroinvasive disease would need to be prevented to make the emergency spraying cost-effective.
- Cost of WNV in 2002. There were more than 800 cases of WNV in Chicago during the first emergence WNV in 2002. This would have cost the city approximately \\$17 million (800*\\$21,000).
- Cost of vector control against WNV. We did not have data on the cost of spraying in Chicago. Using a simple assumption that ~\\$700,000 worth of vector control was used by Sacremento County for its full land area of 2,500 km<sup>2</sup>, we can make a rough estimation that it would cost Chicago ~$7 million for its full land area of 28,120 km<sup>2</sup>.

Sources:
- https://www.ncbi.nlm.nih.gov/pubmed/20202424
- https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3945683/
- https://www.dph.illinois.gov/topics-services/diseases-and-conditions/west-nile-virus/vector-control
- https://en.wikipedia.org/wiki/Sacramento_County,_California
- https://en.wikipedia.org/wiki/Chicago_metropolitan_area

---

## Recommendations
**Strategically fogging**
- Although we couldn’t substantiate the impact of spraying due to the lack of data, we will still recommend the idea of spraying/fogging
- Some considerations:
    - Inhabitants population density area (focus on where denser population instead)
    - Prioritize by infested locations
    - Fogging to be done before mosquitoes peak season e.g. Jun to Aug

**Control tall grass and shrubbery**
- Mosquitoes like to rest in tall grass, plants and shrubs. When we focus on denser population area, for the area where lesser population resides, wedding/grass cutting effort need to be put in place to ensure grass are cut to a length of 5 inches or less to prevent mosquitoes from congregating there.
- Also, to work with parks department to explore introducing mosquito-repelling plants such as citronella Grass / Basil / Lemon Balm

**Eliminate / treat standing water**
- We propose to invest efforts in prevention, starting from increasing awareness of the general population to not leave exposed untreated water (e.g. ponds and pools in their yard). If we cannot get rid of the water, consider treating it by introducing mosquito dunks and bits (small biscuit-like object that dissolves over time and releases a chemical that helps control mosquito larvae).

**Mosquito sterilization**
- Additionally, since one specie (Culex Pipiens, Culex Pipiens/Restuans) stands out as a carrier with higher rates of WNV, We suggest to increase efforts in preventing the proliferation of this specie in particular. This could be done with more specific and environmentally friendly measures (e.g. “targeted biological mosquito control”) which would make the process of lowering occurrences more efficient and less costly

https://control-mosquitoes.com/5-ways-to-control-mosquitoes-in-your-yard/

---

## Next Steps

- Our ROC AUC score was 0.83; however, our Kaggle score was 0.6.
- The scores here are ROC AUC scores. These numbers establish how well a model deals with, as a threshold for predicting the presence of West Nile is increased, the trade off between True and False positives. Namely as the threshold increases, there are fewer predicted positives and the ROC AUC score is a numeric measure of this trade off. A score of 0.5  indicates that the model is doing no better than truly guessing.
- The huge disparity between our local scores and the Kaggle scores indicate that our models are almost entirely modeling noise. It is our recommendation that the project continue in one or more of the following ways:
- Restructure the data
- Since our local score are so different than the Kaggle scores, we are likely just modeling the noise present in the data. More work should be done to address this.
- Get more, cleaner data
- There is a fair amount of missing data in the weather information, so it would probably be worthwhile to track down the true missing values, if they can be found.

---
