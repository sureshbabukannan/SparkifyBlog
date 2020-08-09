# Why do Listeners hangup headphones over Sparkify?

![Dropping the headphone the customers could move on](/SparkifyBlog/Drophead_phone.jpg "Dropping the headphone the customers could move on")

## Overview

#### Sparkify is a fictious online Music portal for songs and music create by Udacity for DataScientist Capstone project turorial. 

>>> ![SparkifyLogo](/SparkifyBlog/Sparkify_Udacity.png "Sparkify Logo")

**Sparkify** users can listen to songs and music on the online portal as `Guest` user or `Registered` user.

But there is a problem. The of existing users are `cancelling` and leaving. Well, This a users choice. But it is not good for the bussiness. Sparkify needs the customers to stick on. 
As long as users are around there is possibility they could `upgrade and become paid` users. Even if they stick on as `free` level user, still there is some minimum revenue to be 
earned due to rolling Adverts.  

As per industry anslysis, The `cost of acquiring new customer is more than the maintaining an existing customers`. But only if we know in advance the customers could leave. 
The industry term for user subscription cancelling is called `User Churn` or `User Attrition`. 

The aim of this experiment is to predict the `user churn` using `Machine learning models`
based on `User activty event log` data on **medium-sparkify-event-log.json** dataset.

## The Data

Any machine learning experiment requires large enough input data to train the models for the ML models.  The Sparkify portal 
logs users activity of every actions of every user. This log is rich source of data to understand user behaviour. 
It is choronological sequence of timestamped event user did on Sparkify portal. We could understand the activity or 
behaviour of the user over time using the data. The event log has datapoints as follows.

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

> `User actions related to Music`
>> * The users can `listen`, `add a friend`, `thumps up`, `thumbs down` , `add to favourite` to a song.

