K-medoids
================

An alternative to k-means is the k-medoids or partitioning around
medoids (PAM) algorithm.

According to [wikipeadia](https://en.wikipedia.org/wiki/K-medoids):

> Both the k-means and k-medoids algorithms are partitional (breaking
> the dataset up into groups) and both attempt to minimize the distance
> between points labeled to be in a cluster and a point designated as
> the center of that cluster. In contrast to the k-means algorithm,
> k-medoids chooses data points as centers (medoids or exemplars) and
> can be used with arbitrary distances, while in k-means the centre of a
> cluster is not necessarily one of the input data points (it is the
> average between the points in the cluster).

K-means attempts to minimize the total squared error, while k-medoids
minimizes the sum of dissimilarities between points labeled to be in a
cluster and a point designated as the center of that cluster.

The k-methods method is more robust to noise and outliers. The medoid
used is comparable to a median rather than a mean. Median is considered
more robust to outliers than using the mean.

Disadvantage: PAM can take much longer to run than k-means. As it
involves computing all pairwise distances, it is O(n^2*k*i); whereas
k-means runs in O(n*k*i)

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.2.1     v purrr   0.3.3
    ## v tibble  2.1.3     v dplyr   0.8.3
    ## v tidyr   1.0.0     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## -- Conflicts ------------------------------------------------------------------------------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(cluster)
```

``` r
data(iris)

str(iris)
```

    ## 'data.frame':    150 obs. of  5 variables:
    ##  $ Sepal.Length: num  5.1 4.9 4.7 4.6 5 5.4 4.6 5 4.4 4.9 ...
    ##  $ Sepal.Width : num  3.5 3 3.2 3.1 3.6 3.9 3.4 3.4 2.9 3.1 ...
    ##  $ Petal.Length: num  1.4 1.4 1.3 1.5 1.4 1.7 1.4 1.5 1.4 1.5 ...
    ##  $ Petal.Width : num  0.2 0.2 0.2 0.2 0.2 0.4 0.3 0.2 0.2 0.1 ...
    ##  $ Species     : Factor w/ 3 levels "setosa","versicolor",..: 1 1 1 1 1 1 1 1 1 1 ...

``` r
fitted <- pam(iris, k = 3, metric = "manhattan")

fitted
```

    ## Medoids:
    ##       ID Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ## [1,]   8          5.0         3.4          1.5         0.2       1
    ## [2,]  56          5.7         2.8          4.5         1.3       2
    ## [3,] 117          6.5         3.0          5.5         1.8       3
    ## Clustering vector:
    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ##  [71] 2 2 2 2 2 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 3 3 3 3 3
    ## [106] 3 2 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3
    ## [141] 3 3 3 3 3 3 3 3 3 3
    ## Objective function:
    ## build  swap 
    ## 1.166 1.138 
    ## 
    ## Available components:
    ##  [1] "medoids"    "id.med"     "clustering" "objective"  "isolation" 
    ##  [6] "clusinfo"   "silinfo"    "diss"       "call"       "data"
