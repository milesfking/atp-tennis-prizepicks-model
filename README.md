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

The ATP Tennis Rankings Database includes a plethora of match by match data including tournament and round info, player metadata, and in-match statistics. The data was cleaned to better fit the purposes of the model. Both the original Sackmann dataset and the cleaned dataset can be found in the data folder.

## Data Cleaning

The data, while robust, needed to be wrangled into a form more useful for modeling. This included the following:

### Fantasy Points Variable

As the original data was not compiled by Sackmann for PrizePicks purposes, a column needed to be created for our intended response variable, fantasy points. Mutating a fantasy points variable involved separating the `score` column, which listed the match scores as a string of set scores (e.g. '6-3 5-7 6-4') into both games won/lost and sets won/lost by a player. I then took a weighted average of these variables along with the player's aces and double faults to obtain their fantasy score.

