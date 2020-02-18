Association Rule Learning
================

For transactional data we are going to work with the groceries dataset

``` r
data(Groceries)
head(Groceries)
```

    ## transactions in sparse format with
    ##  6 transactions (rows) and
    ##  169 items (columns)

The dataset is stored in whats known as a transactions matrix.

``` r
class(Groceries)
```

    ## [1] "transactions"
    ## attr(,"package")
    ## [1] "arules"

We can have a quick look using inspect

``` r
inspect(Groceries[1:5])
```

    ##     items                     
    ## [1] {citrus fruit,            
    ##      semi-finished bread,     
    ##      margarine,               
    ##      ready soups}             
    ## [2] {tropical fruit,          
    ##      yogurt,                  
    ##      coffee}                  
    ## [3] {whole milk}              
    ## [4] {pip fruit,               
    ##      yogurt,                  
    ##      cream cheese ,           
    ##      meat spreads}            
    ## [5] {other vegetables,        
    ##      whole milk,              
    ##      condensed milk,          
    ##      long life bakery product}

A great intuitive explaination of Support, Confidence and Lift can be
found
[here](https://www.hackerearth.com/blog/developers/beginners-tutorial-apriori-algorithm-data-mining-r-implementation).

efverf

![\\sqrt{4}=2](https://latex.codecogs.com/png.latex?%5Csqrt%7B4%7D%3D2
"\\sqrt{4}=2")

How do we choose values of support and confidence to best faciliate
finding the most interesting rules. Check out the following paper:
[Mining the Most Interesting
Rules](http://rakesh.agrawal-family.com/papers/kdd99rules.pdf)

Another interesting idea from Boxun Zhang on Quora:

> However, if you transform the output of Apriori algorithm (association
> rules) into features for a supervised machine learning algorithm, you
> can examine the effect of having different support and confidences
> values (while having other features fixed) on the performance of that
> supervised model (ROC, RMSE, and etc.). Then, the best values for
> support and confidence are the values that maximize, or minimize, the
> performance metric.

However, it is likely that the best way of choosing support and
confidence is through trial and error and domain knowledge.The aim is to
find a good balance between number of rules and interpretability.

### Apriori Algorithm

``` r
rules <- apriori(Groceries,
parameter = list(support = 0.01, confidence = 0.5, maxlen = 5))
```

    ## Apriori
    ## 
    ## Parameter specification:
    ##  confidence minval smax arem  aval originalSupport maxtime support minlen
    ##         0.5    0.1    1 none FALSE            TRUE       5    0.01      1
    ##  maxlen target   ext
    ##       5  rules FALSE
    ## 
    ## Algorithmic control:
    ##  filter tree heap memopt load sort verbose
    ##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
    ## 
    ## Absolute minimum support count: 98 
    ## 
    ## set item appearances ...[0 item(s)] done [0.00s].
    ## set transactions ...[169 item(s), 9835 transaction(s)] done [0.00s].
    ## sorting and recoding items ... [88 item(s)] done [0.00s].
    ## creating transaction tree ... done [0.01s].
    ## checking subsets of size 1 2 3 4 done [0.00s].
    ## writing ... [15 rule(s)] done [0.00s].
    ## creating S4 object  ... done [0.00s].

The

``` r
class(rules)
```

    ## [1] "rules"
    ## attr(,"package")
    ## [1] "arules"

``` r
inspect(rules[1:5])
```

    ##     lhs                     rhs             support confidence     lift count
    ## [1] {curd,                                                                   
    ##      yogurt}             => {whole milk} 0.01006609  0.5823529 2.279125    99
    ## [2] {other vegetables,                                                       
    ##      butter}             => {whole milk} 0.01148958  0.5736041 2.244885   113
    ## [3] {other vegetables,                                                       
    ##      domestic eggs}      => {whole milk} 0.01230300  0.5525114 2.162336   121
    ## [4] {yogurt,                                                                 
    ##      whipped/sour cream} => {whole milk} 0.01087951  0.5245098 2.052747   107
    ## [5] {other vegetables,                                                       
    ##      whipped/sour cream} => {whole milk} 0.01464159  0.5070423 1.984385   144

``` r
summary(rules)
```

    ## set of 15 rules
    ## 
    ## rule length distribution (lhs + rhs):sizes
    ##  3 
    ## 15 
    ## 
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##       3       3       3       3       3       3 
    ## 
    ## summary of quality measures:
    ##     support          confidence          lift           count      
    ##  Min.   :0.01007   Min.   :0.5000   Min.   :1.984   Min.   : 99.0  
    ##  1st Qu.:0.01174   1st Qu.:0.5151   1st Qu.:2.036   1st Qu.:115.5  
    ##  Median :0.01230   Median :0.5245   Median :2.203   Median :121.0  
    ##  Mean   :0.01316   Mean   :0.5411   Mean   :2.299   Mean   :129.4  
    ##  3rd Qu.:0.01403   3rd Qu.:0.5718   3rd Qu.:2.432   3rd Qu.:138.0  
    ##  Max.   :0.02227   Max.   :0.5862   Max.   :3.030   Max.   :219.0  
    ## 
    ## mining info:
    ##       data ntransactions support confidence
    ##  Groceries          9835    0.01        0.5

``` r
rules_sorted <- sort(rules, by = "support") # sort by support
inspect(rules_sorted[1:5]) 
```

    ##     lhs                     rhs             support confidence     lift count
    ## [1] {other vegetables,                                                       
    ##      yogurt}             => {whole milk} 0.02226741  0.5128806 2.007235   219
    ## [2] {tropical fruit,                                                         
    ##      yogurt}             => {whole milk} 0.01514997  0.5173611 2.024770   149
    ## [3] {other vegetables,                                                       
    ##      whipped/sour cream} => {whole milk} 0.01464159  0.5070423 1.984385   144
    ## [4] {root vegetables,                                                        
    ##      yogurt}             => {whole milk} 0.01453991  0.5629921 2.203354   143
    ## [5] {pip fruit,                                                              
    ##      other vegetables}   => {whole milk} 0.01352313  0.5175097 2.025351   133

The quality function contains same info as rules, just without the rules
columns.

``` r
qual <- quality(rules) # extract quality measures

qual
```

    ##       support confidence     lift count
    ## 1  0.01006609  0.5823529 2.279125    99
    ## 2  0.01148958  0.5736041 2.244885   113
    ## 3  0.01230300  0.5525114 2.162336   121
    ## 4  0.01087951  0.5245098 2.052747   107
    ## 5  0.01464159  0.5070423 1.984385   144
    ## 6  0.01352313  0.5175097 2.025351   133
    ## 7  0.01037112  0.5862069 3.029608   102
    ## 8  0.01230300  0.5845411 3.020999   121
    ## 9  0.01199797  0.5700483 2.230969   118
    ## 10 0.01514997  0.5173611 2.024770   149
    ## 11 0.01291307  0.5000000 2.584078   127
    ## 12 0.01453991  0.5629921 2.203354   143
    ## 13 0.01220132  0.5020921 2.594890   120
    ## 14 0.01270971  0.5230126 2.046888   125
    ## 15 0.02226741  0.5128806 2.007235   219

``` r
# compute p(A) and p(B)
pA <- qual$support/qual$confidence
pB <- qual$confidence/qual$lift
# compute lift upper and lower bounds
U <- apply(cbind(1/pA, 1/pB), 1, min)
L <- apply(cbind(1/pA + 1/pB - 1/(pA*pB), 0.01/(pA*pB), 0.5/pB, 0), 1, max)

sLift <- (qual$lift - L)/(U - L) # standardized lift
data.frame(rule = labels(rules), sLift) # print rules and sLift
```

    ##                                                      rule       sLift
    ## 1                           {curd,yogurt} => {whole milk} 0.009071877
    ## 2               {other vegetables,butter} => {whole milk} 0.147208122
    ## 3        {other vegetables,domestic eggs} => {whole milk} 0.105022831
    ## 4             {yogurt,whipped/sour cream} => {whole milk} 0.049019608
    ## 5   {other vegetables,whipped/sour cream} => {whole milk} 0.014084507
    ## 6            {pip fruit,other vegetables} => {whole milk} 0.035019455
    ## 7    {citrus fruit,root vegetables} => {other vegetables} 0.048248513
    ## 8  {tropical fruit,root vegetables} => {other vegetables} 0.169082126
    ## 9        {tropical fruit,root vegetables} => {whole milk} 0.140096618
    ## 10                {tropical fruit,yogurt} => {whole milk} 0.034722222
    ## 11         {root vegetables,yogurt} => {other vegetables} 0.000000000
    ## 12               {root vegetables,yogurt} => {whole milk} 0.125984252
    ## 13     {root vegetables,rolls/buns} => {other vegetables} 0.004184100
    ## 14           {root vegetables,rolls/buns} => {whole milk} 0.046025105
    ## 15              {other vegetables,yogurt} => {whole milk} 0.025761124
