# Why do listeners hangup headphones over Sparikfy?

![Dropping the headphone the customers could move on](/SparkifyBlog/Drophead_phone.jpg "Dropping the headphone the customers could move on")

## Overview
Sparkify is a fictious online Music portal for songs and music. The users are can listen to songs and music as `Guest` or `Registered` users.

But there is a problem that of existing users `cancelling` and leaving. Well, This is users choice. But it is not good for the bussiness. Sparkify needs the customers to stick on. 
As long as users are around there is possibility they could `upgrade and become paid`. Even if they stick on as `free level` user , still there is some minimum revenue to be 
earned due to rolling Adverts.  

As per industry anslysis, The `cost of acquiring new customer is more than the maintaining the existing customers`. But only if we know in advance the customers could leave. 
The industry term for user cancelling and leaving is called `User Churn or Attrition`. The aim of this experiment is to predict the `user churn` using `Machine learning modelling`

## The Data

Any machine learning experiment requires large amounts of releavent data as input for the models to be trained.  The Sparkify portal logs users activity of every actions of every user. This log is rich source of data to understand user behaviour. It is choronological sequence of timestamped events of user. We could understand the activity or behaviour of the user over time. if it may interest you, could have look at the datapoints of the user log below

```
root
|-- artist: string (nullable = true)
|-- auth: string (nullable = true)
|-- firstName: string (nullable = true)
|-- gender: string (nullable = true)
|-- itemInSession: long (nullable = true)
|-- lastName: string (nullable = true)
|-- length: double (nullable = true)
|-- level: string (nullable = true)
|-- location: string (nullable = true)
|-- method: string (nullable = true)
|-- page: string (nullable = true)
|-- registration: long (nullable = true)
|-- sessionId: long (nullable = true)
|-- song: string (nullable = true)
|-- status: long (nullable = true)
|-- ts: long (nullable = true)
|-- userAgent: string (nullable = true)
|-- userId: string (nullable = true)
```


### Understaning the data
> `User Categories`
>> * The registered user can be `free` or `paid` level users.  
>> * The users are can listen to songs and music as `Guest` or `Registered` users.

> `User Musicial Activities`
>> * The users can `listen`, `add a friend`, `thumps up`, `thumbs down` , `add to favourite` to a song.

