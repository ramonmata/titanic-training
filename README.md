Titanic Data
================
Ramon Mata

This notebook contains the exploratory analysis and modeling for the *Titanic Getting Started Prediction Competition*, it also tries to document some of hte *R* commands executed

<small> **Last update**: Wed May 3 18:59:28 2017 <br> **R Version**: 3.4.0 <br> **RStudio Version**: 3.4.0 </small>

Load Packages and Data Files
----------------------------

The following commands add the specified variables to the R environment so we can use their functionality

``` r
# Data Visualisation and themes
library('ggplot2')
library('ggthemes')

# Scale functions for Visualisations
library('scales')

# Data Manipulation
suppressMessages(library('dplyr'))

# Classification and Regression with Random Forest
suppressMessages(library('randomForest'))
```

The supressMessage function will *"suppress"* the extra information printed by the later 2 packages.

Write `?supressMessages` in the *R* console to get more information

Loading Data
------------

``` r
# Load train and test files provided by Kaggle and
# not reading string as Factors
train <- read.csv('data/train.csv', stringsAsFactors = FALSE)
test <- read.csv('data/test.csv', stringsAsFactors = FALSE)
```

-   See [Factors](https://stat.ethz.ch/R-manual/R-devel/library/base/html/factor.html)
-   or type `?factor` within *R* console for help

After Data is loaded in *R* Global Environment, we can take a quick look at the data by executing the following:

``` r
str(train)
```

    FALSE 'data.frame': 891 obs. of  12 variables:
    FALSE  $ PassengerId: int  1 2 3 4 5 6 7 8 9 10 ...
    FALSE  $ Survived   : int  0 1 1 1 0 0 0 0 1 1 ...
    FALSE  $ Pclass     : int  3 1 3 1 3 3 1 3 3 2 ...
    FALSE  $ Name       : chr  "Braund, Mr. Owen Harris" "Cumings, Mrs. John Bradley (Florence Briggs Thayer)" "Heikkinen, Miss. Laina" "Futrelle, Mrs. Jacques Heath (Lily May Peel)" ...
    FALSE  $ Sex        : chr  "male" "female" "female" "female" ...
    FALSE  $ Age        : num  22 38 26 35 35 NA 54 2 27 14 ...
    FALSE  $ SibSp      : int  1 1 0 1 0 0 0 3 0 1 ...
    FALSE  $ Parch      : int  0 0 0 0 0 0 0 1 2 0 ...
    FALSE  $ Ticket     : chr  "A/5 21171" "PC 17599" "STON/O2. 3101282" "113803" ...
    FALSE  $ Fare       : num  7.25 71.28 7.92 53.1 8.05 ...
    FALSE  $ Cabin      : chr  "" "C85" "" "C123" ...
    FALSE  $ Embarked   : chr  "S" "C" "S" "S" ...

``` r
str(test)
```

    FALSE 'data.frame': 418 obs. of  11 variables:
    FALSE  $ PassengerId: int  892 893 894 895 896 897 898 899 900 901 ...
    FALSE  $ Pclass     : int  3 3 2 3 3 3 3 2 3 3 ...
    FALSE  $ Name       : chr  "Kelly, Mr. James" "Wilkes, Mrs. James (Ellen Needs)" "Myles, Mr. Thomas Francis" "Wirz, Mr. Albert" ...
    FALSE  $ Sex        : chr  "male" "female" "male" "male" ...
    FALSE  $ Age        : num  34.5 47 62 27 22 14 30 26 18 21 ...
    FALSE  $ SibSp      : int  0 1 0 0 1 0 0 1 0 2 ...
    FALSE  $ Parch      : int  0 0 0 0 1 0 0 1 0 0 ...
    FALSE  $ Ticket     : chr  "330911" "363272" "240276" "315154" ...
    FALSE  $ Fare       : num  7.83 7 9.69 8.66 12.29 ...
    FALSE  $ Cabin      : chr  "" "" "" "" ...
    FALSE  $ Embarked   : chr  "Q" "S" "Q" "S" ...

After execution of `str()` for both data files, we can see some information about the files like:

-   Train file has more observations
-   An extra variable `Survived` exists in train file
-   Age may contain: `NA`
-   Cabin may come as empty string: ""

Exploratory Analysis - <small>*As is*</small>
---------------------------------------------

### Age <small>vs</small> Survived

``` r
ggplot(train, aes(Age, fill = factor(Survived, labels = c("No","Yes")))) +
  geom_histogram(bins = 30) +
  theme_minimal() +
  xlab("Age") +
  ylab("Count") +
  scale_fill_discrete(name = "Survived")
```

![](README_files/figure-markdown_github/unnamed-chunk-4-1.png)

### Sex <small>vs</small> Survived

``` r
ggplot(train, aes(Sex, fill = factor(Survived, labels = c("No","Yes")))) +
  geom_bar(stat = "count") +
  theme_minimal() +
  xlab("Sex") +
  ylab("Count") +
  scale_fill_discrete(name = "Survived")
```

![](README_files/figure-markdown_github/unnamed-chunk-5-1.png)

### Age <small>vs</small> Sex <small>vs</small> Survived

``` r
ggplot(train, aes(Age, fill = factor(Survived, labels = c("No","Yes")))) +
  geom_histogram(bins = 30) +
  theme_minimal() +
  xlab("Age") +
  ylab("Count") +
  facet_grid(.~Sex)+
  scale_fill_discrete(name = "Survived")
```

![](README_files/figure-markdown_github/unnamed-chunk-6-1.png)

### Class <small>vs</small> Sex <small>vs</small> Survived

``` r
#scale_fill_brewer(palette="Greys", name = "Survived")
ggplot(train, aes(Pclass, fill = factor(Survived, labels = c("No","Yes")))) +
  geom_bar(stat = "count") +
  theme_minimal() +
  facet_grid(~Sex)+
  xlab("Passenger Class") +
  ylab("Count") +
  scale_fill_discrete(name = "Survived")
```

![](README_files/figure-markdown_github/unnamed-chunk-7-1.png)

### Class <small>vs</small> Sex <small>vs</small> Age <small>vs</small> Survived

``` r
ggplot(train, aes(x = Age, y = Sex)) +
  geom_jitter(aes(colour = factor(Survived, labels = c("No","Yes")))) +
  theme_minimal() +
  facet_wrap(~Pclass) +
  labs(x = "Age", y = "Sex", colour = "Survived") +
  scale_x_continuous(name="Age",limits=c(0, 81))
```

![](README_files/figure-markdown_github/unnamed-chunk-8-1.png)

Data Processing and Exploratory Analysis
----------------------------------------

Modeling with Random Forest
---------------------------

Cross - Validation ?
--------------------

Prediction
----------
