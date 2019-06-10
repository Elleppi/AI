## Machine Learning applied to IOTIC

### Problem Statement

In IOTIC we have 4 kinds of interactions: __Follow__, __Share__, __Control__ and __Controlled__.
Let's focus on Share (__S__) and Follow (__F__) which share and follow a feed (__f__) respectively:

     ______           |           ______ 
    |      |          |          |      |
    |  S   |---->f1   |   f1---->|   F  |
    |______|          |          |______|
                                         

In IOTIC we don't store data and __F__ follows a feed __f__ in a event-driven fashion. It means that __F__ receives __f__ if and only if it has been sent by __S__: if __S__ shares __f__ every *t = 10* seconds, __F__ will receive a new __f__ every *t = 10* seconds. So far so good as *t* appears to be relatively small. However, what happens for big values of *t* ? In other words, what happens when *t* is 12 hours or 7 days ? In this case __F__ will have to wait such time before receiving new data. This scenario might be disadvantageous for people or devices that want to know immediately the outcome of new shares.

#### Example 1

Suppose __S__ is an Iotic Thing that shares the number of bicycles available in London every *t = 1h*. Today is Wednesday and I would like to rent a bicycle for me and my family for this Sunday at 10AM. Knowing the number of bicycles in this moment (it's Wednesday) would be meaningless for me. What instead if there was the chance to know (estimate) in advance the number of bicycles available for Sunday at 10AM ?

#### Example 2

Suppose Rolls Royce (or other companies) have their Share Thing which shares the oil temperature of one of their engines at a rate of *t = 1h*. What if they want to know likely anomalies happening in 6 hours ?

### Solution

The idea is to develop a new kind of interaction: __Predict__ (__P__). The Predict interaction will be composed by a __Follow__ interaction followed by a __Share__ interaction:

            ______       
           |      |      
    f1---->|  P   |---->f2       where f1 != f2
           |______|      
                                                                               

By doing this, a user will be able to follow a particular feed (__f1__), predict (internally) its values for either the future *(i)th* share or a set of future shares [*(i)th*, *(i+1)th*, *(i+2)th*], and catch the new produced feed (__f2__) through a usual Follow interaction. The __Predict__ Iotic Thing will be therefore seen externally as a normal Share interaction with an API call similar to the following:

    predict(name_predict_thing, name_thing_to_follow, datetime_to_predict)

#### Solution applied to Example 1

The following will create a new Iotic Thing called *sunday_bicycles* which follows the *n_available* value of feed *bicycles* related to the *London_rent* Iotic Thing, and shares the estimated number of bicycles available for *Sunday_at_10AM* (in datetime object):

    predict("sunday_bicycles", London_rent.bicycles.n_available, Sunday_at_10AM)

#### Solution applied to Example 2

The following will create a new Iotic Thing called *oil_temperature_predictor* which follows the *temperature* value of feed *oil* related to the *engine* Iotic Thing, and shares the estimated value of temperature in 6 hours:

    predict("oil_temperature_predictor", engine.oil.temperature, datetime_now_+6h)

### How it works internally

#### Introduction

There are several issues to take in consideration when developing this project. In IOTIC the data is not stored and ML models make predictions based on historical data. Therefore the idea is to make use of a ML technique called Online Learning (OL). The latter follows a similar concept developed in Reinforcement Learning but with a different approach. Through the use of OL the data is not stored and the model can be trained (improved) at runtime. This means that as soon as new data becomes available, the OL model will be updated to the most recent data so that new predictions will get better and better. 

#### In practice

In IOTIC, a datetime object is associated to any feed and it travels with the feed itself. Some fields of such datetime can be used as features to train the ML model, such as *hour*, *day*, *month* and *day_of_the_week*. The feed's values will be used instead as labels of the ML model. By doing this, as soon as a new feed will be received by the Predict Iotic Thing, the aforementioned features and labels can be used to improve the ML model related to that Iotic Thing.

#### Which ML model is best

Unfortunately, there aren't many ML models available for Online Learning as it is quite a new field of ML. However, the Scikit-Learn library provides 3 useful models applicable to Online Learning: *MLPRegressor*, *PassiveAggressiveRegressor* and *SGDRegressor*. The first one is a Neural Network based model and can be used for non-linear regressions, whereas the last two can only be used for linear regressions. This means that the *MLPRegressor* appears to be the best choice for our case.

(TO BE COMPLETED)