> `User Financial Activities`
>> * `Upgrade` to paid subscription and `Upgrade` multiple levels of subscription,
>> * `Downgrade` to lower paid level `Downgrade` free level subscription. 
>> * `Users can also `Cancel` the subscription. 

User activies like `home` page visits, `Help`, `login` and `logout` are also other user actions logged by sparkify app/online poral.

### Exploring the Data

We set up by exploring and producting visuailations to understand the user activities. The log data has 2 months of user log.

<div class="output_html rendered_html output_subarea output_execute_result">
<style  type="text/css" >
</style>  
<table id="T_7aea3ce4_cda6_11ea_b23c_0242ac120002" > 
<thead>    <tr> 
        <th class="col_heading level0 col0" >start_date</th> 
        <th class="col_heading level0 col1" >end_date</th> 
    </tr></thead> 
<tbody>    <tr> 
        <td id="T_7aea3ce4_cda6_11ea_b23c_0242ac120002row0_col0" class="data row0 col0" >2018-10-01 00:01:57</td> 
        <td id="T_7aea3ce4_cda6_11ea_b23c_0242ac120002row0_col1" class="data row0 col1" >2018-12-03 01:11:16</td> 
    </tr></tbody> 
</table> 
</div>

   



## Problem Statement
**Evaluate algorithms and build a machine learning model to predict the users churn** using the user event log data. Choose a best model and predict user who could churn using the model.

## Metrics to Evaluate Model
The dataset taken for analyis is imbalanced dataset with only around `10%` cancelled user. As the dataset is heavily imbalanced to existing customers
`F1-score` or `Harmonic Mean` is as used a primary metrics to evaluate the model.

## Data Analysis
These are the fields of the **user log** provide as json file **mini-sparkify-event-data.json**

> The user log has schema as follows.
```
root
|-- artist: string (nullable = true)
|-- auth: string (nullable = true)
|-- firstName: string (nullable = true)
|-- gender: string (nullable = true)
|-- itemInSession: long (nullable = true)
|-- lastName: string (nullable = true)
|-- length: double (nullable = true)
|-- level: string (nullable = true)
|-- location: string (nullable = true)
|-- method: string (nullable = true)
|-- page: string (nullable = true)
|-- registration: long (nullable = true)
|-- sessionId: long (nullable = true)
|-- song: string (nullable = true)
|-- status: long (nullable = true)
|-- ts: long (nullable = true)
|-- userAgent: string (nullable = true)
|-- userId: string (nullable = true)
```
At the start of the anlysis `the` **day** `of the year` is add as additional field for data analysis
```
|-- day: integer (nullable = true)
```

The dataset is checked for `nulls` in `UserId` and `page` datapoints and event log with null values for these 2 datafields are removed.

**EDA** Exploratarory Data Analyis shows that there `229` users and `2 months` of event log in the dataset. The number of free and paid 
registered user and free and paid cancelled users are obsevered.

## Data Visualisation

The log dataset is grouped by `per user per user` basis to find the following weekly metrics. Visulatisation are created based on these metrics.
    
> * `Per Cancelled user Weekly song listening time`
> * `Per Active user Weekly song listening time`
> * `Propotional Weekly number of Sessions per Cancelled user`
> * `Propotional Weekly number of Sessions per active user`
> * `Propotional Weekly Items navigated per Cancelled user`
> * `Propotional Weekly Items navigated per Active user`
> * `Weekly Thumbs Up per Cancelled user`
> * `Weekly Thumbs Up per Active user`
> * `Weekly Thumbs Down per Cancelled user`
> * `Weekly Thumbs Down per Active user`
> * `Weekly Add Friends per Cancelled user`
> * `Weekly Add Friends per Active user`
> * `Weekly number of Advertisement per Cancelled user`
> * `Weekly number of Advertisement per Active user`
> * `Total Cancelled user by Song`
> * `Total Downgraded user by Song`
> * `Total Upgraded user by Song`

The Visualisation are part of the workbook, gives a fair idea on `how the Cancelled and Active users activites varied over time`.

## Feature Selection

Based of the EDA I have decideded to further granualarise the time bucket to a day per user. This will captures users behaviour more prescisely
over the period of the log dataset used, making full use of Time Seriese dataset. The same metricts explored by week in EDA is to be use by as features 
machine learing model but now the `varition of metrics is observed at each day interval`.

## Features extraction
The feature dataset has `a features row for every day and every user for a given day who has Sparikfy event log`

The dataset will look like as below for all day in the dataset

```
userId   | day  |isCancelled  |feature1 | .... | features-n |
---------+------+-------------+---------+------+------------+
userId-1 | 234  | 0           |   0.1023| .... |      230.34|
userId-2 | 234  | 1           |   1.3234| .... |       14.34|
...
userId-1 | 321  | 0           |   0.1023| .... |      230.34|
userId-2 | 321  | 1           |   1.3234| .... |       14.34|
...
userId-n | 125  | 1           |   0.1023| .... |      230.34|
userId-m | 231  | 1           |   1.3234| .... |       14.34|
```
First the `isCancelled` field is marked as `0` if Acitve user ( user who dont have `Cancel` event page ) and `1` for cancelled user
This serves as label for machine learning.

The following features are extract from sparkify event log load to Spark Dataframe. All these features are for individual userId for a day

> * **Daily listening time in minitues** - representing Time spent in listening to songs
> * **Number of sessions daily** - How often user logoff and logged in every day
> * **Number of items in session daily** - How much user was navigating daily
> * **Number of songs daily** - Number of song user listen daily, this is different for how long because use could just play few minutes of the song and skip to nextsong
> * **Number of thumbs up daily**
> * **Number of thumbs downs daily**
> * **Number of upgrades daily**
> * **Number of roll adverts daily**
> * **Number of downgrades daily**
> * **Number of add friends daily**
> * **Number of add to playlist daily**
> * **Number of distinct artist daily**
> * **Number of distinct songs daily**
> * **Negative Value of Sum of Rank of all songs, users Listen to on the day** - This feature attributes for individual songs listened by Users
> * **Negative Value of Sum of Rank of all Artist, users Listen to on the day** - This feature attributes for individual artist listened by Users
> * **Length of time in days between first upgrade and cancellation** - This feature is `0` for user who has not cancelled or free user. 
The value is same for all day of cancelled users.
> * **Lenght of time in days between first downgrade and cancellation** - This feature is `0` for user who are free or user how have not downgraded. 
The value is be same for all day of upgraded users.
> * **Average of lenght of time in days between upgrade to cancel** - This feature is 0 for user who are free or user how have not Upgraded. 
The value is same for all day of upgraded user.
> * **Average of lenght of time in days between consequitive Downgrades** - This feature is `0` for user who are free or user how have not downgraded. 
The value is same for all day of Downgraded users.
> * **Average of lenght of time in days between consequitive Upgrades** - This feature is `0` for user who are free or user how have not Upgraded. 
The value is same for all day of Upgraded users.

At the end of the feature extraction features dataset is saved as `features.csv` for use in later analysis.

## Modeling 

> * The features dataset is loaded from the saved file. Loading back from a `.csv` format reset the feature datatypes tp `string`.
The `string` datatype is changed to `float` Type for all feature columns.

> * Features Vector is created using `VectorAssembler`. Some of the vectors are produced as `sparse vector` as output of `VectorAssembler`
 The `sparseVector` is converted in to `DenseVector` to be used in machine learning training.

> * Features are scaled using `StandardScaler`

> * The final machine learing input dataset is created by using `isCancelled` column as `label` and `scaledFeatures` as `feature` vector.

> * The final `feature` dataset is split by `70 / 30` proporation into `test` and `train` datasets. 

> * Following `5` models are trained using `CrossValidator` with a `3 fold interation` and evaluator as `MulticlassClassificationEvaluator`
```
     Evaluated_model = CrossValidator(estimator=model_in,
                                     estimatorParamMaps=param_grid,
                                     evaluator=MulticlassClassificationEvaluator(),
                                     numFolds=3,
                                     seed=77
                                    )
