# Why do listeners hangup headphones over Sparkify?

![Dropping the headphone the customers could move on](/SparkifyBlog/Drophead_phone.jpg "Dropping the headphone the customers could move on")

## Overview
#### Sparkify is a fictious online Music portal for songs and music create by Udacity for DataScientist Capstone project turorial. 

>>> ![SparkifyLogo](/SparkifyBlog/Sparkify_Udacity.png "Sparkify Logo")

The users of **Sparkify** can listen to songs and music from the online portal or add as `Guest` or `Registered` users.

But there is a problem. The of existing users `cancelling` and leaving. Well, This is users choice. But it is not good for the bussiness. Sparkify needs the customers to stick on. 
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

There are `52` cancelled users out of `226` users which is around `23.3%`

<div class="output_html rendered_html output_subarea output_execute_result">
<style  type="text/css" >
</style>  
<table id="T_7c93a8be_cda6_11ea_b23c_0242ac120002" > 
<thead>    <tr> 
        <th class="col_heading level0 col0" >DISTINCT Users</th> 
    </tr></thead> 
<tbody>    <tr> 
        <td id="T_7c93a8be_cda6_11ea_b23c_0242ac120002row0_col0" class="data row0 col0" >226</td> 
    </tr></tbody> 
</table> 
</div>

<div class="output_html rendered_html output_subarea output_execute_result">
<table><tr style="border-bottom: 1px solid"><td style="font-weight: bold">Cancelled Users</td></tr><tr><td>52</td></tr></table>
</div>

The following graph shows `weekly metrics` of users who `eventually cancelled` with in the time period of log and `Users who has not cancelled`. This is done to identify the metrics that could be used for training the machine learning model 

#### `Per Cancelled user Weekly song listening time` vs `Per Active user Weekly song listening time`

![Weekly song listening time - Cancelled vs Active user](/SparkifyBlog/Listen_Time.png "Weekly song listening time - Cancelled vs Active user")

The paid user seems to spend more time as expected. But it is surprising to see per cancelled users send more time listening to song in a week than per registered user especially in the last week.

#### `Propotional Weekly number of Sessions - Cancelled vs Active Users`

![Propotional Weekly number of Sessions - Cancelled vs Active Users](/SparkifyBlog/Sessions.png "Propotional Weekly number of Sessions - Cancelled vs Active Users")

The `paid` user seems to have more number of session per week as compared to free user as the graph 2. But treand is not very evdient with cancelled users.

#### `Weekly number of Thumbs Up, Thumbs Down, Add Friends per Cancelled user vs Active user`

![Weekly number of Thumbs Up, Thumbs Down, Add Friends per Cancelled user vs Active user](/SparkifyBlog/Metrics.png "Weekly number of Thumbs Up, Thumbs Down, Add Friends per Cancelled user vs Active user")

The above graphs shows Cancelled users and Active user are having faily same level of thumbs up and thumbs downs. cancel users have relatively added more friends than active users. This could be they are more enagaged with there friends and sparkify, As they are more engaged they  expect more from performace Sparkify portal.

#### `Weekly number of page navigation per Cancelled user vs Active user`

![Weekly number of page navigation per Cancelled user vs Active user](/SparkifyBlog/navigation.png "Weekly number of page navigation per Cancelled user vs Active user")

## Feature Selection
Based of the **Exploratory Data Analsysis** steps above, I have decided to further granualarise the time bucket to `per user per day` basis, the same set of metrics. This way the user behaviour is more prescisely captured over the 2 months period of the log dataset, Making a full use of `Time Series` nature of the dataset. The same metricts explored by week in EDA is to be use by as features of machine learing model but now with the `varition of metrics is observed at each day interval`.

The section of completed feature dataset is provide below. There is 1 row per day per user with the `machine learning Classification` `Label` as `isCancelled` column and rest of the numeric fields except `userId` and `day` are to be used in building `Feature Vector` as an input to machine learning alogrithms. 

### The features used 

