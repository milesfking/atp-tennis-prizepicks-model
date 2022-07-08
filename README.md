# ATP Tennis Tour Prediction Model

Using data sourced from [Jeff Sackmann's ATP Tennis Rankings Database](https://github.com/JeffSackmann/tennis_atp), my goal is to create a machine learning model to predict the outcomes of matches on the ATP (Association of Tennis Profssionals) Tour. Specifically, the response variable of interest is the number of fantasy points scored by a given player on the popular daily fantasy sports site PrizePicks. Utilizing data from over 50,000 ATP matches between 1997 and 2021, I aim to build a probabilitstic machine learning model to increase my odds of accurately predicting whether a player will overperform or underperform the value set by PrizePicks.

*Note: Pursuant to the Sackmann's [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/), I will not attempt to profit from the model. Success will instead be evaluated by placing hypothetical over/under bets and recording the results on an Excel spreadhseet.

*This is an ongoing project and the repository will continue to be updated as progress is made.

## About PrizePicks

PrizePicks allows users to pick the over or under on player props across dozens of sports, one of which is tennis. Depending on the tournament, PrizePicks lists over/under lines for a number of categories which can be accessed online on the [PrizePicks](https://www.prizepicks.com) website or pulled directly into Python via the [PrizePicks API](https://github.com/PrizePicks-Analytics/PrizePicks-API/wiki). Below is a list of props and how they are calculated.

### Aces

Total number of aces (legal serves that are not touched by the receiver) by the player throughout a match.

### Total Games

Total number of games played in a match. Tennis matches typically consist of three or five sets where each set is played to six games.

### Fantasy Score

A player's fantasy score is an arbitrary number calculated by the following individual stats:
  - Match Played: 10 pts
  - Game Win:	1 pts
  - Game Loss: -1 pts
  - Set Won: 3 pts
  - Set Loss:	-3 pts
  - Ace: 0.5 pts
  - Double Fault: -0.5 pts

## About the ATP Tennis Rankings Database

The ATP Tennis Rankings Database includes a plethora of match by match data including tournament and round info, player metadata, and in-match statistics. The data was cleaned to better fit the purposes of the model. Both the original Sackmann dataset (which is simply composed of all concatenated CSV files of the form `atp_matches_yyyy.csv` for years 1997 to 2021) and the cleaned dataset can be found in the data folder.

## Data Cleaning

The data, while robust, needed to be wrangled into a form more useful for modeling. This included the following:

### Fantasy Points Variable

As the original data was not compiled by Sackmann for PrizePicks purposes, a column needed to be created for our intended response variable, fantasy points. Mutating a fantasy points variable involved separating the `score` column, which listed the match scores as a string of set scores (e.g. '6-3 5-7 6-4') into both games won/lost and sets won/lost by a player. I then took a weighted average of these variables along with the player's aces and double faults to obtain their fantasy score, saved under the `fantasy_pts` column. Other basic data cleaning was performed during this process, including dropping matches that were forfeited and removing points won during tiebreak games (which do not count towards fantasy points) from the score.

### Restructuring the Dataset

Data in the original format was separated by the winning player and the losing player. In order to be able to predict the fantasy points of a player with just one variable, I changed the dataset from a "winner-loser" format to a "player-opponent" format by unnesting each match into two separate observations - one for each player. For instance, if Rafael Nadal plays Novak Djokovic, the newly formatted dataset would have one row to represent Nadal and one to represent Djokovic. While this did effectively double the number of rows of the dataset, it allowed me to condense the variable of interest into just one variable, rather than separate columns for `winner_fantasy_pts` and `loser_fantasy_pts`.

### Historical Stat Averages

Since data is entered on a match-by-match basis, there are no columns which offer historical averages for statistics of interest. As such, for each match, I iterated through the player's previous matches and computed averages for stats including `fantasy_pts`, `ace`, and `df` (double faults) up until that specific match. These averages were stored as `avg_fantasy_pts`, `avg_ace`, and `avg_df`. A similar process was repeated for matches where the two players had faced one another before, stored as `h2h_fantasy_pts`. An obviously flaw of this system is that all matches are weighted equally. In reality, more recent matches are likely far more indicative of player form, and this will be addressed in future iterations of the model.

### Missing Data

Missing data was plentiful and was handled according the category of the missing data. Unseeded players were assigned an arbitrarily low seed. Most tournaments only seed up to 8 players, while major tournaments can seed 32 players. I set the seed of unseeded players as 50 and plan to test an array of different values to find a better value.

First-time players without stat averages were assigned the average stats for all games played with first-time players. For instance, if Carlos Alcaraz has never played a match before, his `average_fantasy_pts` column would be populated by taking the average of true fantasy points for all newcomers in their first matches. 

If no previous matches were played between two players, the `h2h_fantasy_pts` column was populated with the player's value from the `avg_fantasy_pts` column, as we can only assume that the player's performance will be average against an opponent he has never faced before.

# Modeling

To determine what type of model would be the best at predicting a player's fantasy points, we fit four distinct models: a linear regression model, a penalized (lasso) regression model, a decision tree regressor, and a random forest regressor.

## Pre-processing

The cleaned dataset still includes a number of variables that will not be included in our model. To make our dataset ready for modeling, we began by select only significant variables, removing metadata (such as a player's name and country) in the process. The process for obtaining a dataset suitable for modeling also included converting categorical variables (`surface`, `binned_age`, and  `opp_binned_age`) to dummy variables.