> `User Financial Activities`
>> * `Upgrade` to paid subscription and `Upgrade` multiple levels of subscription,
>> * `Downgrade` to lower paid level `Downgrade` free level subscription. 
>> * `Users can also `Cancel` the subscription. 

> `Navigational activities`
>> `home` page visits, 
>> `Help` page visits,
>> `Login` 
>> `Logout` are also other user actions logged by sparkify poral.

### Exploring the Data

We set up by exploring and producting visuailations to understand the user activities. The log data has `2` months of user log.

<div class="output_html rendered_html output_subarea output_execute_result">
<style  type="text/css" >
</style><table id="T_967cee7a_d959_11ea_b619_dd81fbe6be86" >
    <thead><tr>        
        <th class="col_heading level0 col0" >start_date</th>        
        <th class="col_heading level0 col1" >end_date</th>    
    </tr>
    </thead>
    <tbody><tr>
        <td id="T_967cee7a_d959_11ea_b619_dd81fbe6be86row0_col0" class="data row0 col0" >2018-10-01 00:00:11</td>
        <td id="T_967cee7a_d959_11ea_b619_dd81fbe6be86row0_col1" class="data row0 col1" >2018-12-01 00:01:06</td>
    </tr>
    </tbody></table>
</div>

There are are `99` cancelled users out of `499` users which is around `19.8%`

The following graph shows `weekly metrics` of users who are `cancelled` and `Active` with in the time period of log 
This is done to identify the metrics that can be used as features to training the machine learning model.

#### `Per Cancelled user Weekly song listening time` vs `Per Active user Weekly song listening time`

The amount of time user spends listing to Song on Sparkify can be important factor of attrition, the graph below shows how much time user spent listening to songs.

![Weekly song listening time - Cancelled vs Active user](/SparkifyBlog/Listen_Time.png "Weekly song listening time - Cancelled vs Active user")

The paid user seems to spend more time as expected. But it is surprising to see per cancelled users send more time listening to song in a week than per registered user especially in the last week.

#### `Propotional Weekly number of Sessions - Cancelled vs Active Users`

The number times in day or week  user choose to spend time listening in Sparkify can be factor too.
The graph below shows how many session user had in Sparkify per week.

![Propotional Weekly number of Sessions - Cancelled vs Active Users](/SparkifyBlog/Sessions.png "Propotional Weekly number of Sessions - Cancelled vs Active Users")

The `paid` user seems to have more number of session per week as compared to free user as the graph 2. But treand is not very evdient with cancelled users.

#### `Weekly number of Thumbs Up, Thumbs Down, Add Friends per Cancelled user vs Active user`

The thumps up, thumbs Down or Add Friends shows the engagement level of user with Sparkify and how much the like the content. The graph show the counts of thumbs Up, thumbs Down and add Friend for Active and cancelled users.

![Weekly number of Thumbs Up, Thumbs Down, Add Friends per Cancelled user vs Active user](/SparkifyBlog/Metrics.png "Weekly number of Thumbs Up, Thumbs Down, Add Friends per Cancelled user vs Active user")

The above graphs shows Cancelled users and Active user are having faily same level of thumbs up and thumbs downs. cancel users have relatively added more friends than active users. This could be they are more enagaged with there friends and sparkify, As they are more engaged they  expect more from performace Sparkify portal.

#### `Weekly number of page navigation per Cancelled user vs Active user`

The number page visited shows the amount of search or exploration user does to find the content on Sparkify. The graph below shows if for Active and cancelled users.

![Weekly number of page navigation per Cancelled user vs Active user](/SparkifyBlog/navigation.png "Weekly number of page navigation per Cancelled user vs Active user")

The number of items navigated per weeks trend is clear for `free` and `paid` active users. But the tread is not clearly seen for `cancelled` users.

#### `Weekly number of Advertiments per Cancelled user vs Active user`

The number advertisments could be impartant measure of user getting feed up due to more time in advertisments.

![Weekly number of Advertiments per Cancelled user vs Active user](/SparkifyBlog/advert.png "Weekly number of Advertiments per Cancelled user vs Active user")

Looks like the cancelled users have got more adverts that active users. This is possible because of more time they have spent and 
overall more songs they have listen the remaining active users.

#### `Weekly number of Help and Save settings page vists per Cancelled user vs Active user`

The help page and Save setting visits shows how much users need further information to improve their listening experience.

![Weekly number of Help and Save settings page vists per Cancelled user vs Active user](/SparkifyBlog/Help_Settings.png "Weekly number of Help and Save settings page vists per Cancelled user vs Active user")

Clearly the tread show `Active` users both free or paid used `Help` pages more often than `cancelled` users. 
`Save setting` seems to be almost of same frequency with `cancelled`and `active` user.

#### `Effect of OS and Broswser on User song listening experienence`

There could be other technical factor like User device, OS , Browser have effect on song listing experience of the users which could also lead to user cancellation.

![Effect of OS and Broswser on User song listening experienence](/SparkifyBlog/OS_Browser.png "Effect of OS and Broswser on User song listening experienence")

We could see that , there are more cancellation in `Ubuntu-Safari` combination. `Windows-Trident` combination has least Downgrades. 
As we could see there is quite a variation on `OS-Browser` combination this can be used as features.

**Table showing number of Cancelled user by number of user of OS**
<div class="output_html rendered_html output_subarea output_execute_result">
<table>
    <tr style="border-bottom: 1px solid">
        <td style="font-weight: bold">OS</td>
        <td style="font-weight: bold">Cancelled user</td>
        <td style="font-weight: bold">OS User</td>
        <td style="font-weight: bold">Cancelled user per OS user</td>
    </tr>
    <tr>
        <td>Windows</td>
        <td>48</td>
        <td>222</td>
        <td>4.625</td>
    </tr>
    <tr>
        <td>Macintosh</td>
        <td>46</td><td>200</td>
        <td>4.3478260869565215</td>
    </tr>
    <tr>
        <td>Ubuntu</td>
        <td>5</td>
        <td>26</td>
        <td>5.2</td>
    </tr>
</table>
</div>

**Table showing number of Cancelled user by number of user of browser**
<div class="output_html rendered_html output_subarea output_execute_result">
<table>
    <tr style="border-bottom: 1px solid">
       <td style="font-weight: bold">Browser</td>
       <td style="font-weight: bold">Number of Cancelled User</td>
       <td style="font-weight: bold">Number of Browser User</td>
       <td style="font-weight: bold">Cancelled User per Browser</td>
    </tr>
    <tr>
        <td>Safari</td>
        <td>66</td>
        <td>318</td>
        <td>4.818181818181818</td>
        </tr><tr><td>Firefox</td>
        <td>31</td><td>117</td>
        <td>3.774193548387097</td>
    </tr>
    <tr>
        <td>Trident</td>
        <td>2</td>
        <td>13</td>
        <td>6.5</td>
        </tr>
</table>
</div>

**Table showing number of Cancelled user by number of user of combination of OS-Browser**
<div class="output_html rendered_html output_subarea output_execute_result">
<style  type="text/css" >
</style><table id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86" ><thead>    <tr>        <th class="col_heading level0 col0" >OSBrowser</th>        <th class="col_heading level0 col1" >OS_Browser_Can_user</th>        <th class="col_heading level0 col2" >OS_Browser_user</th>        <th class="col_heading level0 col3" >Can_user_per_OSBrowser</th>    </tr></thead><tbody>
                <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row0_col0" class="data row0 col0" >Ubuntu-Firefox</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row0_col1" class="data row0 col1" >2</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row0_col2" class="data row0 col2" >13</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row0_col3" class="data row0 col3" >6.5</td>
            </tr>
            <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row1_col0" class="data row1 col0" >Windows-Trident</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row1_col1" class="data row1 col1" >2</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row1_col2" class="data row1 col2" >13</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row1_col3" class="data row1 col3" >6.5</td>
            </tr>
            <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row2_col0" class="data row2 col0" >Windows-Safari</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row2_col1" class="data row2 col1" >22</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row2_col2" class="data row2 col2" >128</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row2_col3" class="data row2 col3" >5.81818</td>
            </tr>
            <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row3_col0" class="data row3 col0" >Macintosh-Firefox</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row3_col1" class="data row3 col1" >5</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row3_col2" class="data row3 col2" >23</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row3_col3" class="data row3 col3" >4.6</td>
            </tr>
            <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row4_col0" class="data row4 col0" >Ubuntu-Safari</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row4_col1" class="data row4 col1" >3</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row4_col2" class="data row4 col2" >13</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row4_col3" class="data row4 col3" >4.33333</td>
            </tr>
            <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row5_col0" class="data row5 col0" >Macintosh-Safari</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row5_col1" class="data row5 col1" >41</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row5_col2" class="data row5 col2" >177</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row5_col3" class="data row5 col3" >4.31707</td>
            </tr>
            <tr>
                                <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row6_col0" class="data row6 col0" >Windows-Firefox</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row6_col1" class="data row6 col1" >24</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row6_col2" class="data row6 col2" >81</td>
                        <td id="T_ac8dec12_d95b_11ea_b619_dd81fbe6be86row6_col3" class="data row6 col3" >3.375</td>
            </tr>
    </tbody></table>
</div>

## Feature Selection
Based of the **Exploratory Data Analsysis** all meterics analysied is taken per user.  The features extract will produce a feature dataframe with `499`
feature vector same as the number of users.

All the meteric analysed in EDA is taken as numeric features and `OS-Browser` combination categoical features with 7 distinct values. 
The OS-Browser value is numeric encoded and 7 feature columns are added. 
The following are the full list of features used.
```
root
 |-- userId: string (nullable = true)
 |-- isCancelled: long (nullable = true)
 |-- listenTime: double (nullable = false)
 |-- sessions: long (nullable = false)
 |-- numOfItems: long (nullable = false)
 |-- numOfSongs: long (nullable = false)
 |-- thumbsUp: long (nullable = false)
 |-- thumbsDown: long (nullable = false)
 |-- upgrade: long (nullable = false)
 |-- rollAdvert: long (nullable = false)
 |-- advert_seconds: double (nullable = false)
 |-- downgrade: long (nullable = false)
 |-- addFriend: long (nullable = false)
 |-- addToPlaylist: long (nullable = false)
 |-- numOfArtist: long (nullable = false)
 |-- numOfUniqSongs: long (nullable = false)
 |-- sumSongRank: long (nullable = false)
 |-- sumArtistRank: long (nullable = false)
 |-- upgradeToCancelDays: double (nullable = false)
 |-- downgradeToCancelDays: double (nullable = false)
 |-- avgDowngradeDays: double (nullable = false)
 |-- avgUpgradeDays: double (nullable = false)
 |-- helpVisits: long (nullable = false)
 |-- SaveSettings: long (nullable = false)
 |-- Windows_Firefox: integer (nullable = true)
 |-- Macintosh_Firefox: integer (nullable = true)
 |-- Macintosh_Safari: integer (nullable = true)
 |-- Windows_Safari: integer (nullable = true)
 |-- Ubuntu_Firefox: integer (nullable = true)
 |-- Ubuntu_Safari: integer (nullable = true)
 |-- Windows_Trident: integer (nullable = true)