<div class="output_html rendered_html output_subarea output_execute_result">
<style  type="text/css" >
</style>  
<table id="T_0604e6f8_cda7_11ea_b23c_0242ac120002" > 
<thead>    <tr> 
        <th class="col_heading level0 col0" >userId</th> 
        <th class="col_heading level0 col1" >day</th> 
        <th class="col_heading level0 col2" >isCancelled</th> 
        <th class="col_heading level0 col3" >listenTime</th> 
        <th class="col_heading level0 col4" >sessions</th> 
        <th class="col_heading level0 col5" >numOfItems</th> 
        <th class="col_heading level0 col6" >numOfSongs</th> 
        <th class="col_heading level0 col7" >thumbsUp</th> 
        <th class="col_heading level0 col8" >thumbsDown</th> 
        <th class="col_heading level0 col9" >upgrade</th> 
        <th class="col_heading level0 col10" >rollAdvert</th> 
        <th class="col_heading level0 col11" >advert_seconds</th> 
        <th class="col_heading level0 col12" >downgrade</th> 
        <th class="col_heading level0 col13" >addFriend</th> 
        <th class="col_heading level0 col14" >addToPlaylist</th> 
        <th class="col_heading level0 col15" >numOfArtist</th> 
        <th class="col_heading level0 col16" >numOfUniqSongs</th> 
        <th class="col_heading level0 col17" >sumSongRank</th> 
        <th class="col_heading level0 col18" >sumArtistRank</th> 
        <th class="col_heading level0 col19" >upgradeToCancelDays</th> 
        <th class="col_heading level0 col20" >downgradeToCancelDays</th> 
        <th class="col_heading level0 col21" >avgDowngradeDays</th> 
        <th class="col_heading level0 col22" >avgUpgradeDays</th> 
    </tr></thead> 
<tbody>    <tr> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col0" class="data row0 col0" >126</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col1" class="data row0 col1" >283</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col2" class="data row0 col2" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col3" class="data row0 col3" >977.0304800000001</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col4" class="data row0 col4" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col5" class="data row0 col5" >6</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col6" class="data row0 col6" >3</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col7" class="data row0 col7" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col8" class="data row0 col8" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col9" class="data row0 col9" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col10" class="data row0 col10" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col11" class="data row0 col11" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col12" class="data row0 col12" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col13" class="data row0 col13" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col14" class="data row0 col14" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col15" class="data row0 col15" >3</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col16" class="data row0 col16" >3</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col17" class="data row0 col17" >-3</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col18" class="data row0 col18" >-3</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col19" class="data row0 col19" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col20" class="data row0 col20" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col21" class="data row0 col21" >1.6103075396825437</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row0_col22" class="data row0 col22" >2.4966608796296295</td> 
    </tr>    <tr> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col0" class="data row1 col0" >126</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col1" class="data row1 col1" >334</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col2" class="data row1 col2" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col3" class="data row1 col3" >39161.05634999997</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col4" class="data row1 col4" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col5" class="data row1 col5" >189</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col6" class="data row1 col6" >162</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col7" class="data row1 col7" >9</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col8" class="data row1 col8" >4</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col9" class="data row1 col9" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col10" class="data row1 col10" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col11" class="data row1 col11" >1.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col12" class="data row1 col12" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col13" class="data row1 col13" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col14" class="data row1 col14" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col15" class="data row1 col15" >156</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col16" class="data row1 col16" >161</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col17" class="data row1 col17" >-162</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col18" class="data row1 col18" >-162</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col19" class="data row1 col19" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col20" class="data row1 col20" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col21" class="data row1 col21" >1.6103075396825437</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row1_col22" class="data row1 col22" >2.4966608796296295</td> 
    </tr>    <tr> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col0" class="data row2 col0" >126</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col1" class="data row2 col1" >318</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col2" class="data row2 col2" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col3" class="data row2 col3" >3772.726980000001</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col4" class="data row2 col4" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col5" class="data row2 col5" >22</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col6" class="data row2 col6" >17</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col7" class="data row2 col7" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col8" class="data row2 col8" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col9" class="data row2 col9" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col10" class="data row2 col10" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col11" class="data row2 col11" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col12" class="data row2 col12" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col13" class="data row2 col13" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col14" class="data row2 col14" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col15" class="data row2 col15" >17</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col16" class="data row2 col16" >17</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col17" class="data row2 col17" >-17</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col18" class="data row2 col18" >-17</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col19" class="data row2 col19" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col20" class="data row2 col20" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col21" class="data row2 col21" >1.6103075396825437</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row2_col22" class="data row2 col22" >2.4966608796296295</td> 
    </tr>    <tr> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col0" class="data row3 col0" >126</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col1" class="data row3 col1" >274</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col2" class="data row3 col2" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col3" class="data row3 col3" >10138.736670000002</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col4" class="data row3 col4" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col5" class="data row3 col5" >38</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col6" class="data row3 col6" >28</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col7" class="data row3 col7" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col8" class="data row3 col8" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col9" class="data row3 col9" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col10" class="data row3 col10" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col11" class="data row3 col11" >471.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col12" class="data row3 col12" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col13" class="data row3 col13" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col14" class="data row3 col14" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col15" class="data row3 col15" >28</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col16" class="data row3 col16" >28</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col17" class="data row3 col17" >-28</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col18" class="data row3 col18" >-28</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col19" class="data row3 col19" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col20" class="data row3 col20" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col21" class="data row3 col21" >1.6103075396825437</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row3_col22" class="data row3 col22" >2.4966608796296295</td> 
    </tr>    <tr> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col0" class="data row4 col0" >126</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col1" class="data row4 col1" >330</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col2" class="data row4 col2" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col3" class="data row4 col3" >7517.45297</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col4" class="data row4 col4" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col5" class="data row4 col5" >35</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col6" class="data row4 col6" >29</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col7" class="data row4 col7" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col8" class="data row4 col8" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col9" class="data row4 col9" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col10" class="data row4 col10" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col11" class="data row4 col11" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col12" class="data row4 col12" >0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col13" class="data row4 col13" >2</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col14" class="data row4 col14" >1</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col15" class="data row4 col15" >29</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col16" class="data row4 col16" >29</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col17" class="data row4 col17" >-29</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col18" class="data row4 col18" >-29</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col19" class="data row4 col19" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col20" class="data row4 col20" >0.0</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col21" class="data row4 col21" >1.6103075396825437</td> 
        <td id="T_0604e6f8_cda7_11ea_b23c_0242ac120002row4_col22" class="data row4 col22" >2.4966608796296295</td> 
    </tr></tbody> 
