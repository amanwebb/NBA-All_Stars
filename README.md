# NBA All-Star Predictions
## Introduction

Because Luka Doncic was snubbed from being listed as a starter this year (2021-2022), I wanted to see if he actually should be or if it is just my fandom that says he should.

Every year the NBA selects 24 of its star players to play in an exhibition "All-Star" game. This game is usually held in the middle of the season and is one of the most anticipated events of the year, serving as the marquee event of the otherwise uniform middle part of the season. 

Being selected as an All-Star is considered a very high honor, and the number of All-Star selections a player has is often one of the first statistics cited to demonstrate a player's greatness.

This project aims to answer the question of if player data can be used to predict who will be selected as an All-Star. It will do so by being trained on data from the 2016-2022 seasons and then making predictions for the 2022 season All-Star game. This will shed light on how predictive the data is for All-Star selctions, what factors matter most, and what players, if any, were snubbed in the 2022 season according to historical data.

I have a lot to do still with this to make it perfect, but it gives a good start on predicting these All-Stars based on in-game stats alone. I know that there are other factors like popularity and such, but I want to knwo who the statistical All-Stars are. Included in this code are basic stats like Games Startedd, Free throws, and Personal Fouls to name a few. I feel like this gives a good starting point to my quest of identifying the true statistical All-Stars.

One last thing I would like to point out would be that I do not yet have a part of the code where it differentiates Starters from Reserves. That is my next step, as that is what my primary goal was starting this project.

## Data Collection

I got all my data from scraping the Basketball Reference website https://www.basketball-reference.com/leagues/NBA_2022_per_game.html

Due to the nature of websites changing their format and such, this code may not work. Basketball Reference has been pretty uniform through the years on their layout, so I do not see any changes coming soon. This is just as a warning though. The last edit was February 7, 2022. As of this day, the code still scrapes correctly. 

This is how I scraped some of the years with a loop. This is the broad overview scraping and does not include the added parts found later in the code. For a full version (and much lengthier, due to fact that I am unsure how to do a loop scrape with All-Star selection for previous years) check the attached Python file.

```python
# Create an empty list of years
year = []

# loop through the years wanted and add to the list
x = range(2010, 2021)
for n in x:
  year.append(n)

# Create an empty listof dataframes
dfs = []
# Define URL
url = "https://www.basketball-reference.com/leagues/NBA_{}_per_game.html"
# Create a loop for each year and add to list
for i in year:    
    dframe = pd.read_html(url.format(i), header = None)[0]
    dfs.append(dframe)

# Combine all dataframes within the list
finaldf = pd.concat(dfs)
```
