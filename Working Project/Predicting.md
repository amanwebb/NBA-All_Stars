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

This is what the Radar Charts look like at the end. The further out the points are, the higher the ranking in that particular category. This is true, except for the Personal Fouls section. I inversed that section, due to Personal Fouls being bad. Therefore, it aligns with the other categories - the closer the point is to the center, the worse it is. The closer to the center it is, the more amount of fouls they average.  