```

**A preview of feature dataset is as shown below**
-- `UserID` followed by `isCancelled` as classification label column followed by `29` feature values.
<div class="output_wrapper">
<div class="output">
<div class="output_area">
<div class="output_subarea output_stream output_stdout output_text">
<pre>
+------+-----------+------------------+--------+----------+----------+--------+----------+-------+----------+--------------+---------+---------+-------------+-----------+--------------+-----------+-------------+-------------------+---------------------+------------------+--------------------+----------+------------+---------------+-----------------+----------------+--------------+--------------+-------------+---------------+
|userId|isCancelled|        listenTime|sessions|numOfItems|numOfSongs|thumbsUp|thumbsDown|upgrade|rollAdvert|advert_seconds|downgrade|addFriend|addToPlaylist|numOfArtist|numOfUniqSongs|sumSongRank|sumArtistRank|upgradeToCancelDays|downgradeToCancelDays|  avgDowngradeDays|      avgUpgradeDays|helpVisits|SaveSettings|Windows_Firefox|Macintosh_Firefox|Macintosh_Safari|Windows_Safari|Ubuntu_Firefox|Ubuntu_Safari|Windows_Trident|
+------+-----------+------------------+--------+----------+----------+--------+----------+-------+----------+--------------+---------+---------+-------------+-----------+--------------+-----------+-------------+-------------------+---------------------+------------------+--------------------+----------+------------+---------------+-----------------+----------------+--------------+--------------+-------------+---------------+
|100010|          1|25870.950739999997|       2|       137|        96|       4|         3|      2|        22|        4584.0|        0|        3|            1|         94|            96|        -96|          -96|0.06645833333333333|                  0.0|               0.0|8.101851851851852E-5|         0|           0|              0|                0|               1|             0|             0|            0|              0|
|200002|          1| 78695.82683999998|       5|       395|       310|      15|         5|      2|        11|        1717.0|        3|        2|            6|        287|           306|       -310|         -310| 23.444675925925925|   17.436921296296298| 8.637135416666666|    5.91644675925926|         1|           0|              0|                0|               1|             0|             0|            0|              0|
|   296|          1|28314.150059999996|       5|       152|       112|       8|         0|      1|         9|        1217.0|        1|        2|            3|        108|           109|       -112|         -112|  6.081342592592593| 1.157407407407407...|               0.0|                 0.0|         2|           0|              0|                0|               1|             0|             0|            0|              0|
|   125|          0|15813.771070000003|       3|        84|        62|       3|         1|      0|         6|           0.0|        0|        3|            2|         61|            62|        -62|          -62|                0.0|                  0.0|               0.0|                 0.0|         2|           0|              0|                0|               1|             0|             0|            0|              0|
|   124|          1| 456664.0405799998|      17|      2156|      1826|     102|        15|      0|         1|         142.0|       21|       26|           45|       1240|          1611|      -1826|        -1826|                0.0|    40.98420138888889|2.0492094907407403|                 0.0|        10|           3|              0|                0|               1|             0|             0|            0|              0|
|    51|          1| 66817.21162999999|       5|       328|       266|      16|         1|      0|         1|         136.0|        4|        3|            8|        233|           253|       -266|         -266|                0.0|   15.399166666666666| 5.133051697530864|                 0.0|         0|           0|              0|                0|               0|             1|             0|            0|              0|
|     7|          0|      115987.01665|      15|       600|       462|      12|         4|      6|        35|           0.0|        0|       16|            6|        407|           447|       -462|         -462|                0.0|                  0.0|               0.0|   4.262210648148147|         3|           2|              1|                0|               0|             0|             0|            0|              0|
|200037|          1| 30116.43781000001|       7|       155|       122|       5|         3|      0|         9|           0.0|        0|        2|            4|        114|           120|       -122|         -122|                0.0|                  0.0|               0.0|                 0.0|         0|           0|              0|                0|               1|             0|             0|            0|              0|
|   169|          0|       28033.43819|       8|       149|       112|       6|         0|      2|        13|           0.0|        0|        6|            0|        111|           112|       -112|         -112|                0.0|                  0.0|               0.0| 0.00568287037037037|         0|           1|              0|                0|               1|             0|             0|            0|              0|
|   205|          0|263650.81615999993|       5|      1244|      1048|      50|        10|      0|         0|           0.0|       10|       21|           25|        803|           966|      -1048|        -1048|                0.0|                  0.0| 5.304983281893006|                 0.0|        11|           2|              0|                0|               1|             0|             0|            0|              0|
+------+-----------+------------------+--------+----------+----------+--------+----------+-------+----------+--------------+---------+---------+-------------+-----------+--------------+-----------+-------------+-------------------+---------------------+------------------+--------------------+----------+------------+---------------+-----------------+----------------+--------------+--------------+-------------+---------------+
</pre>
</div>
</div>
    </div>
</div>

First `10` row of the feature dataset is shown

## Meaning of features

##### Numerical Features

`isCancelled` field is marked as `0` if Acitve user ( user who dont have `Cancel` event page ) and `1` for cancelled user. This serves as label for machine learning.
> * **Listening time in minitues** - representing Time spent in listening to songs
> * **Number of sessions** - How often user logoff and logged 
> * **Number of items in session** - How much user was navigating
> * **Number of songs** - Number of song user listen, this is different for how long because use could just play few minutes of the song and skip to nextsong
> * **Number of thumbs up**
> * **Number of thumbs downs**
> * **Number of upgrades**
> * **Number of roll adverts**
> * **Number of downgrades**
> * **Number of add friends**
> * **Number of Help page visits**
> * **Number of Save setting visits**
> * **Number of Add to Favourite**
> * **Number of add to playlist**
> * **Number of distinct artist**
> * **Number of distinct songs**
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

##### Categorical Feature

The `7` features columns correspond to `7` values of `OS-Browser`
> * **Windows-Firefox**
> * **Macintosh-Firefox**
> * **Macintosh-Safari**
> * **Windows-Safari**
> * **Ubuntu-Firefox**
> * **Ubuntu-Safari**
> * **Windows-Trident**

## Machine Learning modelling 

It is about **applying statistical methods** over very large volume of data or **big data**. 
When we say **Big data** the data which cannot fit into one single computers memory and hence had to be split into multiple chunks 
,stored and processed seperately on multiple comuters. The results of the processing is assembled back into single unit by driver 
computer.  This is roughly how we can describe the **distributed processing**

If you like, You can find a interesting read on machine learning to start to understand [here](https://vas3k.com/blog/machine_learning/).

The ML model is run on `pySpark 3.6` on **IBM Watson Studio**  distributed comptute platform.

### Algorithms

The goal is to predict the value of the label column, in this case `isCancelled` which takes a value `0` or `1`. 
We have defined label which takes 2 values. So this is a `Supervised Learning - binary classification` problem. 

The following binary classfication algorithms are evaluated to choose a best model 
>> * **Logisctic regression**
>> * **Random Forests**
>> * **Gradient-Boosted Trees**
>> * **Decision Tree Classifier**

### Modelling 

> * First the features dataframe is taken and feature vectors are formed, Code snippit showing `vectorAssembler` below

```
vectAssembler = VectorAssembler(
    inputCols=['listenTime', 'sessions', 'numOfItems', 'numOfSongs', 'thumbsUp',
               'thumbsDown', 'upgrade', 'rollAdvert', 'advert_seconds', 'downgrade',
               'addFriend', 'addToPlaylist', 'numOfArtist', 'numOfUniqSongs',
               'sumSongRank', 'sumArtistRank', 'upgradeToCancelDays',
               'downgradeToCancelDays', 'avgDowngradeDays', 'avgUpgradeDays',
               'helpVisits', 'SaveSettings', 'Windows_Firefox', 'Macintosh_Firefox',
               'Macintosh_Safari', 'Windows_Safari', 'Ubuntu_Firefox',
               'Ubuntu_Safari', 'Windows_Trident' ],
    outputCol="featVector",
    handleInvalid="keep"
    )

