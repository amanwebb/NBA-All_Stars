# NBA All-Star Predictions
## Introduction

Because Luka Doncic was snubbed from being listed as a starter this year (2021-2022), I wanted to see if he actually should be or if it is just my fandom that says he should.

Every year the NBA selects 24 of its star players to play in an exhibition "All-Star" game. This game is usually held in the middle of the season and is one of the most anticipated events of the year, serving as the marquee event of the otherwise uniform middle part of the season. 

Being selected as an All-Star is considered a very high honor, and the number of All-Star selections a player has is often one of the first statistics cited to demonstrate a player's greatness.

This project aims to answer the question of if player data can be used to predict who will be selected as an All-Star. It will do so by being trained on data from the 2016-2022 seasons and then making predictions for the 2022 season All-Star game. This will shed light on how predictive the data is for All-Star selctions, what factors matter most, and what players, if any, were snubbed in the 2022 season according to historical data.

I have a lot to do still with this to make it perfect, but it gives a good start on predicting these All-Stars based on in-game stats alone. I know that there are other factors like popularity and such, but I want to knwo who the statistical All-Stars are. Included in this code are basic stats like Games Startedd, Free throws, and Personal Fouls to name a few. I feel like this gives a good starting point to my quest of identifying the true statistical All-Stars.

One last thing I would like to point out would be that I do not yet have a part of the code where it differentiates Starters from Reserves. That is my next step, as that is what my primary goal was starting this project.

## Data Collection

I got all my statistic data from scraping the Basketball Reference website https://www.basketball-reference.com/leagues/NBA_2022_per_game.html

Due to the nature of websites changing their format and such, this code may not work. Basketball Reference has been pretty uniform through the years on their layout, so I do not see any changes coming soon. This is just as a warning though. The last edit was February 7, 2022. As of this day, the code still scrapes correctly. 

This is how I scraped some of the years with a loop. This is the broad overview scraping and does not include the added parts found later in the code. For a full version (and much lengthier, due to fact that I am unsure how to do a loop scrape with All-Star selection for previous years) check the attached Python file. For All-Star selection in previous years, I simply googled and added the information manually.

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
## Modeling

I started with creating a list of numerical variables, then filled missing values with a zero. From there, I split the data into training and testing sets. I made it an .80 training size, obviously with the result variable being All-Star.

```python
# Split the data into training and testing

X_train, X_test, y_train, y_test = train_test_split(results[xcols], 
                                                    results['All-Star'], 
                                                    train_size = 0.8, 
                                                    random_state = 1)
print('training data:', X_train.shape)
print('test data:', X_test.shape)
```

I fit a logistic regression model with 3000 iterations to assure the validity of this model. I came out with about a 98% accuracy score. I listed the top and worst coefficients to help identify the most influential in prediciting All-Stars. I added the top five and the bottom five coefficients to a new list. I will use these in the next test/train set.

```python
log_reg = LogisticRegression(solver = 'lbfgs', max_iter = 3000)

# Fit the model to the training data
clf = log_reg.fit(X_train, y_train)

print('training accuracy: {}'.format(clf.score(X_train, y_train).round(3)))
print('test accuracy: {}'.format(clf.score(X_test, y_test).round(3)))
```

Next, was somee regularization and cross validating. The next little bit of code includes recall, precision, a confusion matrix, etc. This helps identify a little more accuracy and such. 

```python
# Confusion matrix

cm = confusion_matrix(y_test, y_pred_test)
sample = np.array([['TN', 'FP'], ['FN', 'TP']])
print('CM key:\n', sample, '\n')
print('CM for test:\n', cm)
```

## Predicting

Now it is finally time to do some predicting! We will start by setting up the dataframe. This will include only taking the important stats (top and bottom five variables). This will help us come out with more accuracy in the end, as we found out with the modeling code. 

```python
# only take the important stats we found earlier

player_stats_22_short = player_stats_22[['STL', 'BLK', 'DRB', 'PTS', '3P', 
                                         '2P', '3PA', 'TOV', 'PF', 'ORB']]
```

![image](https://user-images.githubusercontent.com/81338261/153013027-45fab31c-d9f9-46ac-97fe-0a8b4c0b4cf6.png)

We then use the all-powerful predict function built in Python libraries. This is done with a simple one line of code.

```python
pred = clf.predict(player_stats_22[xcols3])
```

This outputs a long list of zeros and ones. The ones being a yes or an All-Star. We put this in a list and add it to the dataframe in the final column. We can now filter this resulting dataframe and see only the predicted All-Stars.

![image](https://user-images.githubusercontent.com/81338261/153013719-96d260f3-1e49-48ae-bdda-7bdd65fb0270.png)

Now, if we undo the filter, we can see all the players again. From this dataframe, which still shows the All-Star predictions, we can compare other players' stats to the average All-Star predicted. A lot of articles in the news recently have been saying that Jarrett Allen was snubbed and should have been selected as an All-Star. I started with comparing him. I created a specific player then subtracted his stats from the mean of the All-Star stats. This means we want to see postive results in our subtraction equations. If we see positive numbers, excluding the turnover and personal fouls (which you would want less of, so lower numbers)  

```python
specific_player = player_stats_22.loc[player_stats_22['Player'] == 'Jarrett Allen']
specific_player = specific_player.drop(['Player', 'Pos', 'Tm'], axis = 1)
specific_player
```


