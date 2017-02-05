# Predicting Swiss Ice Hockey Playoffs using Machine Learning Models

## Table of Contents

[Introduction](#Introduction)

[Data](#data)

[Method](#method)

[Results](#results)

[Discussion](#discussion)

[Conclusion](#conclusion)

[Future Outlook](#future-outlook)

[File Contents](#file-contents)

## Introduction
Our goal in this project is to predict the outcome of playoff series of the Swiss ice hockey league.
Predicting the outcome of sports matches from statistics and performance data has always been an attractive but challenging task. Even more so in ice hockey where not much work has been devoted so far compared to other sports.

In hockey, as in many other sports, the best teams of the season go through a direct elimination tournament called the playoffs. To advance in the tournament, teams face each other through a best-of-seven series i.e. the first team to win four games wins the series.

There are two main leagues in Switzerland. The national league A (NL A), being the top tier league, and the national league B (NL B), the inferior division. Both of these leagues are composed of 12 teams each. During the regular season, each team plays 50 games. The top eight teams after the regular season qualify for the playoffs to determine the Swiss champion in best-of-seven series.

In this project, we tried to predict the outcome of the series instead of individual games, this is motivated by several factors. First of all randomness plays a huge role in the outcome of single game, a mathematically better team has a 24% chance of always beating an easier opponent. Secondly, this is harder to intuitively define the features for each game.
## Data
We gathered statistics from seasons 2008-2009 to 2015-2016 from the [Swiss Ice Hockey Federation](http://www.sihf.ch/fr/). We extracted the regular season data of each team for both the NL A and NL B. Our data generation pipeline is composed of two steps. First, we build feature vectors of regular season statistics for each team and each season, these statistics include:

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

In order to check if it was relevant to use regular season stats to predict playoffs games, we did some exploratory data analysis to try to discover correlation between some statistics and the rank at the end of the season. We include here some of these plots:

![Comparison between playoffs ranking and regular season ranking](Plots/lna_PO_vs_RE.png)

The above plot is a scatter plot between the rank in regular season and rank in playoffs, the radius of the point symbolizes the number of times this rank happens. Although there is randomness, we can still notice a trend where teams first at the end the season are more likely to win the playoffs, conversely, bottom ranked teams are more likely to be eliminated from the playoffs straight away.

![feat-plot](Plots/features/____Ranking__vs__GF_GP.png)

This plot is a scatter plot of the number of goals scored per match against the ranking, we can observe a trend and correlation between the two. This is also indicative that these statistics could be helpful for our model.
We also did additional analysis but don't include it here to try to stay concise.

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
![Learning curve](Plots/learning_curve.png)



## Results
We used the NLA playoffs of the season 2015-12016 as our prediction testbed.

| Team 1 | Team 2 | Winner | Prediction |
| :----: | :----: | :----: | :--------: |
| SC Bern | ZSC Lions| SC Bern | **ZSC Lions** |
| HC Davos | Kloten Flyers | HC Davos | HC Davos |
| Genève-Servette HC | Fribourg-Gottéron | Genève-Servette HC | Genève-Servette HC |
| EV Zug | HC Lugano | HC Lugano | HC Lugano |
| SC Bern | HC Davos | SC Bern | SC Bern |
| Genève-Servette HC | HC Lugano | HC Lugano | HC Lugano |
| SC Bern | HC Lugano | SC Bern | SC Bern |

Using SVM, we correctly predicted 6 out of 7 playoffs series, which is pretty good. We were able to reproduce this result in 80% of the cases, the other 20% yielded 5/7.

## Discussion

During this project, we faced some issues and had to change our game plan due to several reasons.

Our first idea was to do clustering on players statistics to try to highlight their player types e.g. "enforcer", "play maker", "sniper" since we only know if they are defender or attacker. We lacked some key statistics in order to do so, e.g. the percentage of completed passes, the number of hits etc...

We first tried to make an analysis by player but we rapidly saw that the number of statistics for each player was too small to be used and not accurate to predict the outcome of a game. That's why we decided to work with the statistics of each team to predict the outcome of playoffs' games.

Working with teams instead of players reduces by a lot the cardinality of our dataset.
We need to train our models on the games of the playoffs, but as we have only have 7 series per year, if we only use the playoff in LNA we don't have enough data to train the model. In order to have more accurate models, we also included the series of the playoff in LNB, which doubled our dataset. Each model is trained on the playoffs from 2008 to 2014 and is tested on the playoffs 2015-2016. Our testing set contains only 7 series, which is quite small, so to be more accurate we made an average over many runs.


## Conclusion
We predicted the outcome of playoffs series for the Swiss Ice Hockey league by using regular season statistics and machine learning models. We first merged two datasets at our disposal, namely regular season statistics and playoffs games. Using this created dataset, we tried several models and retained SVM as our final model. Testing on the 15-16 LNA season, we correctly predicted 6 out of 7 series.

## Future Outlook

This could be interesting to check if we could apply the same method with other sports which have the same playofs format. We could also enhance our models with custom features based on the basic features we used, for example the percentage of possession, which we did not have at our disposal.

## File contents
* `Data`: data folder.
* `Deprecated`: contains the previous notebooks we used to try to analyze players.
* `Plots`: contains the plots for the exploratory data analysis.
* `Poster_slides.pdf`: the slides for our poster session.
* `ML.ipynb`: contains all the machine learning models tried, the hyperparamaters selection and the model validation.
* `Visualisation.ipynb`: contains all the functions used to plot the exploratory data analysis.
* `Team_Regular_Analysis.ipynb`: contains all the transformation we applied to the original dataset to obtain our training dataset. 