```
>> * **Logisctic regression**
>> * **Random Forests**
>> * **Gradient-Boosted Trees**
>> * **Support Vector Machine**
>> * **MultilayerPerceptronClassifier**

> * The `Crossvalidator` picks the best comination of `hyperparameter` of the model and whole of `train` dataset is used to train the models. 
    The trained model is saved.

> Prediction are made with `test` dataset `feature` and prediction is compared with `actual` value of `label` column.
 Based on the `prediction` outcome and the `actual` value of the `label` column of `test` dataset following `metrics` are 
 calculated for each model

>> * **Accuracy**
>> * **Precision**
>> * **Recall**
>> * **F1-Score**

> The model metrics are plotted for comparision using `seaborn` and `F1` score is used as `model selection criteria`. The model with the 
largest `F1` score is selected.

## Conclusion
The experiment is done here with small set of data of 2 months. There features were computed on each day basis to account for the
variation of data over time. There are 20 features extracted from raw log data. The featured dataframe is then used to for ML models. 
There are 5 binary classification algorthms used to evaluate the metrics. Out of 5 models `Random Forest` proved to be better in all
metrics and highest `F1-score` of `32%`. 

Though Initial set out to work with `medium-sparkify-event-data.json` on IBM cloud, by the end of data analysis I have exhausted
the processing units of Free tier. So I had to continue the experiment on Udacity workspace with smaller dataset provided. 

There also needs to be further work done for hyperparameter tuning to improve the `F1-score`. Because of limited resources the models are trained with 
`mini_sparkify_event_data.json`. There is a need to train all choosen models with a larger dataset. Then the model also needs to be 
pacakged as class and pacakges for deployment.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
**Copyright (c) 2020** Sureshbabu K

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Credits

* I have heavily referenced [sparkify by drnesr](https://github.com/drnesr/Sparkify) to complete the models. 

* I have heavily referenced online documentation of pySpark, Pandas, Stackoverflow, Databrick and other techincal content online

* The license text choosen from [choosealicense](https://choosealicense.com/licenses/mit/)


**For further information on the Data Analysis and Machine learing modeling outtcome please refer to [The Sparkify blog](--- Blog hyperlink here ---).**

