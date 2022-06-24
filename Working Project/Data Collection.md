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
