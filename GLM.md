Generalized Linear Model
================

Modelling a dataset using the generalized linear model. A GLM can compare a response to one or more predictors. It is similar to linear regression and ANOVAs but can be used on non-normal data.

Therefore use this model when:

-   data is not normally distributed
-   variance of response variable depends on the mean
-   the GLM can be fit a distriubtion without perfroming tranformations (e.g. log transform)

Compare 80 individuals and see how effective each of the 3 different drugs are compared to a control group. The respose can is either 1 = cured or 2 = not cured, therefore, GLM can be used to fit the binomial distribution.

``` r
response <- c(1,0,0,0,1,0,0,1,0,0,0,0,0,0,1,0,0,0,1,0,1,0,0,0,1,0,0,1,0,0,1,0,0,0,1,0,0,0,1,0,1,0,0,0,1,0,0,1,1,1,1,1,0,0,1,0,1,1,1,0,1,0,0,0,1,0,1,1,1,1,1,1,1,1,1,0,1,1,1,1)
predictor <- c(rep("control",20),rep("drug1",20),rep("drug2",20),rep("drug3",20))
drug.trial <- data.frame(predictor,response)
```

Creating the GLM model

``` r
m2<- glm(response~predictor, data=drug.trial, family="binomial")

summary(m2) 
```

    ## 
    ## Call:
    ## glm(formula = response ~ predictor, family = "binomial", data = drug.trial)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.6651  -0.8446  -0.7585   1.0935   1.6651  
    ## 
    ## Coefficients:
    ##                Estimate Std. Error z value Pr(>|z|)   
    ## (Intercept)     -1.0986     0.5164  -2.127  0.03338 * 
    ## predictordrug1   0.2513     0.7105   0.354  0.72354   
    ## predictordrug2   1.2993     0.6846   1.898  0.05772 . 
    ## predictordrug3   2.1972     0.7303   3.009  0.00262 **
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 110.453  on 79  degrees of freedom
    ## Residual deviance:  96.947  on 76  degrees of freedom
    ## AIC: 104.95
    ## 
    ## Number of Fisher Scoring iterations: 4

Essentially the results show the comparsion of each drug againts the control. Also want to determine which drug is the best. First, the chisquare test is used to determine if the significance is significantly different from zero.

``` r
anova(m2, test="Chisq") ## chisq test on predictor from model
```

    ## Analysis of Deviance Table
    ## 
    ## Model: binomial, link: logit
    ## 
    ## Response: response
    ## 
    ## Terms added sequentially (first to last)
    ## 
    ## 
    ##           Df Deviance Resid. Df Resid. Dev Pr(>Chi)   
    ## NULL                         79    110.453            
    ## predictor  3   13.506        76     96.947 0.003661 **
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

To determine which drug is most effective, use pairwise comparisons.

``` r
#install.packages("lsmeans")
library(emmeans)
```

``` r
emmeans(m2, pairwise~predictor, adjust="tukey") ##conduct pairwise comparisons on predictor
```

    ## $emmeans
    ##  predictor     emmean        SE  df   asymp.LCL   asymp.UCL
    ##  control   -1.0986123 0.5163973 Inf -2.11073247 -0.08649211
    ##  drug1     -0.8472979 0.4879498 Inf -1.80366190  0.10906617
    ##  drug2      0.2006707 0.4494666 Inf -0.68026760  1.08160899
    ##  drug3      1.0986123 0.5163973 Inf  0.08649211  2.11073247
    ## 
    ## Results are given on the logit (not the response) scale. 
    ## Confidence level used: 0.95 
    ## 
    ## $contrasts
    ##  contrast          estimate        SE  df z.ratio p.value
    ##  control - drug1 -0.2513144 0.7104655 Inf  -0.354  0.9848
    ##  control - drug2 -1.2992830 0.6846068 Inf  -1.898  0.2289
    ##  control - drug3 -2.1972246 0.7302961 Inf  -3.009  0.0140
    ##  drug1 - drug2   -1.0479686 0.6634118 Inf  -1.580  0.3903
    ##  drug1 - drug3   -1.9459101 0.7104655 Inf  -2.739  0.0313
    ##  drug2 - drug3   -0.8979416 0.6846068 Inf  -1.312  0.5555
    ## 
    ## Results are given on the log odds ratio (not the response) scale. 
    ## P value adjustment: tukey method for comparing a family of 4 estimates

Look at the p-values to determine which predictors are significantly different from the control. Since drug 3 has a p-value less than 0.05, this is the only drug that is significantly different from the control.
