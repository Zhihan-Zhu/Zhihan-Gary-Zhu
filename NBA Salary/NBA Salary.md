NBA Player Salary
================
Zhihan Zhu

    ## Loading required package: knitr

Introduction
------------

Once the final ends in June each year, the National Basketball Association (NBA) season end. However, the fans' passion do not go away, because the next season is on the way. Every team is working on renew the contracts with their superstars and offer. It is a big challenge for the team organizations to determine appriopriate contract values to make a balanced team. Moreover, since the salary cap, which is comprised of Basketball Related Income (BRI) which consists of revenue generated from ticket sales, national and local broadcast deals, in arena concession sales, signage, stadium naming rights, and merchandise sales, keeps increasing, the salaires from previous years cannot be a good guide, and it becomes more difficult to determine contract values.

What should players focus on the most to get high salaries is always a hot topic. Berri, Brook, and Schmidt conclude that players can get well paid by simply focus on scoring (Schmidt et al.). It seems resonalbe since soring is usually more impressive to audience than other statistics such as rebound and assistance.

In this paper, I use the the 2017-2018 season players's salaries and stats of regular seaseon data to explore the most important factors that affect players salaries. After exploring the data, I choose to use a multiple linear regression model to obtain the coefficients of several independent variables.I first do a simulation to choose between nonparametric bootstrap and parametric bootstrap based on their power of Hypothesis Test that whether coefficients equal to zero. Then I build the model, and use the preferred bootstrap method to create confidence interval for the coefficients.

Data
----

This paper uses the 2017-2018 season players's salaries and stats of regular seaseon from Basketball Reference Website (“2017-18 NBA Player Stats”). Some players have been traded during the season and has record on more than one team. Therefore, these players stats will be averaged and put in the row of their last team. Moreover, some players have no records in some columns such as field goal percentage because they never made any shot attempts. These nan values would be simply repalced by zero to avoid computational errors.

Now let us take a first look at the salary data: 

```r
stats=read.csv('stats1718.csv',header = TRUE)

new_stats=stats[,c('Player','Age','Tm','MP','FG.','X3P.','X2P.','eFG.','FT.','TS.','PER','TRB','AST','STL','BLK','TOV','PF','PS.G')]

new_stats$Player=as.character(new_stats$Player)
new_stats$Tm=as.character(new_stats$Tm)

a=unique(new_stats$Player)
name=c()
for (i in 1:length(a)){
  if (sum(new_stats$Player==a[i])>1){
    name=c(name,a[i])
  }
}

for (i in 1:length(name)){
  rowindex=which(new_stats$Player==name[i] & new_stats$Tm != 'TOT')
  new_stats[rowindex[1]-1,]$Tm=new_stats[tail(rowindex,1),]$Tm
  new_stats=new_stats[-rowindex,]
}


salary=read.csv('NBA_season1718_salary.csv',header = TRUE)
salary_stats=merge(new_stats,salary,by = c('Player','Tm'))
colnames(salary_stats)[colnames(salary_stats)=='season17_18']="Salary"
salary_stats=salary_stats[,c(1:2,20,3:18)]
salary_stats[is.na(salary_stats)] = 0
hist(salary_stats$Salary/1e7,main='Histogram of NBA player Salary 2017-18',xlab='Salary (millions)')
```

![](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/NBA%20Salary/hist.png)

As superstars, only 8% players have contracts greater than $20,000,000. On the contrary, over 61% players' salaries are below $5,000,000. This show a big gap between players. However, their contribution on the court differ a lot as well. As shown in Table 1, it is reasonalbe for the top players to have much higher salaries. Each of their statistics outstands the "Low-Salary" Players, especially Points Per Game, which is almost three times of the other group.

```r
com1=apply(salary_stats[salary_stats$Salary<=5000000,][,5:19],2,mean)
com2=apply(salary_stats[salary_stats$Salary>=20000000,][,5:19],2,mean)
table=data.frame(com1,com2)
colnames(table)=c('Salary < $5,000,000','Salary >$20,000,000')
kable(table,caption = 'Table 1: Comparison of average stats of two groups with low and high salaries')

```

<sub>**Table 1: Comparison of average stats of two groups with low and high salaries**</sub>

