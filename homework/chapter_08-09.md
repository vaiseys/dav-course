---
title: "Chapter 8-9"
output: 
  html_document: 
    toc: true
    theme: united
    keep_md: true
---

We are going to cover the basic concepts in Chapters 8 and 9: confidence intervals and hypothesis testing. 

# Chapter 8 

We are going to practice resampling again, but this time armed with the language we learned in chapter 8. We are going to use three different datasets along the way. Don't forget to load the packages you will need.

To make life easier on yourself, be sure to specify a random number seed using `set.seed()` at the beginning of *each* of your code blocks. 
## Polling 
### Question 1

Let's start by redoing the polling analysis we did in class. Be sure to use this exact code (including this specific `set.seed()`) to make the dataset.


```r
set.seed(1108)

poll <- tibble(
  vote_gop = rbinom(n = 1000,
                    size = 1,
                    prob = .53))
```

I want you to make a **bootstrap 95% percent confidence interval** for GOP (Republican) vote share based on this poll. I want you to do this two different ways to connect what you learned in chapter 7 and chapter 8.

##### First way

Calculate it using *only* the following functions:

* `rep_sample_n()`
* `group_by()`
* `summarize()`
* `quantile()`

You may also use the `pull()` or `select()` function if you need them.

A few hints: 

1. Make sure you pay attention to the `replace` option in `rep_sample_n()`.
2. When you have the bootstrap distribution, you can get the confidence interval by piping it to `quantile(c(.025, .975))`.

What is the estimated confidence interval?

#### Second way

Now do it using *only* the following functions:

* `specify()`
* `generate()`
* `calculate()`
* `get_ci()`

What is the estimated confidence interval? (HINT: even though you are estimating a proportion, use `stat = "mean"` instead of `stat = "prop"`.)

How similar are the two confidence intervals you calculated? Why aren't they *exactly* the same?

## History of rap

Here, we are going to use a dataset that contains the results of a survey conducted where artists and critics listed their favorite rap tracks. For each track we have information about its release data, and what we are going to do is to examine what is the year of that shows up the most - i.e. we are going to try to find out the year when rap peaked. 

This is not a silly attempt to make a data analysis course more interesting or relatable, this is a real issue in the sociology of culture. We have evidence [that music sales are higher for old music](https://www.theatlantic.com/ideas/archive/2022/01/old-music-killing-new-music/621339/). Nostalgia is the name of the game. A lot of people really believe that old music was better and organizing their music-listening habits around this idea. So understanding where supposed "experts" place the peak of rap is important and interesting. 

The data can be found [here](https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-04-14/polls.csv). Load the data and call the object `rap_poll` and then we'll get started.

First of all, let's just keep the #1 song listed by each critic to make things simple. Use `filter()` to keep only those songs where `rank` is equal to 1. The resulting data frame should have 107 rows.

### Question 2

Make a histogram of the year each of the tracks was released. What is the year of the most commonly named favorite track in this critic poll? How many critics named a track from this year?

### Question 3

Let's assume that these 107 critics are a random sample of a population of critics. If so, it might be useful to construct a 95% confidence interval for the peak of rap. Calculate this interval, using either implementation of the percentile method I asked you to use above. Report the upper and lower bound of this interval to the nearest year.

### Question 4

Let's say that instead of asking 107 critics, we had asked 25 critics. What would the confidence interval (again, rounded to the nearest year) be in that case? (HINT: you will probably need to use the "first way" for calculating a confidence interval you used above.) How does the width of this confidence interval compare to the width of the confidence interval when we use the full sample of 107?

# Chapter 9

Here, we will reinforce some ideas from Chapter 9. This one is very important, not only because the content itself is important, but because you will see these ideas used and abused everywhere to support all kinds of silly arguments. A thorough understanding of these ideas will be a great BS detector when you are confronted with other people's data analyses. 

We will be using soccer data from the English Premier League to examine whether home field advantage is a thing in this league. Do teams who play at home - with their own fans - win more on average? We will use your new hypothesis testing skills to examine this question. We will use data from all games of the legendary 2015/2016 season. Don't know why it's legendary? Look it up. It's worth it. Best sports story of all time some say.  

Let's begin by reading in the data and looking at it. 


```r
pl_data <- read_csv("../Data/premier_league.csv")

glimpse(pl_data)
```

```
## Rows: 380
## Columns: 4
## $ home_team  <chr> "Bournemouth", "Chelsea", "Everton", "Leicester", "Man Unit…
## $ away_team  <chr> "Aston Villa", "Swansea", "Watford", "Sunderland", "Tottenh…
## $ score_diff <dbl> -1, 0, 0, 2, 1, -2, -2, 0, -1, -3, -1, -3, -2, 2, 0, 0, -1,…
## $ result     <chr> "aw", "d", "d", "hw", "hw", "aw", "aw", "d", "aw", "aw", "a…
```

We have 4 columns then: the home team, the away team, the score difference (from the standpoint of the home team), and the result. We have three different types of results: an away win (aw), a home win (hw), and a draw (d). What we are interested in then is the proportion of home wins. What is the rate of winning at home and is it different that mere chance? 

## Question 5

Use your data wrangling skills to calculate the proportion of home wins during the 2015/2016 season. 

## Question 6

Now, we are going to shuffle. We are going to examine what proportion we would expect if a home win was equally likely as any other result. This one is a bit more interesting than the examples in the chapter because there are 3 choices instead of 2. Below, you will find some code that simulates our dataset, reshuffled, 1000 times. Here, however, we assume that an away-win, a draw, and a home-win have equal probability. Run the script and plot the resulting proportions as a histogram.


```r
set.seed(22)

sampled_proportions <- c()

for (i in 1:1000) {
  
  sampled_results <- sample(c("aw", "hw" , "d"), 
                            size = 380,
                            replace = TRUE, 
                            prob = c(1/3,1/3,1/3))
  prop <- sum(sampled_results == "hw")/380
  sampled_proportions[i] <- prop
  
}

Describe the histogram in a sentence or two. How does the proportion you found in Question 6 compare to the proportion we would expect if a home win was equally likely as any other result?

## Question 7

In this scenario, what would be the null hypothesis and the alternative hypothesis? Provide enough detail so that I know you understand. 

## Question 8

What would a p-value mean in this example? 





