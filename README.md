In this project I will apply data wrangling and exploratory data analysis skills to baseball data. In particular, we want to know how well did [Moneyball](https://en.wikipedia.org/wiki/Moneyball_(film)) work for the [Oakland A's](https://en.wikipedia.org/wiki/Oakland_Athletics). Was it worthy of a movie?

## A bit of background

We'll be looking at data about teams in [Major League Baseball](https://en.wikipedia.org/wiki/Major_League_Baseball). A couple of important points:

Major League Baseball is a professional baseball league, where teams pay players to play baseball. The goal of each team is to win as many games out of a 162 game season as possible. Teams win games by scoring more runs ("getting more points") than their adversary. In principle, better players are costlier, so teams that want good players need to spend more money. Teams that spend the most, frequently win the most. So, the question is, how can a team that can't spend so much win? The basic idea that Oakland (and other teams) used is to redefine what makes a player good, i.e., figure out what player characteristics translated into wins. Once they realized that teams were not really pricing players using these characteristics, they could exploit this to pay for undervalued players, players that were good according to their metrics, but were not recognized as such by other teams, and therefore not as expensive.

You can get more information about this period in baseball history from:

[Wikipedia](http://en.wikipedia.org/wiki/Moneyball)

[The Moneyball book](http://www.amazon.com/Moneyball-The-Winning-Unfair-Game/dp/0393324818)

[The Moneyball movie](http://www.imdb.com/title/tt1210166/)

## The Data

I will be using data from a very useful database on baseball teams, players and seasons curated by Sean Lahman available at [http://www.seanlahman.com/baseball-archive/statistics/](http://www.seanlahman.com/baseball-archive/statistics/). The database has been made available as a `sqlite` database [https://github.com/jknecht/baseball-archive-sqlite](https://github.com/jknecht/baseball-archive-sqlite). `sqlite` is a light-weight, file-based database management system that is well suited for small projects and prototypes.

You can read more about the dataset here: [http://seanlahman.com/files/database/readme2014.txt](http://seanlahman.com/files/database/readme2014.txt).

You can download the `sqlite` file directly from github at [https://github.com/jknecht/baseball-archive-sqlite/raw/master/lahman2014.sqlite](https://github.com/jknecht/baseball-archive-sqlite/raw/master/lahman2014.sqlite).

I will be accessing the `sqlite` database in python using the [sqlite package](https://docs.python.org/2/library/sqlite3.html). This package provides a straightforward interface to extract data from `sqlite` databases using standard SQL commands.

Once I establish a connection with the `sqlite` database, I can store query results directly in a pandas dataframe using the [read_sql](http://pandas.pydata.org/pandas-docs/version/0.15.0/generated/pandas.read_sql.html) function.



## The Question

We want to understand how efficient teams have been historically at spending money and getting wins in return. In the case of Moneyball, one would expect that Oakland was not much more efficient than other teams in their spending before 2000, were much more efficient (they made a movie about it after all) between 2000 and 2005, and by then other teams may have caught up. My job in this project is to see how this is reflected in the data we have.

## Part 1: Wrangling

#### Problem 1
The data I need to answer these questions is in the Salaries and Teams tables of the database.

Using SQL I compute a relation containing the total payroll and winning percentage (number of wins / number of games * 100) for each team (that is, for each teamID and yearID combination).I include other columns that will help me when performing EDA later on (e.g., franchise ids, number of wins, number of games).


## Part 2: Exploratory Data Analysis

### Payroll Distribution

#### Problem 2

I made plots to illustrate the distribution of payrolls across teams conditioned on time (from 1990-2014).

#### Question 1

What statements can be make about the distribution of payrolls conditioned on time based on these plots? 

#### Problem 3

Producing plots that specifically justifies my answer for the above question. 

### Correlation between payroll and winning percentage

#### Problem 4

I discretize year into five time periods and then made a scatterplot showing mean winning percentage (y-axis) vs. mean payroll (x-axis) for each of the five time periods.

#### Question 2

What can one say about team payrolls across these periods? Are there any teams that standout as being particularly good at paying for wins across these time periods? What can one say about the Oakland A's spending efficiency across these time periods (labeling points in the scatterplot can help interpretation).

## Part 3: Data transformations

### Standardizing across years

Since, comparing payrolls across years is problematic I do a transformation that will help with these comparisons.

#### Problem 5

I create a new variable in your dataset that standardizes payroll conditioned on year. So, this column for team `i` in year `j` should equal:

<!--
```
$ standardized\_payroll_{ij} = \frac{{payroll}_{ij} - \overline{payroll}_{j} }{{s}_{j}} $
```
-->

![](figs/prob5_alternate.png)

for team `i` in year `j`.

where <!--<em><span style="text-decoration: overline">payroll</span><sub>j</sub></em>--> <em>avg\_payroll<sub>j</sub></em> is the average payroll for year `j`, and <em>s<sub>j</sub></em> is the standard deviation of payroll for year `j`.

#### Problem 6

Repeating the same plots as Problem 4, but use this new standardized payroll variable.

#### Question 3

So, how do Problem 4 and Problem 6 reflect the transformation I did on the payroll variable.

### Expected wins

It's hard to see global trends across time periods using these multiple plots, but now that we have standardized payrolls across time, we can look at a single plot showing correlation between winning percentage and payroll across time.

#### Problem 7

Made a single scatter plot of winning percentage (y-axis) vs. standardized payroll (x-axis). Added a regression line to highlight the relationship.

The regression line gives you expected winning percentage as a function of standardized payroll. Looking at the regression line, it looks like teams that spend roughly the average payroll in a given year will win 50% of their games (i.e. win\_pct is 50 when standardized\_payroll is 0), and teams increase 5% wins for every 2 standard units of payroll (i.e., win\_pct is 55 when standardized\_payroll is 2). 

From these observations we can calculate the expected win percentage for team `i` in year `j` as

<!--
```
${expected\_win\_pct_{ij} = 50+2.5×standardized\_payroll_{ij}}$
```
-->
![](figs/prob7.png)

### Spending efficiency

Using this result, I can now create a single plot that makes it easier to compare teams efficiency. The idea is to create a new measurement unit for each team based on their winning percentage and their expected winning percentage that I can plot across time summarizing how efficient each team is in their spending.

#### Problem 8

Creating a new field to compute each team's spending effiency, given by

![image](https://user-images.githubusercontent.com/43916597/115973944-cce78f80-a526-11eb-9cfb-27149d41d8be.png)

for team `i` in year `j`, where `expected_win_pct` is given above.

Made a line plot with year on the x-axis and efficiency on the y-axis. A good set of teams to plot are Oakland, the New York Yankees, Boston, Atlanta and Tampa Bay (teamIDs OAK, BOS, NYA, ATL, TBA).

#### Question 4

What can you learn from this plot compared to the set of plots you looked at in Question 2 and 3? How good was Oakland's efficiency during the Moneyball period?

