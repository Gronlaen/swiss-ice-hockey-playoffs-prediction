# Predicting Swiss Ice Hockey Playoffs using Machine Learning Models

## Introduction
Our goal in this project is to predict the outcome of playoff series of the Swiss ice hockey league.
Predicting the outcome of sports matches from statistics and performance data has always been an attractive but challenging task. Even more so in ice hockey where not much work has been devoted so far compared to other sports.

In hockey, as in many other sports, the best teams of the season go through a direct elimination tournament called the playoffs. To advance in the tournament, teams face each other through a best-of-seven series i.e. the first team to win four games wins the series.

There are two main leagues in Switzerland. The national league A (NL A), being the top tier league, and the national league B (NL B), the inferior division. Both of these leagues are composed of 12 teams each. During the regular season, each team plays 50 games. The top eight teams after the regular season qualify for the playoffs to determine the Swiss champion in best-of-seven series.

In this project, we tried to predict the outcome of the series instead of individual games, this is motivated by several factors. First of all randomness plays a huge role in the outcome of single game, a mathematically better team has a 24% chance of always beating an easier opponent. Secondly, this is harder to intuitively define the features for each game.
## Data
We gathered statistic from seasons 2008-2009 to 2015-2016 from the [Swiss Ice Hockey Federation](http://www.sihf.ch/fr/). We extracted the regular season data of each team for both the NL A and NL B. Our data generation pipeline is composed of two steps. First, we build feature vectors of regular season statistics for each team and each season, these statistics include:

* Year: the season
* GF: number of goals scored.
* GA: number of goals conceded.
* GF even: number of goals scored 5v5.
* GA even: number of goals conceded 5v5.
* PPG: number of goals scored in numerical superiority.
* PP GA: number of goals conceded in numerical superiority.
* SHG: number of goals scored in numerical inferiority.
* PK GA: number of goals conceded in numerical inferiority.
* SOWGF: number of goals scored in penalty shootouts.
* SOWGA: number of goals conceded in penalty shootouts.
* PP OP: number of situations in numerical superiority.
* PPT: time in numerical superiority.
* PP%: efficiency in numerical superiority.
* PK SI: number of situations in numerical inferiority.
* PKT: time in numerical inferiority.
* PK%: efficiency in numerical inferiority.
* GM: number of penalties for misconduct.
* MP: number of match penalties.
* 2': number of 2 min penalties.
* 5': number of 5 min penalties.
* 10': number of 10 min penalties.
* PIM total: total number of penalties.

We also have all of the preceding features per game. In total, this amounts to 34 features and 166 rows.

Secondly, we extracted the results of each game of the playoffs. We then aggregated those games to series and computed the winner. This second dataset has the following characteristics:

* Series_ID: a unique identifier of the season and the two teams involved e.g. `0910_HC Davos Kloten Flyers`
* Opponent 0/Opponent 1: the name of the teams involved.
* Winner: a label 0/1 depending on which team won the series.
* Homefield advantage: the name of the team that played the most at home during the series.

Since the top 8 teams face each other in the playoffs in each league, we have 7 series for each league and each season. This amounts to 112 observations in our dataset. 
Since we want to predict the winner of the series given both teams season statistics, we had to combine the two datasets in a smart way. We chose to compute the column wise difference between the feature vectors of each team involved in the series i.e.

`new_feature_vector = Opponent0_feature_vector - Opponent1_feature_vector`

and we append the label of the corresponding winner: 0 or 1.

We trained our models on seasons 08-09 until 14-15 and kept the playoffs of season 15-16 as our testing set.

## Method
After having preprocessed our data, all classfier parameters were chosen using grid-search and 5-fold cross-validation. Our results are reported here:

| Classifier | Mean Validation Accuracy (%)| std |
| :----: | :----: | :----: |
| Baseline | 50 | 0 |
| Random Forest | 57 | 0.08 |
| Logistic Regression | 61 | 0.14 |
| Neural Network | 66 | 0.07 |
| SVM | 71 | 0.1 |

Here is the learning curve obtained with our best performing model, SVM:
![Learning curve](learning_curve.png)


## Results
We
| Team 1 | Team 2 | Winner | Prediction |
| :----: | :----: | :----: | :--------: |
| SC Bern | ZSC Lions| SC Bern | ZSC Lions |
| HC Davos | Kloten Flyers | HC Davos | HC Davos |
| Genève-Servette HC | Fribourg-Gottéron | Genève-Servette HC | Genève-Servette HC |
| EV Zug | HC Lugano | HC Lugano | HC Lugano |
| SC Bern | HC Davos | SC Bern | SC Bern |
| Genève-Servette HC | HC Lugano | HC Lugano | HC Lugano |
| SC Bern | HC Lugano | SC Bern | SC Bern |

##Discussion (issues encountered, overfitting, ...)

##Future Outlook

##Conclusion

# ADA_project

##Abstract:
Sport is a very interesting domain, when it comes to Data Analysis. We wanted to use a clean Dataset, in order to extract real informations and being able to make accurate predictions. Because of the few choices of Sports with large Dataset in Switzerland, the best choice was Ice Hockey.

We want to exctract statistics from the Swiss Ice Hockey League in order to find interesting patterns and visualize them.
Once we'll have found the interesting features, we want to use them to predict the outcome of the upcoming games. (Using a predictive model)

##Data Description:
In this project we choose to use the data from the Swiss Ice Hockey League (http://www.sihf.ch/fr/game-center/national-league).
For each player of the league we have statistics about Goals/Assists, Shots, Penalties, Shootouts, Faceoffs and Time on Ice. 
For each team of the league we have statistics about Goals, Shots, Power Play, Box Play, Penalties, Shootouts and Spectator Attendance.
The data goes over 8 years and covers multiple leagues. We chose to focus on the main one, the LNA. This leagues is formed by 12 teams, for a total of more than 300 players.

##Feasibility and Risks
As all the data is accessible in CSV, we shouldn't have any problem parsing it. Nevertheless, we still don't know if the data is 100 percent correct or if there are missing or wrong entries.

We want to do good visualization, but we are new to the field. Thus it could be challenging to find the right tools to use, and how to use them.

Even if our visualization is good, the accuracy of the final predictions will only depend on the features we chose to use. Therefore the features extraction is the key to a good model. The risk comes from the fact that even if the method used is the good one, the accuracy of the prediction and therefore the viability of the model depends on how it is trained (which features are used).

##Deliverables

The visualizations will be available on a webpage, with all the documentation and explanations needed. We will also display the outcome of our predictions on a webpage.

##Time Plan
Estimated starting date : **mid of November**
- Data analysis : **end of November**
- Visualization : **early-mid of December**
- features extraction : **mid of December**
- model building : **early-mid of January**
- predicting outcomes : **mid of January**
- synthetisation of results : **end of January**
