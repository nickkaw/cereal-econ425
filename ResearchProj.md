Research Project - Cereals
================
Nicholas Kaw
12/10/2021

## Introduction

For this research project, I managed to stumble upon an interesting data
set about cereals. I found this on Kaggle
(<https://www.kaggle.com/crawford/80-cereals>), but it is also available
as a built in data set in the plspm package. However, this data set is
originally from StatLib during a 1993 Statistical Graphics Exposition
(<http://lib.stat.cmu.edu/datasets/1993.expo/>). This is a
cross-sectional data set that contains information of 77 cereal brands,
its manufacturer, whether it is eaten cold or hot, its nutritional
information, what shelf level it is found on, the amount per serving,
and rating of the cereal.

One caveat of the original data set is the rating variable. It seems
that the original authors are not certain of how rating is obtained or
what type of rating is measured. Another problem is that there are some
observations that are missing values filled in with a $-1$. Also, there
are very little hot cereals listed in the data set. Because of this, I
decided to remove the observations with missing values and hot cereals
from my experiment since it is negligible.

Thus, I am interested in estimating the relationship between *rating*
and the main nutritional components that usually appear in cereals.
Namely, *sugars*, *sodium*, *fiber*, *potassium*, and *calories*. Both
*sugars* and *sodium* are considered as “nutrients to get less of”, and
*fiber* and *potassium* are considered as “nutrients to get more of”,
according to the FDA. On the other hand, *calories* is a measure of how
much energy you can get per serving. Although the information of the
*rating* variable is unclear, I am going to assume that it measures the
rating in terms of its nutritional information. In other words, how
“good” or “not so good” a cereal is based on its nutritional benefits.
And throughout this study, we will keep assuming that this is the case
for the rating variable.

### Summary Statistics

Here is an overall summary of the of all of the variables in the data
set. The first is a summary of the raw data set, the second is a summary
after removing some observations and selecting the variables I will be
focusing on for my regression.

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.3     v purrr   0.3.4
    ## v tibble  3.0.5     v dplyr   1.0.3
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.1

    ## Warning: package 'forcats' was built under R version 4.0.5

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(stargazer)
```

    ## 
    ## Please cite as:

    ##  Hlavac, Marek (2018). stargazer: Well-Formatted Regression and Summary Statistics Tables.

    ##  R package version 5.2.2. https://CRAN.R-project.org/package=stargazer

``` r
library(AER)
```

    ## Warning: package 'AER' was built under R version 4.0.5

    ## Loading required package: car

    ## Warning: package 'car' was built under R version 4.0.5

    ## Loading required package: carData

    ## 
    ## Attaching package: 'car'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     recode

    ## The following object is masked from 'package:purrr':
    ## 
    ##     some

    ## Loading required package: lmtest

    ## Warning: package 'lmtest' was built under R version 4.0.5

    ## Loading required package: zoo

    ## Warning: package 'zoo' was built under R version 4.0.5

    ## 
    ## Attaching package: 'zoo'

    ## The following objects are masked from 'package:base':
    ## 
    ##     as.Date, as.Date.numeric

    ## Loading required package: sandwich

    ## Warning: package 'sandwich' was built under R version 4.0.5

    ## Loading required package: survival

     name               mfr                type              calories    

Length:77 Length:77 Length:77 Min. : 50.0  
Class :character Class :character Class :character 1st Qu.:100.0  
Mode :character Mode :character Mode :character Median :110.0  
Mean :106.9  
3rd Qu.:110.0  
Max. :160.0  
protein fat sodium fiber  
Min. :1.000 Min. :0.000 Min. : 0.0 Min. : 0.000  
1st Qu.:2.000 1st Qu.:0.000 1st Qu.:130.0 1st Qu.: 1.000  
Median :3.000 Median :1.000 Median :180.0 Median : 2.000  
Mean :2.545 Mean :1.013 Mean :159.7 Mean : 2.152  
3rd Qu.:3.000 3rd Qu.:2.000 3rd Qu.:210.0 3rd Qu.: 3.000  
Max. :6.000 Max. :5.000 Max. :320.0 Max. :14.000  
carbo sugars potass vitamins  
Min. :-1.0 Min. :-1.000 Min. : -1.00 Min. : 0.00  
1st Qu.:12.0 1st Qu.: 3.000 1st Qu.: 40.00 1st Qu.: 25.00  
Median :14.0 Median : 7.000 Median : 90.00 Median : 25.00  
Mean :14.6 Mean : 6.922 Mean : 96.08 Mean : 28.25  
3rd Qu.:17.0 3rd Qu.:11.000 3rd Qu.:120.00 3rd Qu.: 25.00  
Max. :23.0 Max. :15.000 Max. :330.00 Max. :100.00  
shelf weight cups rating  
Min. :1.000 Min. :0.50 Min. :0.250 Min. :18.04  
1st Qu.:1.000 1st Qu.:1.00 1st Qu.:0.670 1st Qu.:33.17  
Median :2.000 Median :1.00 Median :0.750 Median :40.40  
Mean :2.208 Mean :1.03 Mean :0.821 Mean :42.67  
3rd Qu.:3.000 3rd Qu.:1.00 3rd Qu.:1.000 3rd Qu.:50.83  
Max. :3.000 Max. :1.50 Max. :1.500 Max. :93.70  
name lcalories sodium fiber  
Length:73 Min. :3.912 Min. : 0.0 Min. : 0.000  
Class :character 1st Qu.:4.605 1st Qu.:135.0 1st Qu.: 1.000  
Mode :character Median :4.700 Median :180.0 Median : 2.000  
Mean :4.654 Mean :164.6 Mean : 2.205  
3rd Qu.:4.700 3rd Qu.:220.0 3rd Qu.: 3.000  
Max. :5.075 Max. :320.0 Max. :14.000  
sugars potass rating  
Min. : 0.000 Min. : 15.00 Min. :18.04  
1st Qu.: 3.000 1st Qu.: 40.00 1st Qu.:32.21  
Median : 7.000 Median : 90.00 Median :40.11  
Mean : 7.164 Mean : 98.56 Mean :42.20  
3rd Qu.:11.000 3rd Qu.:120.00 3rd Qu.:49.79  
Max. :15.000 Max. :330.00 Max. :93.70

Here is a more visually appealing summary in a table format. This is a
summary of the cleaned data set.

``` r
stargazer(title = "Summary Statistics", as.data.frame(cereal), type = "text", digits = 2,
                 summary.stat = c("n", "mean", "sd", "median", "min", "max"))
```

    ## 
    ## Summary Statistics
    ## ===============================================
    ## Statistic N   Mean  St. Dev. Median  Min   Max 
    ## -----------------------------------------------
    ## lcalories 73  4.65    0.21    4.70  3.91  5.08 
    ## sodium    73 164.59  81.09    180     0    320 
    ## fiber     73  2.21    2.43     2      0    14  
    ## sugars    73  7.16    4.36     7      0    15  
    ## potass    73 98.56   71.37     90    15    330 
    ## rating    73 42.20   14.05   40.11  18.04 93.70
    ## -----------------------------------------------

## Main Analysis

The first regression is a Simple Linear Regression. Here the equation
regresses *rating* only on *sugars*, where sugars is measured in grams.
I decided to use *sugars* as my main independent variable because it is
the easiest to intuitively understand. I may not be a nutrition expert,
but to my knowledge, the amount of sugars on a nutrition facts label is
an easy sign of unhealthy adversely affects a persons health

The second regression is a Multiple Linear Regression. Here we add in
*sodium*, *fiber*, *potassium*, and logged *calories*. *Sodium* is
measured in milligrams. *Fiber* is measured in grams. *Potassium* is
measured in milligrams. And *calories* is measured as per serving.
Calories is logged because there is no meaningful 0 of calories compared
to other variables in this data set. Calories do have a meaningful zero,
which would be something like water, but cereals tend to normally have
at least 1 calorie per serving. Also, logging would make a more normal
distribution of the data since it takes relative differences. Logging
would also change non-linear factors into linear scale, since it looks
at relative differences, which we can perform linear regressions with.

``` r
one.lm = lm(rating ~ sugars, data = cereal)
two.lm = lm(rating ~ sugars + sodium + fiber + potass + lcalories, data = cereal)
```

``` r
stargazer(one.lm, two.lm, type = "html", omit.table.layout = "n", omit.stat ="ser")
```

<table style="text-align:center">
<tr>
<td colspan="3" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td colspan="2">
<em>Dependent variable:</em>
</td>
</tr>
<tr>
<td>
</td>
<td colspan="2" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td colspan="2">
rating
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
\(1\)
</td>
<td>
\(2\)
</td>
</tr>
<tr>
<td colspan="3" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
sugars
</td>
<td>
-2.426<sup>\*\*\*</sup>
</td>
<td>
-1.923<sup>\*\*\*</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(0.252)
</td>
<td>
(0.129)
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td style="text-align:left">
sodium
</td>
<td>
</td>
<td>
-0.049<sup>\*\*\*</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
(0.006)
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td style="text-align:left">
fiber
</td>
<td>
</td>
<td>
3.682<sup>\*\*\*</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
(0.573)
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td style="text-align:left">
potass
</td>
<td>
</td>
<td>
-0.037<sup>\*</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
(0.018)
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td style="text-align:left">
lcalories
</td>
<td>
</td>
<td>
-5.673<sup>\*</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
(3.137)
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td style="text-align:left">
Constant
</td>
<td>
59.583<sup>\*\*\*</sup>
</td>
<td>
85.878<sup>\*\*\*</sup>
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
(2.106)
</td>
<td>
(13.762)
</td>
</tr>
<tr>
<td style="text-align:left">
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td colspan="3" style="border-bottom: 1px solid black">
</td>
</tr>
<tr>
<td style="text-align:left">
Observations
</td>
<td>
73
</td>
<td>
73
</td>
</tr>
<tr>
<td style="text-align:left">
R<sup>2</sup>
</td>
<td>
0.567
</td>
<td>
0.932
</td>
</tr>
<tr>
<td style="text-align:left">
Adjusted R<sup>2</sup>
</td>
<td>
0.561
</td>
<td>
0.927
</td>
</tr>
<tr>
<td style="text-align:left">
F Statistic
</td>
<td>
93.031<sup>\*\*\*</sup> (df = 1; 71)
</td>
<td>
183.336<sup>\*\*\*</sup> (df = 5; 67)
</td>
</tr>
<tr>
<td colspan="3" style="border-bottom: 1px solid black">
</td>
</tr>
</table>

### Scatter Plot

Here is a scatter plot of the Simple Linear Regression. With number of
sugars on the x axis, and rating of the cereal on the y axis

``` r
plot(cereal$sugars, cereal$rating, xlab="Number of Sugars (in grams)",ylab="Cereal Rating",col="dark green") 
abline(one.lm,col="blue")
```

![](ResearchProj_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## Six Assumptions

### Assumption 1

This assumption assumes that in the population, the relationship between
the dependent and independent variables are linear.

I believe that this assumption holds for this data set. The population
in this data set would be every cold cereal in the world. The dependent
variable we are measuring is the cereal’s rating in terms of its
nutritional information. In general, there are cases of non-linearity in
nutrition. For example, too much *fiber* or too much *potassium* can
adversely affect an individuals health. But since we are focusing on
only the population of cold cereals, we can assume that there is only
linear relationships between the independent variables and dependent
variables

Also, not logged calories would violate this assumption. Calories
initially had a non-linear relationship in the data set, which violates
the assumption of linearity in the population. But after logging
calories it transforms a non-linear factor into a linear one, since it
looks at relative differences, which we can perform linear regressions
with.

### Assumption 2

This assumption assumes that the data represents a random sample drawn
from the population.

It is difficult to truly assume that this assumption holds for this data
set. There is a possibility that the original authors just went to one
or two big box retail stores and wrote down the nutritional information
of all the different types of cereal that they could find. This is
because in the original data set there is a variable called *shelf*,
which reports what shelf-level the cereal was found on. However, since
we have established that the population in this context would be every
cold cereal in the world, then we can say that this set of observations
is randomly sampled from the population. Thus, this assumption holds for
the data set.

### Assumption 3

This assumption assumes that none of the independent variables are
constants and no perfect collinearity. No perfect collinearity means
that independent variables are perfectly correlated (correlation between
$x_1$ and $x_2$ is one) to other independent variables.

In the Multiple Linear Regression, it shows that each of the independent
variables are not constants, meaning that there are different values of
*sugars*, *sodium*, *fiber*, etc. Also, none of the independent
variables are perfectly collinear, meaning that we cannot draw perfect
linear relationships between the independent variables. So, this
assumption holds for this data set.

### Assumption 4

Explain the assumption and whether you think it holds for your data set.
This is the most important assumption to think about. If you don’t think
this assumption is true, what are your ideas for how you could fix this
problem in a future research project? You don’t have to try and
implement your solution for this project.

This assumption assumes zero conditional mean. Which means that the
average value of the unobserved factors, conditional on all independent
variables, equals to zero. This means that the value of the explanatory
variables must contain no information about the mean of the unobserved
factors.

I believe that this assumption is violated for this data set. One reason
is that *calories* can be associated with many other nutritional
factors. This includes *fats*, *carbohydrates*, *protein*, and other
variables that I decided not to include in my regression from the
original data set.

A possible solution to this problem would be to include the variables
that were left out in another regression. Then this assumption may
possibly hold. However, one caveat of doing this would be increasing our
standard errors, due to introducing more multicollinearity between all
of the independent variables in the regression. Another solution to this
problem would be to increase our sample size, and assume the weaker
version of this assumption to hold true. Since right now, 73
observations is not quite enough data needed to satisfy the weaker
version of this assumption.

### Assumption 5

Explain the assumption and whether you think it holds for your data set.
It is easy to test this assumption. Just do it for one of your
regressions (best to do it for the one with the most x variables). See R
Day \#12b for more details.

This assumption assumes homoskedasticity. Which means that the variance
of the unobserved factors, conditional on all independent variables, is
a constant. This means that value of the explanatory variables must
contain no information about the mean of the unobserved factors.

``` r
bptest(two.lm)
```

    ## 
    ##  studentized Breusch-Pagan test
    ## 
    ## data:  two.lm
    ## BP = 5.944, df = 5, p-value = 0.3117

I used a Breusch-Pagan test to test for heteroskedasticity. Since the
p-value of this test is greater than 0.05, we fail to reject the null
hypothesis of homoskedasticity. This means that this assumption holds
for this data set.

### Assumption 6

Explain the assumption and whether you think it holds for your data set.
For assumption 6, plot a histogram of your residuals with a normal
distribution on top and see if they match. See R Day \#9 for an example.

This assumption assumes that the unobserved factors are normally
distributed for each independent variable individually.

``` r
residuals <- resid(two.lm)
steps <- (max(residuals)-min(residuals))/100

hist(residuals,freq=F)
lines(seq(min(residuals), max(residuals), by=steps), dnorm(seq(min(residuals), max(residuals), by=steps), 0, sd(residuals)), col="dark green")
```

![](ResearchProj_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

The histogram shows that the residuals are not normally distributed. We
see that the most of the residuals appear around 0 and 5. There are also
parts of the graph that are way below the normal curve (represented by a
green line). In short, this assumption is violated.

One solution to satisfy this assumption is to obtain more data. This is
mainly because of the Central Limit Theorem. As we increase the sample
size that we have, the more normally distributed the mean of sample
becomes.

If this assumption is violated, what can you do about it?

## Conclusion

In the conclusion, state what you have learnt from this research
project. What questions remain unanswered?

As a result, all but assumptions 4 and 6 of the Classical Linear Model
are satisfied. Our coefficient estimates are biased, therefore our
standard errors, t-ratios, and p-values, are all biased as well. A
question that remains unanswered is, is there ever a way to accurately
pin point causation between the rating of a cereal by its nutritional
health factors?

For this research project, I have learned that it is extremely easy to
assume that your stance is correct for any of the six Classical Linear
Regression assumptions. However, it is also extremely difficult to
accurately prove all six assumptions. I now understand how a lot of
argument may arise from economists, statisticians, and other peoples
having different point of views on a statement.
