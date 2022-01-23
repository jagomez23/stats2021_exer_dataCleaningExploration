# Data cleaning

## Package management in R

``` r
# keep a list of the packages used in this script
packages <- c("tidyverse","rio","jmv")
```

This next code block has eval=FALSE because you don’t want to run it
when knitting the file. Installing packages when knitting an R notebook
can be problematic.

``` r
# check each of the packages in the list and install them if they're not installed already
for (i in packages){
  if(! i %in% installed.packages()){
    install.packages(i,dependencies = TRUE)
  }
  # show each package that is checked
  print(i)
}
```

``` r
# load each package into memory so it can be used in the script
for (i in packages){
  library(i,character.only=TRUE)
  # show each package that is loaded
  print(i)
}
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.6     v dplyr   1.0.7
    ## v tidyr   1.1.4     v stringr 1.4.0
    ## v readr   2.1.1     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    ## [1] "tidyverse"
    ## [1] "rio"
    ## [1] "jmv"

A note about working with packages and functions in R: When installing
packages you may see notifications that SomePackage::function() masks
OtherPackage::function(). What that means is that two different packages
used the same function name. The functions may or may not do the same
thing, but for whatever reason the pogrammers of each package happened
to choose the same name for a function in their respective package. The
last package installed will be the version of that function that will be
used if only the function name is used in your R code. The
SomePackage::function() notation with the colons in it means to use the
function named function() from the package named SomePackage. I think I
have decided when I write R code that I will always try to stick to that
notation. That way it’s very clear which package a desired function
comes from and there will be no problems with masking if different
packages used the same function name.

## Open the dataset

The rio package works for importing several different types of data
files. We’re going to use it in this class. There are other packages
which can be used to open datasets in R. You can see several options by
clicking on the Import Dataset menu under the Environment tab in
RStudio. (For a csv file like we have this week we’d use either From
Text(base) or From Text (readr). Try it out to see the menu dialog.)

``` r
# import the Week1.csv dataset into RStudio

# Using the file.choose() command allows you to select a file to import from another folder.
# dataset <- rio::import(file.choose())

# This command will allow us to import the csv file included in our project folder.
dataset <- rio::import("Week1.csv")
```

## Examine the dataset

You can uncomment the code below to try different ways to examine the
dataset in RStudio. (Remove the \# sign from one line at a time.)

``` r
# The View() command opens a data tab in RStudio. Notice the eval=FALSE option is set for this block.
# Don't evaluate blocks which use the View() command when knitting a file. It could be problematic.
View(dataset)
```

``` r
# These commands are all different ways to examine the dataset in R.
# You can uncomment each row and run the code block to try each one out.
dplyr::glimpse(dataset) #dplyr is part of the Tidyverse packages
```

    ## Rows: 10
    ## Columns: 4
    ## $ nominal  <chr> "female", "female", "female", "female", "female", "male", "ma~
    ## $ ordinal  <chr> "first", "second", "third", "fourth", "first", "second", "thi~
    ## $ interval <dbl> 36.9, 37.0, 37.1, 37.2, 37.3, 38.0, 38.1, 37.0, 36.8, 39.0
    ## $ ratio    <dbl> 64.0, 63.0, 65.5, 70.0, 68.5, 69.5, 67.0, 72.5, 81.0, 71.0

``` r
# str(dataset)
```

## Examine the data using the jmv package descriptives

``` r
# get descriptive statistics using jmv package
jmv::descriptives(dataset, vars = vars(nominal,ordinal),freq=TRUE)
```

    ## 
    ##  DESCRIPTIVES
    ## 
    ##  Descriptives                                 
    ##  -------------------------------------------- 
    ##                          nominal    ordinal   
    ##  -------------------------------------------- 
    ##    N                          10         10   
    ##    Missing                     0          0   
    ##    Mean                                       
    ##    Median                                     
    ##    Standard deviation                         
    ##    Minimum                                    
    ##    Maximum                                    
    ##  -------------------------------------------- 
    ## 
    ## 
    ##  FREQUENCIES
    ## 
    ##  Frequencies of nominal                             
    ##  -------------------------------------------------- 
    ##    Levels    Counts    % of Total    Cumulative %   
    ##  -------------------------------------------------- 
    ##    female         5      50.00000        50.00000   
    ##    male           5      50.00000       100.00000   
    ##  -------------------------------------------------- 
    ## 
    ## 
    ##  Frequencies of ordinal                             
    ##  -------------------------------------------------- 
    ##    Levels    Counts    % of Total    Cumulative %   
    ##  -------------------------------------------------- 
    ##    first          3      30.00000        30.00000   
    ##    fourth         2      20.00000        50.00000   
    ##    second         3      30.00000        80.00000   
    ##    third          2      20.00000       100.00000   
    ##  --------------------------------------------------

``` r
# get descriptive statistics using jmv package
jmv::descriptives(dataset, vars = vars(interval,ratio),freq=FALSE)
```

    ## 
    ##  DESCRIPTIVES
    ## 
    ##  Descriptives                                    
    ##  ----------------------------------------------- 
    ##                          interval     ratio      
    ##  ----------------------------------------------- 
    ##    N                            10          10   
    ##    Missing                       0           0   
    ##    Mean                   37.44000    69.20000   
    ##    Median                 37.15000    69.00000   
    ##    Standard deviation    0.7042727    5.148894   
    ##    Minimum                36.80000    63.00000   
    ##    Maximum                39.00000    81.00000   
    ##  -----------------------------------------------

## Convert factors

Notice that the jmv::descriptives() function doesn’t tell us the data
type for each variable like the dplyr::glimpse() function.

The rio package (or readr package in RStudio) doesn’t automatically
convert columns with strings to factors. We need to convert the nominal
and ordinal variables to factors and set the order of the levels for the
ordinal variable. (R puts factor levels in alphabetical order if an
order is not specified.) If you run dplyr::glimpse() again after you
execute the following code block, you’ll see the class has changed for
nominal and ordinal.

``` r
# https://suzan.rbind.io/2018/02/dplyr-tutorial-2/#working-with-discrete-columns
dataset <- dataset %>% mutate(nominal = as.factor(nominal))
levels(dataset$nominal)
```

    ## [1] "female" "male"

``` r
dataset <- dataset %>% mutate(ordinal = as.factor(ordinal))
levels(dataset$ordinal)
```

    ## [1] "first"  "fourth" "second" "third"

``` r
# These could have been done on a single line.
# dataset <- dataset %>% mutate_at(vars(nominal, ordinal), factor)

