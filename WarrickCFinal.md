Data Analysis Final Paper
================
Chris Warrick
16 May 2017

Issue Description
-----------------

The issue is whether or not a "homefield advantage" exists in college football, and if it can be quantified.

Questions
---------

Does a homefield advantage exist for any or all football teams who play in the Big Ten conference? In other words, are they more likely to win a game in their home stadium than on the road? Do teams with bigger stadiums have a bigger advantage?

Data Source
-----------

The initial dataset I intended to use for this assignment was posted to the Internet under an open license. However, on further investigation, it seems to have been scraped from the archived version of a website which no longer offers these datasets for free.

Therefore, to avoid any potential legal or ethical issues, I deleted that data and created my own dataset. I went to the NCAA's website, which has a free public portal displaying college sports data. I pulled the win/loss records of each team from the appropriate form, located at the URL <http://web1.ncaa.org/stats/StatsSrv/careersearch?searchSport=MFB>. I pulled the stadium capacities for each team from their respective pages at en.wikipedia.org. I turned this into an Excel file, which I then imported into R Studio.

``` r
library(readr)
```

    ## Warning: package 'readr' was built under R version 3.3.3

``` r
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.3.3

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Warning: package 'ggplot2' was built under R version 3.3.3

    ## Warning: package 'tibble' was built under R version 3.3.3

    ## Warning: package 'tidyr' was built under R version 3.3.3

    ## Warning: package 'purrr' was built under R version 3.3.3

    ## Warning: package 'dplyr' was built under R version 3.3.3

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

``` r
library(scales)
```

    ## Warning: package 'scales' was built under R version 3.3.3

    ## 
    ## Attaching package: 'scales'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     discard

    ## The following object is masked from 'package:readr':
    ## 
    ##     col_factor

``` r
DataFinal <- read_csv("~/MSSA/DataFinal.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Team = col_character(),
    ##   Year = col_integer(),
    ##   HomeWin = col_integer(),
    ##   HomeLoss = col_integer(),
    ##   AwayWin = col_integer(),
    ##   AwayLoss = col_integer(),
    ##   Capacity = col_integer()
    ## )

Documentation
-------------

None from the originating websites. My own notes: the dataset contains home and away wins and losses for each Big Ten football squad, as well as their stadium capacities, from the 2007-8 to the 2016-17 football seasons. Of note, some teams joined the conference later. Nebraska joined in 2011. Maryland and Rutgers joined in 2014. For consistency's sake, all data goes back to 2007, regardless of conference.

Also of note, games played at a neutral site per the NCAA's data were treated as "away" for the purposes of this assignment; "home" games are games played in the team's home stadium. There were several discrepancies where the NCAA would arbitrarily list a game at a neutral site as either "home," "away," or "neutral."

Finally, I ignore vacating of wins by NCAA penalties. We are concerned with the gameday atmosphere and whether it has an effect on a team while playing a game, regardless of whether the game was retroactively invalidated for cheating.

Description of the Data
-----------------------

Use the tools in R such as str() and summary() to describe the original dataset you imported.

``` r
str(DataFinal)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    140 obs. of  7 variables:
    ##  $ Team    : chr  "Penn State" "Penn State" "Penn State" "Penn State" ...
    ##  $ Year    : int  2016 2015 2014 2013 2012 2011 2010 2009 2008 2007 ...
    ##  $ HomeWin : int  7 6 5 4 5 5 5 6 7 6 ...
    ##  $ HomeLoss: int  0 2 2 4 2 2 2 2 0 1 ...
    ##  $ AwayWin : int  4 1 2 3 3 4 2 5 4 3 ...
    ##  $ AwayLoss: int  3 4 3 2 2 2 4 0 2 3 ...
    ##  $ Capacity: int  106572 106572 106572 106572 106572 106572 107282 107282 107282 107282 ...
    ##  - attr(*, "spec")=List of 2
    ##   ..$ cols   :List of 7
    ##   .. ..$ Team    : list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_character" "collector"
    ##   .. ..$ Year    : list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
    ##   .. ..$ HomeWin : list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
    ##   .. ..$ HomeLoss: list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
    ##   .. ..$ AwayWin : list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
    ##   .. ..$ AwayLoss: list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
    ##   .. ..$ Capacity: list()
    ##   .. .. ..- attr(*, "class")= chr  "collector_integer" "collector"
    ##   ..$ default: list()
    ##   .. ..- attr(*, "class")= chr  "collector_guess" "collector"
    ##   ..- attr(*, "class")= chr "col_spec"

``` r
summary(DataFinal)
```

    ##      Team                Year         HomeWin         HomeLoss    
    ##  Length:140         Min.   :2007   Min.   :1.000   Min.   :0.000  
    ##  Class :character   1st Qu.:2009   1st Qu.:3.000   1st Qu.:1.000  
    ##  Mode  :character   Median :2012   Median :5.000   Median :2.000  
    ##                     Mean   :2012   Mean   :4.564   Mean   :2.393  
    ##                     3rd Qu.:2014   3rd Qu.:6.000   3rd Qu.:3.000  
    ##                     Max.   :2016   Max.   :8.000   Max.   :6.000  
    ##     AwayWin         AwayLoss       Capacity     
    ##  Min.   :0.000   Min.   :0.00   Min.   : 41500  
    ##  1st Qu.:1.750   1st Qu.:2.00   1st Qu.: 52454  
    ##  Median :3.000   Median :3.00   Median : 67353  
    ##  Mean   :2.686   Mean   :3.15   Mean   : 71598  
    ##  3rd Qu.:4.000   3rd Qu.:4.00   3rd Qu.: 81067  
    ##  Max.   :8.000   Max.   :6.00   Max.   :109901

Cleaning and Preparation
------------------------

First, the year is not being used as an integer value, but as a categorical variable showing which season we are talking about. University names are also used the same way. So we first need to change those variables to the proper types.

``` r
DataFinal$Year <- as.factor(DataFinal$Year)
DataFinal$Team <- as.factor(DataFinal$Team)
```

After that, we need to create a few variables for each year. Specifically, for ease in graphing, we want to know a team's overall winning percentage for each season. We also want to split this into a "home" and "away" winning percentage.

``` r
#Add computed values to the DataFinal tibble
DataFinal <- DataFinal %>%
             mutate(WinPct = (HomeWin + AwayWin)/(HomeWin + AwayWin +
                                                  HomeLoss + AwayLoss),
                    HomePct = HomeWin/(HomeWin + HomeLoss),
                    AwayPct = AwayWin/(AwayWin + AwayLoss))
```

Final Results
-------------

First, we summarize the data, and look to see if the collective winning percentage is higher at home.

``` r
#Generate scalar values for mean home/away percentages for all Big Ten teams
DataFinal %>% summarise(MeanHomePct = mean(HomePct),
                        MeanAwayPct = mean(AwayPct))
```

    ## # A tibble: 1 × 2
    ##   MeanHomePct MeanAwayPct
    ##         <dbl>       <dbl>
    ## 1   0.6526786   0.4463095

Indeed, it is, and not by a little bit! Big Ten teams have roughly a 20.6 percent better record at home than they do as a visiting team or at a neutral site.

Now before the SEC fans can turn that into a shot at the Big Ten's bowl record, let's see if we can dig a little deeper. Are there any teams which vary notably from this average? Do all teams win more at home? We'll look at each team's winning percentages.

``` r
#Create TeamPercentages tibble
TeamPercentages <- DataFinal %>%
                   group_by(Team) %>%
                   summarise(MeanHomePct = mean(HomePct),
                             MeanAwayPct = mean(AwayPct))

#Plot win percentages at home and away
TeamPercentages %>% ggplot(aes(x = Team)) +
                    geom_point(aes(y = MeanHomePct), color = "blue") +
                    geom_point(aes(y = MeanAwayPct), color = "red") +
                    theme(axis.text.x = element_text(angle = 45, vjust = 1,
                                                     hjust=1)) +
                    scale_y_continuous(labels = percent) +
                    labs(title = "Big Ten Winning Percentages", 
                         y = "Winning Percentage")
```

![](WarrickCFinal_files/figure-markdown_github/unnamed-chunk-6-1.png)

In the above graph, blue is the home winning percentage, and red is away.

It seems that individual records have little to do with whether or not a team does better at home or on the road. No matter their overall record, all teams do better at home (to varying degrees). A perennial basement-dweller like Purdue or Indiana still doesn't have much of a chance against powerhouses such as Ohio State, Penn State, or Wisconsin. But it seems the size of that small chance still depends on if they're playing at home or away.

There does seem to be significant variation in the size of the advantage. Northwestern seems to gain just a small advantage from playing at Ryan Field, whereas Michigan seems to gain a significant advantage from facing their opponents at the "Big House" of Michigan Stadium. But the Big House is over twice the size of Ryan Field. Could the size of a team's stadium be correlated with the difference in their win percentages? Do bigger stadiums allow for a louder and more raucous crowd, possibly tipping the scales for the home team? Let's find out.

Some teams expanded their stadiums in the past 10 years, so we'll take the mean of a team's stadium capacity, for simplicity's sake. We'll see if that is a predictor of the difference between a team's winning percent at home versus away.

``` r
#Create a tibble with the necessary data
Correlation <- DataFinal %>%
               group_by(Team) %>%
               summarise(MeanHomePct = mean(HomePct),
                         MeanAwayPct = mean(AwayPct),
                         PctDiff = MeanHomePct - MeanAwayPct,
                         MeanCapacity = mean(Capacity))

#Run a linear regression on the aforementioned variables
DataModel <- lm(PctDiff ~ MeanCapacity, data = Correlation)
summary(DataModel)
```

    ## 
    ## Call:
    ## lm(formula = PctDiff ~ MeanCapacity, data = Correlation)
    ## 
    ## Residuals:
    ##       Min        1Q    Median        3Q       Max 
    ## -0.144247 -0.035458  0.000988  0.062396  0.099068 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)  1.096e-01  7.198e-02   1.522    0.154
    ## MeanCapacity 1.352e-06  9.640e-07   1.402    0.186
    ## 
    ## Residual standard error: 0.07639 on 12 degrees of freedom
    ## Multiple R-squared:  0.1408, Adjusted R-squared:  0.06918 
    ## F-statistic: 1.966 on 1 and 12 DF,  p-value: 0.1862

The P-value of this linear model is 0.186. It seems there is not a statistically significant relationship between a team's stadium capacity and the difference in its winning percentages. What does the graph look like? Could it be a nonlinear relationship?

``` r
#Create a scatterplot of the data
Correlation %>% ggplot(aes(x = MeanCapacity, y = PctDiff)) +
                geom_point() + 
                scale_y_continuous(labels = percent) + 
                scale_x_continuous(labels = comma) +
                labs(x = "Stadium Capacity", y = "Difference in Winning Percentage")
```

![](WarrickCFinal_files/figure-markdown_github/unnamed-chunk-8-1.png)

It certainly looks like there is no correlation to be found here. This is not surprising, because the size of a stadium is no guarantee that people will actually buy tickets and show up. Perhaps average attendance would be a better predictor than stadium capacity. Alternatively, perhaps the average decibel level inside the stadium would have an effect, although that would be hard to find data on.

In the end, we must remember that football is a human endeavor. There are certainly many confounding variables involved in predicting who will win, including many which can't be measured.