</table> 
</div>

### Meaning of the features

`isCancelled` field is marked as `0` if Acitve user ( user who dont have `Cancel` event page ) and `1` for cancelled user. This serves as label for machine learning.
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

## The Machine Learning modelling 
 
Machine learning modelling is not something out of science fiction movies, But it is about **applying statistical methods** over very large volume of data or **big data**. When we say **Big data** the data which can not fit into one single computers memory and hence had to be split into multiple chunks , stored and processed seperately on multiple comuters. The results of the processing is assembled back into single unit by driver computer.  This is roughly how we describe the **distributed processing**

If you like, You can find a interesting read on machine learning to start to understand [here](https://vas3k.com/blog/machine_learning/).
 
### Machine learning Algorithms
`A machine learning process can be applied on big data at distributed processing facility` based on what I have described above. 
This value to be predicted can only `0` or `1`. This is a `binary classification` problem. 

Applying the same statement for this experiment the following `binary classification` machine learning alogrithms  
>> * **Logisctic regression**
>> * **Random Forests**
>> * **Gradient-Boosted Trees**
>> * **Support Vector Machine**
>> * **MultilayerPerceptronClassifier**
are applied on **mini_sparkify_event_log.json** at the `Udatacity workspace` to train the models. 

### Metrics to Evaluate the ML Models
The dataset taken for analyis is imbalanced dataset with only around `23%` cancelled user. As the dataset is slightly imbalanced towards existing customers
`F1-score` or `Harmonic Mean` is as used a primary metrics to evaluate the model. Validation data is used for prediction of `IsCancelled` value using trained. The model metrics are calcuated for each model. Model with the best `F1-Score` can be used for further tuning.

### Resultant Model Metrics.

There are 4 metrics calculated of each trained model and are presented as follows.
> * **Accuracy**
> * **Precision**
> * **Recall**
> * **F1-Score**

<div class="output_html rendered_html output_subarea output_execute_result">
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Accuracy</th>
      <th>Precision</th>
      <th>Recall</th>
      <th>F1</th>
      <th>model</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training</th>
      <td>0.951020</td>
      <td>0.142157</td>
      <td>0.707317</td>
      <td>0.236735</td>
      <td>Support Vector Machine</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.970620</td>
      <td>0.148005</td>
      <td>0.809859</td>
      <td>0.250272</td>
      <td>Support Vector Machine</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.978237</td>
      <td>0.157014</td>
      <td>0.859155</td>
      <td>0.265506</td>
      <td>Logistic Regression</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.971882</td>
      <td>0.167211</td>
      <td>0.831978</td>
      <td>0.278458</td>
      <td>Logistic Regression</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.984766</td>
      <td>0.168597</td>
      <td>0.922535</td>
      <td>0.285092</td>
      <td>MultilayerPerceptronClassifier</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.993471</td>
      <td>0.175032</td>
      <td>0.957746</td>
      <td>0.295974</td>
      <td>Random Forest</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.993471</td>
      <td>0.175032</td>
      <td>0.957746</td>
      <td>0.295974</td>
      <td>Gradient-Boosted Trees</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.982313</td>
      <td>0.181373</td>
      <td>0.902439</td>
      <td>0.302041</td>
      <td>MultilayerPerceptronClassifier</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.990930</td>
      <td>0.190087</td>
      <td>0.945799</td>
      <td>0.316553</td>
      <td>Gradient-Boosted Trees</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.991383</td>
      <td>0.190632</td>
      <td>0.948509</td>
      <td>0.317460</td>
      <td>Random Forest</td>
    </tr>
  </tbody>
</table>
</div>
</div>

### Comparision of Metrics of 5 Machine Learning Models

![Comparision of metrics of 5 Machine Learning Models](/SparkifyBlog/Model_Compare.png "Comparision of metrics of 5 Machine Learning Models")

## Conclusion
The experiment is done with small set of data of only 2 months data. There features were computed on each day basis to account for the
variation of data over time. There are `20` features extracted from raw log data. The featured dataframe is then used to train 
5 binary classification algorthms. The best model is evaluate based on `F1-score` metric. 
**Based on the comparision of metrics of the models,** `Random Forest` **proved to be better in all metrics and with highest** `F1-score` **of** `32%` 

There is also need to do further work on hyperparameter tuning to improve the `F1-score`. Because of limited resources the models are trained with 
`mini_sparkify_event_data.json`. There is a need to train all choosen models with a larger dataset. Then the model also needs to be 
pacakged as class and pacakges for deployment.

### Constrains

> * Though Initially set out to work with **medium-sparkify-event-data.json** on `IBM Watson studio`, by the end of data analysis phase, I have exhaustedv the processing units of Free tier. So I had to continue the experiment on `Udacity workspace` with smaller dataset **mini-sparkify-event-data.json**. The results of the experiment is based on this smaller set of data.

> * At a larger scale the same set of alorithms can be used to bigger dataset **medium-sparkify-event-log.json** at distributed processing facility like `AWS`,`IBM Watsom Studio` or `Azure` Cloud. But because of the resourse contraints and assoicated cost of using Public cloud computing platforms `Udatacity Workspace` is used. 

### Further Improvements Suggested.
The results are not perfect and there is scope for much improvement. Some of the improvement I can thing about are

> * Only numerical statistics of users are used as features. There is scope to use the Catergorical value like `Gender`, `State`, `UserAgents`, `OS` in building more number of features. These categoical variables have to be numerically encoded and used as features.
> * There is scope to choose other Binary classification algorithms for model evaluation, One such algorithm not used here is `Decision Tree` which can also be used.
> * Each model can be trained with a large set of `Hyper Parameters` using the model `CrossValidator`.
> * Use a larger dataset like  **medium-sparkify-event-log.json** are if available, even larger dataset on machine learning cloud platfrom.
> * The methods used in the Modelling notebook can be packaged as `class` and `importable packages` for easier implemenetation/ integration with web GUI.

 ------------------
Thank you - [Suresh](https://www.linkedin.com/in/sureshbabu-kannan-15228657/)