# Correct order can be specified for ordinal factors.
dataset <- dataset %>% mutate(ordinal = recode_factor(ordinal,"first"="first","second"="second","third"="third","fourth"="fourth",.ordered=TRUE))
levels(dataset$ordinal)
```

    ## [1] "first"  "second" "third"  "fourth"

## Plots

### jmv::descriptives() function

Jamovi can generate the plots commonly used to examine categorical
(nominal, ordinal) and continuous (interval, ratio) variables.

``` r
# get descriptive statistics using jmv package
jmv::descriptives(dataset, vars = vars(nominal,ordinal),freq=TRUE,bar=TRUE)
```

    ## 
    ##  DESCRIPTIVES
    ## 
    ##  Descriptives                                 
    ##  -------------------------------------------- 
    ##                          nominal    ordinal   
    ##  -------------------------------------------- 
    ##    N                          10         10   
    ##    Missing                     0          0   
    ##    Mean                                       
    ##    Median                                     
    ##    Standard deviation                         
    ##    Minimum                                    
    ##    Maximum                                    
    ##  -------------------------------------------- 
    ## 
    ## 
    ##  FREQUENCIES
    ## 
    ##  Frequencies of nominal                             
    ##  -------------------------------------------------- 
    ##    Levels    Counts    % of Total    Cumulative %   
    ##  -------------------------------------------------- 
    ##    female         5      50.00000        50.00000   
    ##    male           5      50.00000       100.00000   
    ##  -------------------------------------------------- 
    ## 
    ## 
    ##  Frequencies of ordinal                             
    ##  -------------------------------------------------- 
    ##    Levels    Counts    % of Total    Cumulative %   
    ##  -------------------------------------------------- 
    ##    first          3      30.00000        30.00000   
    ##    second         3      30.00000        60.00000   
    ##    third          2      20.00000        80.00000   
    ##    fourth         2      20.00000       100.00000   
    ##  --------------------------------------------------

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-10-1.png)![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-10-2.png)

``` r
# get plots for continuous variables using jmv package
jmv::descriptives(dataset, vars = vars(interval,ratio),freq=FALSE, hist=TRUE, box=TRUE)
```

    ## 
    ##  DESCRIPTIVES
    ## 
    ##  Descriptives                                    
    ##  ----------------------------------------------- 
    ##                          interval     ratio      
    ##  ----------------------------------------------- 
    ##    N                            10          10   
    ##    Missing                       0           0   
    ##    Mean                   37.44000    69.20000   
    ##    Median                 37.15000    69.00000   
    ##    Standard deviation    0.7042727    5.148894   
    ##    Minimum                36.80000    63.00000   
    ##    Maximum                39.00000    81.00000   
    ##  -----------------------------------------------

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-11-1.png)![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-11-2.png)![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-11-3.png)![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-11-4.png)

### ggplot2::ggplot() function

Plots can also be used to explore your data while data cleaning. While
the dfSummary function provides a quick look at variables, sometimes you
may want to run specific plots for different purposes. The ggplot2
package is a Tidyverse package whith many types of plots. Run the
following code blocks to see examples of plots for each variable type.
(There are also many others.)

``` r
ggplot(data=dataset, aes(x=nominal)) + 
  geom_bar(stat="count")
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-12-1.png)

``` r
ggplot(data=dataset, aes(x=ordinal)) +
  geom_bar(aes(y = (..count..)/sum(..count..)))
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-13-1.png)

``` r
ggplot(data=dataset, aes(x= "", y = interval)) +
  geom_boxplot()
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-14-1.png)

``` r
ggplot(data=dataset, aes(x=ratio)) +
  geom_histogram(binwidth = .2)
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-15-1.png)

``` r
ggplot(data=dataset, aes(x=interval, y=ratio)) + 
  geom_point()
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-16-1.png)

### ggplot2::qplot() function

The ggplot2 function qplot() is a quick way to make plots. Run the
following block of code to create plots for all the variables using
qplot().

``` r
qplot(dataset$nominal)
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-17-1.png)

``` r
qplot(dataset$ordinal)
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-17-2.png)

``` r
qplot(dataset$interval)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-17-3.png)

``` r
qplot(dataset$ratio)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-17-4.png)

``` r
qplot(dataset$nominal,dataset$ratio)
```

![](Week-02-Computer-Assignment---Data-Cleaning_files/figure-markdown_github/unnamed-chunk-17-5.png)