|      |  Salary &lt; $5,000,000|  Salary &gt;$20,000,000|
|------|-----------------------:|-----------------------:|
| MP   |              16.6670251|              31.1589744|
| FG.  |               0.4372401|               0.4870256|
| X3P. |               0.2811183|               0.3193077|
| X2P. |               0.4741864|               0.5387949|
| eFG. |               0.4948996|               0.5402564|
| FT.  |               0.6752401|               0.7637692|
| TS.  |               0.5231254|               0.5805128|
| PER  |              12.5458781|              20.1666667|
| TRB  |               2.8695341|               6.9923077|
| AST  |               1.4982079|               3.7794872|
| STL  |               0.5297491|               0.9871795|
| BLK  |               0.3261649|               0.8230769|
| TOV  |               0.9186380|               2.1333333|
| PF   |               1.5000000|               2.2000000|
| PS.G |               6.5612903|              18.0205128|

```r
pairs(salary_stats[,c(3:4,11:19)])
```

![](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/NBA%20Salary/scatter.png)

The scatterplot matrix clearly shows there are relationships among salaries and the players' performance. The relationship between salaries and shooing percentage is not very obvious since most of the players have TS% (True Shooting Percentage) around 50%. This plot suggests positive linear relationships between salaries and TRB, AST, STL, BLK, TOV, PF and PS.G. Moreover, the linear pattern between salairies and PS.G is the most obvious.

Methods
-------

Since there is no obvious nonlinear relationship in the above plot, a linear regression model is used in this paper (Statistical Computing with R \[Book\]). The dependent variable for this paper is the salaries. By exploring the scatterplot matrix of all the variables, the independent variables are chosen to be Age (Age), TS% (True Shooting Percentage), TRB (Total rebounds per game), AST (Assist per game), STL (Steal per game), BLK(Block per game), TOV (Turnovers per game), PF(Personal fouls per game), PS.G (Points per game).

MP (Minutes per game) is not considered in this paper due to the result of the fact that usually players with more play time have greater statistics, which makes it closely relates to the other variables. PER (Player Efficiency Rating), which reflect a player's performance by taking into account all statistics, is also ruled out. Since exploring the contribution of each statistic to salary is the main purpose, PER is not a good option.

TS% is chosen over FG% (Field Goal Percentage), 3P% (3-Point Percentage), 2p% (2-Point Percentage), eFG% (Effective Field Goal Percentage) and FT% (Free Throw Percentage) because True shooting percentage is a measure of shooting efficiency that takes into account field goals, 3-point field goals, and free throws (“Glossary”). It describes a player's scoring ability in a general way.

The model is as follows:

*Salary* = *β*<sub>0</sub> + *β*<sub>1</sub> \* *Age* + *β*<sub>2</sub> \* *TS*%+*β*<sub>3</sub> \* *TRB* + *β*<sub>4</sub> \* *AST* + *β*<sub>5</sub> \* *STL* + *β*<sub>6</sub> \* *BLK* + *β*<sub>7</sub> \* *TOV* + *β*<sub>8</sub> \* *PF* + *β*<sub>9</sub> \* *PS*.*G* + *ϵ*

where *β*<sub>*i*</sub>, *i* = 0, ..., 9 are all the regression coefficients, and *ϵ* ∼ *N*(0, *σ*<sup>2</sup>) for some *σ*.

This model assumes Multivariate Normality, Multivariate Normality, and Homoscedasticity

As the distribution of the population is uncertain. I will create bootstrap to compute the coefficients to get an estimate of the confidence interval.

Simulations
-----------

Before directly building the model, a simulation will be conducted to choose between nonparametric bootstrap and parametric bootstrap. The criterion is to choose the one that has higher power of the hypothesis test that *H*<sub>0</sub> : *β*<sub>*i*</sub> = 0 vs *H*<sub>*a*</sub> : *β*<sub>*i*</sub> ≠ 0 for *i* = 0, ..., 9.

Using the original independent variable data, I will generate mock data of Salary by letting:

*β*<sub>0</sub> = 15, *β*<sub>1</sub> = 3, *β*<sub>2</sub> = 5, *β*<sub>3</sub> = 4, *β*<sub>4</sub> = 6, *β*<sub>5</sub>, 5.5, *β*<sub>6</sub> = 3, *β*<sub>7</sub> = −3.2, *β*<sub>8</sub> = −2.5, *β*<sub>9</sub> = 10

and adding a error term ϵ *ϵ* ∼ *N*(0, *σ*<sup>2</sup>) with *σ* = 15 and 20.