features = vectAssembler.transform(features)
```

> * The feature vector dataset is split into 70/30 proporation as train and test datasets.

```
train, test = features.select(col('isCancelled').alias('label'),       \
                              col('featVector').alias('features')) \
              .randomSplit([0.7, 0.3], seed=77)
```

> * Each Algorithm is fittes to train dataset using CrossValidator. The `CrossValidator` is used as a
>> * Randomly splits the input data into test and train internally.
>> * Using each the combination of hyperparameter in `param_grid` fits the data to the model. 
>> * Repeat the above steps for number of iteration specified.
>> * for example, if there are 3 sets of parameter each with 2 values in set. the crossvaidator will fit the data (3* 2 * 3 * 3 ( number of iteration time)) = 48 times
This way we makes sure the output model is based on best hyperparameter for value for given set of hyperparameters. Code snippit showing CrossValidator definition.

```
Evaluated_model = CrossValidator(estimator=model_in,
                                     estimatorParamMaps=param_grid,
                                     evaluator=BinaryClassificationEvaluator(),
                                     numFolds=3,
                                     seed=77
                                    )
```

>> * The `Crossvalidator` picks the best comibination of `hyperparameter` of the model and the results are evaluate by `BinaryClassificationEvaluator`
    
#### **HyperParameter Grid passed to CrossValidator and Resultant Metrics**

The Model are trained by crossvaidator based param_grid passed. The trained model is used to predict the label from `test` dataset, the data model has 
not seen before.

##### **Logisctic regression**

> * **Parameters :**

```
param_grid = ParamGridBuilder()                     \
           .addGrid(lr.regParam,[0.01, 0.1])        \
           .addGrid(lr.elasticNetParam,[0.0, 0.5])  \
           .addGrid(lr.aggregationDepth,[2, 5])     \
           .build()
