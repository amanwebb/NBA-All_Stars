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
# Get the specific player you want
specific_player = player_stats_22.loc[player_stats_22['Player'] == 'Jarrett Allen']
specific_player = specific_player.drop(['Player', 'Pos', 'Tm'], axis = 1)
specific_player
```
This is the comparison of Jarrett Allen and the mean All-Star

![image](https://user-images.githubusercontent.com/81338261/153034368-f81b3628-3f05-47fe-a0d0-12c0af105de3.png)

Now everyone can can look at the numbers in the dataframe, but that is no fun. So I decided to make some Radar Charts to visualize the comparisons. This will help make the comparisons easier, faster, and more fun. 

I only included six variables within the charts, mainly just for simplicity. When you have too many, it gets too clustered and not easy to read. For me, I decided to go with Points, Total Rebounds, Steals, Blocks, Assists, and Personal Fouls. I felt like these were a good indicator for an all-around star player. The new dataframe will obviously be a lot smaller.

![image](https://user-images.githubusercontent.com/81338261/153035121-e701dd00-227b-4302-9d1c-c36468bc630c.png)

From here, I added a Rank column (percentage) for these columns. This is what we will use for our charts. This ranking will allow us to see the comparisons easier. Rather than seeing a player with an average of 30 points and another with 21, we can see the one player with a 90% and a 75%. Disclosure - these numbers in this paragraph were simply and clearly made up. A 30 point average is not necessarily a 90% ranking. 

![image](https://user-images.githubusercontent.com/81338261/153035757-326a8f04-8de6-4959-8475-19ab2cd5da6b.png)

I then wrote the function for the graph setup. This includes assigning team colors to their respective hex codes, splitting the graph into the appropriate sections (six), and creating a function to call the player and team. 

```python
# Calculate angles for radar chart
offset = np.pi / 6
angles = np.linspace(0, 2 * np.pi, len(categories) + 1) + offset

# Generate chart
def create_radar_chart(ax, angles, player_data, color = 'blue'):
    
    ax.plot(angles, np.append(player_data[-(len(angles) - 1):], 
            player_data[-(len(angles) - 1)]), 
            color = color, 
            linewidth = 2)
    ax.fill(angles, 
            np.append(player_data[-(len(angles) - 1):], 
            player_data[-(len(angles) - 1)]), 
            color = color, 
            alpha = 0.2)
    
    # Set category labels
    ax.set_xticks(angles[:-1])
    ax.set_xticklabels(categories)


    # Remove radial labels
    ax.set_yticklabels([])

    # Add player name
    ax.text(np.pi / 2, 2.2, 
            player_data[0], 
            ha = 'center', 
            va = 'center', 
            size = 18, 
            color = color)
    
    # Use white grid
    ax.grid(color = 'white', 
            linewidth = 1.5)

    # Set axis limits
    ax.set(xlim = (0, 
                   2 * np.pi), 
           ylim = (0, 1))

    return ax
    
# Function to select the player and team    
def get_data(data, player):
  return np.asarray(select_data.loc[select_data['Player'] == player])[0]
```

We will now use those functions for the players we want to compare. I have six players compared at a time. If you want to change that, you just have to remove the excess players (ax5, ax6, etc.) depending on how many you want to compare. You should also adjust the first number in the subplot area, because that is the actual layout of the graphs.

```python
# Create figure
fig = plt.figure(figsize = (8, 8), facecolor = 'white')

# Add subplots
ax1 = fig.add_subplot(231, projection = 'polar', facecolor = '#ededed')
ax2 = fig.add_subplot(232, projection = 'polar', facecolor = '#ededed')
ax3 = fig.add_subplot(233, projection = 'polar', facecolor = '#ededed')
ax4 = fig.add_subplot(234, projection = 'polar', facecolor = '#ededed')
ax5 = fig.add_subplot(235, projection = 'polar', facecolor = '#ededed')
ax6 = fig.add_subplot(236, projection = 'polar', facecolor = '#ededed')

# Adjust space between subplots
plt.subplots_adjust(hspace = 0.2, wspace = 1.0)

# Get data with player
candidate_1_data = get_data(select_data, 'Kevin Durant')
candidate_2_data = get_data(select_data, 'Trae Young')
candidate_3_data = get_data(select_data, 'LeBron James')
candidate_4_data = get_data(select_data, 'Luka Dončić')
candidate_5_data = get_data(select_data, 'LaMelo Ball')
candidate_6_data = get_data(select_data, 'Jarrett Allen')

# Plot data
ax1 = create_radar_chart(ax1, angles, candidate_1_data, team_colors['BRK'])
ax2 = create_radar_chart(ax2, angles, candidate_2_data, team_colors['ATL'])
ax3 = create_radar_chart(ax3, angles, candidate_3_data, team_colors['LAL'])
ax4 = create_radar_chart(ax4, angles, candidate_4_data, team_colors['DAL'])
ax5 = create_radar_chart(ax5, angles, candidate_5_data, team_colors['CHA'])
ax6 = create_radar_chart(ax6, angles, candidate_6_data, team_colors['CLE'])

plt.show()
```

![image](https://user-images.githubusercontent.com/81338261/153037689-3d8e5cfb-3192-4230-9aba-33fee96be111.png)

This is what the Radar Charts look like at the end. The further out the points are,, the higher teh ranking in that particular category. This is true, except for the Personal Fouls section. I inversed that section, due to Personal Fouls being bad. Therefor, it aligns withthe other categories - the closer the point is to the center, the worse it is. The closer to the center it is, the more amount of fouls they average.  

## Summary and Final Thoughts

This code is to help predict NBA All-Stars based on player statistics alone. I have been able to get a pretty good accuracy with my predictions, as my 2022 predictions come out with 89% of my selections actually being All-Stars. That is extremely good! We do not want it to be too high, because then we have to deal with the potential issue of overfitting and other problems. 

One thing that I also need to look into a little more - my predictions only outputted 18 picks. There are a total of 26 players selected, so I need to adjust the code to include a feew more players. This could change the accuracy score a little bit either direction. 

I still need to adjust my code to pick a specific number of players from each conference, as well as including starters and bench. This was the entire purpose of this project - to see whether or not Luka should be a starter or a bench player. That is what I will work on in the near future. I will keep you all updated as I progress in this endeavor. 

I have a lot of work to do if I want to make this prediction model even better and up to what I actually want. It is very good as is, but a lot of work is still needed to be done. 