The nonparametric bootstrap method draws sample from the original dataset, and compute the coefficients on the sample. In the parametric bootstrap method, the independent variables are fixed, and resample is draw from ![equation](https://github.com/Zhihan-Zhu/Zhihan-Gary-Zhu/blob/master/NBA%20Salary/equation.png). For each method, a total of 100 bootstrap replications are created.

Using a Monte Carlo approach, the power of each method shown in Table 2 and Table 3 shows that nonparametric is more powerful. Therefore, in the remaining of the paper, nonparametric bootsrtrap method would be used for analysis.

```r
lm_coef <- function(data,index){
  d=data[index,]
  model=lm(Salary~.,data=d)
  model$coefficients
}

lm_coef_2 <- function(data,index){
  SS_model=lm(Salary~.,data=data)
  ehat=SS_model$residuals
  yhat=SS_model$fitted.values
  estar=ehat[index]
  ystar=yhat+estar
  model=lm(ystar~.-Salary,data=data)
  model$coefficients
}

coeff_test <- function(error){
  salary_stats_sim$Salary=m%*%b+error
  data=salary_stats_sim[,c(1:9,11)]
  lm_sim_boot=boot(data = data, statistic = lm_coef,R = 100,parallel = "snow", cl = makeCluster(2), ncpus = 2)
  obs_b=lm_sim_boot$t0
  bs=lm_sim_boot$t
  
  pvalues=c()
  for (i in 1:length(obs_b)){
    pvalues=c(pvalues, 2*min(mean(bs[,i]>=0),mean(bs[,i]<=0)))
  }
  pvalues
}

coeff_test_2 <- function(error){
  salary_stats_sim$Salary=m%*%b+error
  data=salary_stats_sim[,c(1:9,11)]
  lm_sim_boot_2=boot(data = data, statistic = lm_coef_2,R = 500,parallel = "snow", cl = makeCluster(2), ncpus = 2)
  obs_b=lm_sim_boot_2$t0
  bs=lm_sim_boot_2$t
  
  pvalues=c()
  for (i in 1:length(obs_b)){
    pvalues=c(pvalues, 2*min(mean(bs[,i]>=0),mean(bs[,i]<=0)))
  }
  pvalues
}
```


```r
load('power.rda')
p1=apply(test,1,function(x){mean(x<=0.05)})
p2=apply(test2,1,function(x){mean(x<=0.05)})
p3=apply(test3,1,function(x){mean(x<=0.05)})
p4=apply(test4,1,function(x){mean(x<=0.05)})

table=data.frame(p1,p2)
rownames(table)=c(paste("beta",0:9))
colnames(table)=c('nonparametric','parametric')
kable(table,caption = 'Table 2: power for sigma=20')
```

<sub>**Table 2: power for sigma=20**</sub>

|        |  nonparametric|  parametric|
|--------|--------------:|-----------:|
| beta 0 |           0.57|        0.50|
| beta 1 |           1.00|        1.00|
| beta 2 |           0.18|        0.14|
| beta 3 |           1.00|        1.00|
| beta 4 |           1.00|        1.00|
| beta 5 |           0.42|        0.37|
| beta 6 |           0.19|        0.16|
| beta 7 |           0.24|        0.07|
| beta 8 |           0.29|        0.31|
| beta 9 |           1.00|        1.00|

```r
table=data.frame(p1,p2)
rownames(table)=c(paste("beta",0:9))
colnames(table)=c('nonparametric','parametric')
kable(table,caption = 'Table 3: power for sigma=15')
```

<sub>**Table 3: power for sigma=15**</sub>


|        |  nonparametric|  parametric|
|--------|--------------:|-----------:|
| beta 0 |           0.57|        0.50|
| beta 1 |           1.00|        1.00|
| beta 2 |           0.18|        0.14|
| beta 3 |           1.00|        1.00|
| beta 4 |           1.00|        1.00|
| beta 5 |           0.42|        0.37|
| beta 6 |           0.19|        0.16|
| beta 7 |           0.24|        0.07|
| beta 8 |           0.29|        0.31|
| beta 9 |           1.00|        1.00|

Analysis
--------

The linear regression model concludes that Age, TRB, PF and PS.G are important at 0.05 level of significance, and AST is important at 0.1 level of significance. All the other variables are not statistically significant. The bootstrap 95% confidence interval also support this conclusion.

There are positive relationship between Salary and Age, TRB, AST and PS.G, which are not suprising. As players play longer in the league, they get more skilled and are more likely to recieve good contracts. The effects of TRB, AST and PS.G support that statement that scoring is the most important thing people care since these three statistics have the most direct relation to scoring. Personal Fouls, on the other way, has a negative effect toward Salary. It also makes sense, since foul will give the opponent easy ways to score points.

```r
salary_stats_reg=salary_stats[,c(3,4,11,13:19)]

SS_model=lm(Salary~.,data = salary_stats_reg)

summary(SS_model)
```

    ## 
    ## Call:
    ## lm(formula = Salary ~ ., data = salary_stats_reg)
    ## 
    ## Residuals:
    ##       Min        1Q    Median        3Q       Max 
    ## -14063574  -3439671   -463144   2827936  28442804 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -11810250    1921290  -6.147 1.74e-09 ***
    ## Age            492525      58950   8.355 8.15e-16 ***
    ## TS.          -1882719    2223055  -0.847   0.3975    
    ## TRB            746677     178204   4.190 3.36e-05 ***
    ## AST            526804     301553   1.747   0.0813 .  
    ## STL            385457     868382   0.444   0.6573    
    ## BLK            961746     924752   1.040   0.2989    
    ## TOV           -970419     828031  -1.172   0.2418    
    ## PF           -1240722     537999  -2.306   0.0216 *  
    ## PS.G           616283      83809   7.353 9.17e-13 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 5351000 on 451 degrees of freedom
    ## Multiple R-squared:  0.4946, Adjusted R-squared:  0.4846 
    ## F-statistic: 49.05 on 9 and 451 DF,  p-value: < 2.2e-16


```r
lm_sim_boot=boot(data = salary_stats_reg, statistic = lm_coef,R = 1000)


interval=c()
for (i in 1:10){
  ci=boot.ci(lm_sim_boot,type = 'perc',index=i)$perc[4:5]
  ci=round(ci,4)
  interval=c(interval,paste('(',ci[1],',',ci[2],')',sep = ''))
}

table=data.frame(lm_sim_boot$t0,interval)
colnames(table)=c('estimated beta','95% confidence interval')

kable(table,caption = 'Table 4: Summary of bootstrap result')
```

<sub>**Table 4: Summary of bootstrap result**</sub>


|             |  estimated beta| 95% confidence interval        |
|-------------|---------------:|:-------------------------------|
| (Intercept) |     -11810249.7| (-14976838.6495,-8519276.9043) |
| Age         |        492525.3| (374424.7817,612966.3941)      |
| TS.         |      -1882718.6| (-4882278.1074,584405.8959)    |
| TRB         |        746676.5| (282887.4413,1142075.0974)     |
| AST         |        526804.0| (-157913.0284,1313034.7553)    |
| STL         |        385456.8| (-1571462.3578,2301910.7937)   |
| BLK         |        961746.4| (-1118502.1663,3253475.5812)   |
| TOV         |       -970418.6| (-2918121.2328,851341.7181)    |
| PF          |      -1240721.6| (-2258419.0325,-126285.4062)   |
| PS.G        |        616282.8| (427601.638,823970.0844)       |

Disucssion
----------

In this paper, I build a multiple linear regression model on 2017-2018 season NBA player salaries and statistics dataset, and use nonparametric bootstrap to create confidence interval for each statistics to find the important statistics that can let a NBA player get high salary. It turns out that Age, Rebounds, Assistance and points per game are significant.

However, the data of only one year is not good enough. Since players usually have long term contracts more than one year, it might be inappropriate to evluate whether they deserve the salaries based on the statistic of one season. A player may get high-value contract for 3 years when they have great performance, but get an injury and cannot fully recovery from it in the next year. Therefore, these factors could be useful to take into consideration when building a model. Moreover, the *R*<sup>2</sup> of the model is only 0.49, which shows that linear model may not be the best option. Hence, other models should also be investigated.

Reference
---------

“2017-18 NBA Player Stats: Per Game.” *Basketball-Reference.Com*, <https://www.basketball-reference.com/leagues/NBA_2018_per_game.html>. Accessed 17 Dec. 2018. “Glossary.” *Basketball-Reference.Com*, <https://www.basketball-reference.com/about/glossary.html>. Accessed 18 Dec. 2018. Schmidt, Martin, et al. “Does One Simply Need to Score to Score?” *International Journal of Sport Finance*, vol. 2, Feb. 2007, pp. 190–205. *Statistical Computing with R \[Book\]*. <https://www.oreilly.com/library/view/statistical-computing-with/9781498786591/>. Accessed 18 Dec. 2018.