```

> * **Resultant Metrics** 
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training</th>
      <td>0.864516</td>
      <td>0.116667</td>
      <td>0.400000</td>
      <td>0.180645</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.891304</td>
      <td>0.128440</td>
      <td>0.482759</td>
      <td>0.202899</td>
    </tr>
  </tbody>
</table>
</div>

##### **Random Forests**

> * **Parameters :**

```
param_grid = ParamGridBuilder()                                       \
            .addGrid(rfc.maxDepth,[5, 10])                            \
            .addGrid(rfc.impurity,['entropy', 'gini'])                \
            .addGrid(rfc.maxBins ,[10, 5])                            \
            .addGrid(rfc.numTrees ,[20, 10])                          \
            .addGrid(rfc.featureSubsetStrategy ,['sqrt', 'onethird']) \
            .build()
```

> * **Resultant Metrics** 

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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training</th>
      <td>0.993548</td>
      <td>0.283333</td>
      <td>0.971429</td>
      <td>0.438710</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.992754</td>
      <td>0.256881</td>
      <td>0.965517</td>
      <td>0.405797</td>
    </tr>
  </tbody>
</table>
</div>
</div>

##### **Gradient-Boosted Trees**

> * **Parameters :**

```
param_grid = ParamGridBuilder()               \
            .addGrid(gbt.maxDepth,[3, 5, 10]) \
            .addGrid(gbt.maxBins ,[10, 5])    \
            .addGrid(gbt.maxIter ,[20, 5])    \
            .build()
