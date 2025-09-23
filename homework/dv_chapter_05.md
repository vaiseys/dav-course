# Chapter 5 - Data Visualization

Here, we are going to practice some of the skills emphasized in Chapter 5. At first, it may seem that a lot of the skills are similar to those we learned in Modern Dive. I have two responses to that. First, you are right; repetition is important. That's how we learn things. Second, this chapter presents some incredibly handy tricks that as a Data Analyst you will use all the time. In these exercises, we are going to be using data from the WNCAA tournament.

As always, let's begin by reading in the data.

``` r
library(tidyverse)
# Read in the data 
wncaa <- read_csv("https://raw.githubusercontent.com/vaiseys/dav-course/main/Data/wncaa.csv")

# Glimpse the data 
glimpse(wncaa)
```

```         
## Rows: 2,092
## Columns: 19
## $ year              <dbl> 1982, 1982, 1982, 1982, 1982, 1982, 1982, 1982, 1982…
## $ school            <chr> "Arizona St.", "Auburn", "Cheyney", "Clemson", "Drak…
## $ seed              <dbl> 4, 7, 2, 5, 4, 6, 5, 8, 7, 7, 4, 8, 2, 1, 1, 2, 3, 6…
## $ conference        <chr> "Western Collegiate", "Southeastern", "Independent",…
## $ conf_w            <dbl> NA, NA, NA, 6, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
## $ conf_l            <dbl> NA, NA, NA, 3, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
## $ conf_percent      <dbl> NA, NA, NA, 66.7, NA, NA, NA, NA, NA, NA, NA, NA, NA…
## $ conf_place        <chr> "-", "-", "-", "4th", "-", "-", "-", "-", "-", "-", …
## $ reg_w             <dbl> 23, 24, 24, 20, 26, 19, 21, 14, 21, 28, 24, 17, 22, …
## $ reg_l             <dbl> 6, 4, 2, 11, 6, 7, 8, 10, 8, 7, 5, 13, 7, 5, 1, 6, 4…
## $ reg_percent       <dbl> 79.3, 85.7, 92.3, 64.5, 81.3, 73.1, 72.4, 58.3, 72.4…
## $ how_qual          <chr> "at-large", "at-large", "at-large", "at-large", "aut…
## $ x1st_game_at_home <chr> "Y", "N", "Y", "N", "Y", "N", "N", "N", "N", "N", "Y…
## $ tourney_w         <dbl> 1, 0, 4, 0, 2, 0, 0, 0, 0, 0, 2, 0, 2, 1, 5, 3, 1, 1…
## $ tourney_l         <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1…
## $ tourney_finish    <chr> "RSF", "1st", "N2nd", "1st", "RF", "1st", "1st", "1s…
## $ full_w            <dbl> 24, 24, 28, 20, 28, 19, 21, 14, 21, 28, 26, 17, 24, …
## $ full_l            <dbl> 7, 5, 3, 12, 7, 8, 9, 11, 9, 8, 6, 14, 8, 6, 1, 7, 5…
## $ full_percent      <dbl> 77.4, 82.8, 90.3, 62.5, 80.0, 70.4, 70.0, 56.0, 70.0…
```

We have data for all teams that have made it to the WNCAA tournament. We have a wealth of information from `reg_percent`, the percentage of wins in the regular season, to the place they ended in a given tournament (`tourney_finish`).

> Note: The original data comes from [here](https://github.com/rfordatascience/tidytuesday/tree/main/data/2020/2020-10-06).

## Question 1

Let's practice some of the summarizing skills that Healy introduces. We are going to examine the percentage of tournaments that schools have won.

First, `filter` the dataset for observations where `tourney_finish` equals `Champ`.

Now, use `group_by` and `summarize` to calculate the number of tournament championships each team has, and then the percentage of tournaments each team has.

Plot a bar plot that shows these percentages by school.

What patterns do you see? Who are the two teams that have won the most?

## Question 2

Let's now look at how the top teams have been seeded as they enter into the tournament. Let's begin by creating a dataset that includes just the "top teams". How are we going to do this? Well, let's take the teams that show up in your bar plot above. We are going to work with the dataset that only includes teams that have ever won the tournament. I'll show you how to do it.

The dataset I created for the plot above is called `champs`. Let's get the names of the champions:

``` r
champ_names <- unique(champs$school)
```

Now, we filter our original name so that only these schools are included.

``` r
winners <- wncaa |> 
  filter(school %in% champ_names)
```

Now, make a plot that shows boxplots for the distribution of `seeds` for each school.

These days, it's good practice to add all the data points in addition to the boxplot. You can use `geom_jitter()` to do this. Don't forget to use `outliers = FALSE` in the boxplot so you don't plot the outliers twice.

We will also want to organize the plots so that they convey information more clearly.

> Hint: Use the `reorder()` trick to show the distributions in a an order that is easier to understand. You will need to calculate some school-specific statistics to use for the reordering. You could also look into the equivalent `fct_reorder()` function. (There are many ways to do this).

Describe the results? Any surprises?

Try to make the same plot using `geom_violin()` instead of `geom_boxplot()`. Which visualization do you think is more informative? There's no right answer here but provide some reasoning to justify your choice.

## Question 3

Try making the plot above but using `geom_point` only. Why does it not work very well?

## Question 4

Okay, now let's try the `summarize()` verb. Let's make a new data frame by taking the `winners` dataset, grouping by school, and take the `mean()` and `sd()` of the columns *if* they are numeric.

> NOTE: The book uses a version of this function called `summarize_if()`. Some years ago a new function called `across()` was introduced, which we can use like this:
>
> ```         
> winners_mean_sd <- winners |> 
>   group_by(school) |> 
>   summarize(across(where(is.numeric), list(avg = mean, sd = sd)))
> ```
>
> You can read more about this function [here](https://dplyr.tidyverse.org/articles/colwise.html).

Let's explore the average win percentage of these schools across the seasons. In your new dataset, this column should be called `reg_percent_avg`. Make a dot plot, where this column is in the x-axis and school is the y-axis. (Use `reorder` to make the plot legible).

Describe the results. Which tournament winner had the lowest regular season win percentage?

Now, let's try to take into account the standard deviation. Use the `geom_pointrange` to show the intervals of one standard deviation below and above the mean (just like Figure 5.15 in [socviz.co](https://socviz.co/workgeoms.html)).

> HINT: This requires `xmax` and `xmin` arguments inside the aesthetic mapping.

What is the school with the narrowest interval? What does this mean?

Can you make the same plot using `geom_linerange` ? Do you think this is a good or bad idea? Why? There is no right answer; just give your reasoning.