```

> * **Resultant Metrics** 

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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training</th>
      <td>0.974194</td>
      <td>0.258333</td>
      <td>0.885714</td>
      <td>0.400000</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.992754</td>
      <td>0.256881</td>
      <td>0.965517</td>
      <td>0.405797</td>
    </tr>
  </tbody>
</table>
</div>
</div>

##### **Decision Trees Classifier**

> * **Parameters :**

```
param_grid =ParamGridBuilder()                           \
            .addGrid(dtc.maxDepth,[3, 5, 10])          \
            .addGrid(dtc.impurity,['entropy', 'gini']) \
            .build()
```

> * **Resultant Metrics** 

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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training</th>
      <td>0.980645</td>
      <td>0.279167</td>
      <td>0.957143</td>
      <td>0.432258</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.963768</td>
      <td>0.256881</td>
      <td>0.965517</td>
      <td>0.405797</td>
    </tr>
  </tbody>
</table>
</div>
</div>

### **Metrics to Evaluate the ML Models**

The dataset taken for analyis is imbalanced dataset with only around `23%` cancelled user. As the dataset is slightly imbalanced 
towards active customers. `F1-score` or `Harmonic Mean` is as used a primary metrics to evaluate the model. 
Validation data is used for prediction of `IsCancelled` value of test dataset. Model with the best `F1-Score` can be used 
for further hyperparameter tuning.

#### **Resultant metric of all 4 models**

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
      <td>0.864516</td>
      <td>0.116667</td>
      <td>0.400000</td>
      <td>0.180645</td>
      <td>Logistic Regression</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.891304</td>
      <td>0.128440</td>
      <td>0.482759</td>
      <td>0.202899</td>
      <td>Logistic Regression</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.974194</td>
      <td>0.258333</td>
      <td>0.885714</td>
      <td>0.400000</td>
      <td>Gradient-Boosted Trees</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.992754</td>
      <td>0.256881</td>
      <td>0.965517</td>
      <td>0.405797</td>
      <td>Random Forest</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.992754</td>
      <td>0.256881</td>
      <td>0.965517</td>
      <td>0.405797</td>
      <td>Gradient-Boosted Trees</td>
    </tr>
    <tr>
      <th>Validation</th>
      <td>0.963768</td>
      <td>0.256881</td>
      <td>0.965517</td>
      <td>0.405797</td>
      <td>Decision Tree Classifier</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.980645</td>
      <td>0.279167</td>
      <td>0.957143</td>
      <td>0.432258</td>
      <td>Decision Tree Classifier</td>
    </tr>
    <tr>
      <th>Training</th>
      <td>0.993548</td>
      <td>0.283333</td>
      <td>0.971429</td>
      <td>0.438710</td>
      <td>Random Forest</td>
    </tr>
  </tbody>
</table>
</div>
</div>

We could see that `Decision Tree Classifier` and `Gradient Boosted Trees` both perform better with `0.40` **F1 score** on validation set. 
There is scope for more hyperparameter tuning and train the model on larger dataser and using more number of features.

#### Comparision of Metrics of Machine Learning Models

![Comparision of metrics of ML Models](/SparkifyBlog/Model_Compare.png "Comparision of metrics of ML Models")

## Conclusion

The experiment is done with `2` months data. The features were engineered on `per user basis`. There are `29` features extracted from 
raw log data. The featured dataframe is then used in ML modelling.  `4` binary classification algorthms are evaluated and model
with best `F1` score is used of prediction. Out of 4 models `Gradient Boosted tree` and `Decision Trees Classifier` both proved 
to be better in all metrics, with F1 score of `0.40` on validation data and `0.43` on training data. `Decision Trees Classifier` is more 
cost effective than `Gradient Boosted tree`, so can be used for prediction. The model can be improved by using more hyperparameter tuning 
with boarder set of parameters and fitting with larger dataset.

### Constrains

> * Limited availabity of data and compute resourses. There is always scope for further improvement due to this constraints.

### Further Improvements Suggested.

The results are not perfect and there is scope for improvement. Some of the improvement I can thing about are

> * Use more Catergorical features based on `Gender`, `State`, Song, Artist etc. 
> * Think about more numberic of features. 
> * Each model can be trained with a large set of `Hyperparameters` using the model `CrossValidator`.
> * Use even larger dataset than `medium-sparkify-event-log.json`, if available.
> * The methods used in the Modelling notebook can be packaged as `class` and `importable packages` 
for easier implemenetation/ integration with web GUI.

 ------------------
Thank you :)
> [Suresh](https://www.linkedin.com/in/sureshbabu-kannan-15228657/)