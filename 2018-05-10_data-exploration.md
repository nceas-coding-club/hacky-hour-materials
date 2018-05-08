
# Tools and techniques for data exploration


#### *NCEAS Hacky Hour 2018-05-10*

This week's hacky hour covers workflows and tools for data exploration in R. Ideally, you have already identified a general family of analytical methods you want to use based on the nature of your research question(s) and how you designed your study, if you are the creator of the dataset. Data exploration is the next step for refining your choices and determining things like:

* Are there clearly erroneous values? (and other data QA)
* Do my data meet the assumptions of the tests or models I want to use?
* If not, what can I do about it?


We're roughly following the structure of [**Zuur et al. (2010) A protocol for data exploration to avoid common statistical problems**](https://besjournals.onlinelibrary.wiley.com/doi/epdf/10.1111/j.2041-210X.2009.00001.x) but skipping a couple sections. This paper is an awesome resource that I revisit often.


Here are the links that are referenced below:

#### Outliers
* The [**`outliers` package**](https://cran.r-project.org/web/packages/outliers/index.html) which has several tests for outliers
* [**This write-up**](http://r-statistics.co/Missing-Value-Treatment-With-R.html#3.%20Imputation%20with%20mean%20/%20median%20/%20mode) on methods for imputing missing values (or replacing outliers)
    + [**More outlier tricks**](http://r-statistics.co/Outlier-Treatment-With-R.html) from the same resource

#### Assumptions and Distributions
* The [**`olsrr` package**](https://cran.r-project.org/web/packages/olsrr/index.html) which has nice functions related to simple linear regression.
* [**This vignette**](https://cran.r-project.org/web/packages/olsrr/vignettes/residual_diagnostics.html) which covers **`olsrr`** functions for checking out your residuals.
* The [**`MASS` package**](https://cran.r-project.org/web/packages/MASS/MASS.pdf) which contains the **`fitdistr`** function for fitting distributions.
* The [**`fitdistrplus` package**](https://cran.r-project.org/web/packages/fitdistrplus/vignettes/paper2JSS.pdf) which extends **`fitdistr`** and contains plotting functions for visual assessment of distribution fit.
* [**This list**](http://www.cookbook-r.com/Statistical_analysis/Homogeneity_of_variance/#levenes-test) of a few ways to test for homogeneity of variance in R
* [**This vignette**](https://cran.r-project.org/web/packages/olsrr/vignettes/heteroskedasticity.html) about testing for homoscedasticity in `olsrr`.

#### Collinearity
* You can create beautiful plots of your correlation matrices, either for exploration or as an analytical end-product, with the package [**`corrplot`**](https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html).
* This vignette from `olsrr` covers [**tolerance and variance inflation factors**](https://cran.r-project.org/web/packages/olsrr/vignettes/regression_diagnostics.html).
* [**A paper**](http://cescos.fau.edu/gawliklab/papers/GrahamMH2003.pdf) that discusses multicollinearity and covers some methods (but not an exhaustive list) for analyzing data with collinear variables.
  + A package I love called [**`hier.part`**](https://cran.r-project.org/web/packages/hier.part/index.html) for doing hierarchical partitioning, and a [**cool example**](https://naes.unr.edu/weisberg/old_site/publications/Weisberg2014Acta.pdf) of its implementation.

#### Scatterplots and Interactions
* A [**quick-start guide**](http://www.cookbook-r.com/Graphs/Scatterplots_(ggplot2)/) and a [**more detailed guide**](http://tutorials.iq.harvard.edu/R/Rgraphics/Rgraphics.html) to drawing scatterplots in `ggplot2`.

***

### 1. Outliers

There are several ways to identify outliers, some of which are covered [**here**](http://r-statistics.co/Outlier-Treatment-With-R.html). The simplest visual method might be with **`boxplot`**. When you create a boxplot in R, the box represents the interquartile range (IQR; the 25th to 75th percentile). The value of 1.5 x IQR is the default distance beyond the IQR for the range of data to fall in, so **`boxplot`** draws the ends of the whiskers at the lowest and highest value within this range (but you can change this in the `range` argument if you want). Values falling beyond this range are considered outliers, and are plotted as circles. To locate the outliers in your dataframe, you can use **`boxplot.stats`**:

```{r, eval=FALSE}
outliers <- boxplot.stats(df$someVariable)$out
df[which(df$someVariable %in% outliers), ]
```

The [**`outliers` package**](https://cran.r-project.org/web/packages/outliers/index.html) includes a variety of other tests you can use. 

Next inspect the outliers and determine if they are likely to be errors or true values. Errors (from transcription or measurement error) can be dropped from the dataset with **`outlier.rm`** or have a new value imputed. Replacing the value with the mean, median, or min/max is common but there are other imputation methods, some of which are [**described here**](http://r-statistics.co/Missing-Value-Treatment-With-R.html#3.%20Imputation%20with%20mean%20/%20median%20/%20mode).

If the outliers are true values, you might consider transforming your data with a logarithmic transformation, or using a non-parametric method robust to outliers (e.g. methods not based on means, which are sensitive to extreme values). The same goes for section 2 (normality) and 3 (homogeneity of variance) if you have an uncommon distribution and/or are violating parametric assumptions.

### 2. Normality

A normal distribution (approximately symmetric around the mean) is an assumption of many standard statistical tests. Zuur et al. reference several papers explaining that it is better to assess normality with a visual inspection of your data rather than with statistical tests, which can be over-conservative. **`hist`** is the go-to in base R for simply checking out the distribution of the data. Adjust the number of bins with **`breaks = n`**. If the distribution looks normal, you're good for most tests that assume it. If it's borderline, and your test demands it, then the Shapiro-Wilk test (**`shapiro.test(x)`**) can provide a p-value to guide you. 

Don't forget to be clear on what exactly is supposed to be normal for the analysis you are doing. For t-tests, you are checking for normality in the response variable. For ANOVA and linear regression, you are checking for normality in the *residuals* of y vs. x.

For simple linear regression, there are several diagnostic tools for examining residuals (which you can view in base R by calling **`plot`** on a model created with **`lm`**). Two commonly used plots are:
* QQ plot: normally distributed residuals should stick to the line and not fly away at higher or lower values.
* Residuals vs. Fitted plot: normally distributed residuals will appear randomly scattered about the 0 line, and they are not correlated with fitted values.

[**This vignette covers them**](https://cran.r-project.org/web/packages/olsrr/vignettes/residual_diagnostics.html) using the [**`olsrr` package**](https://cran.r-project.org/web/packages/olsrr/index.html). This is a large package that takes a little while to install but it's easy to use, designed for teaching, and packed with great stuff so it's a good one to have around anyway.

You can also just throw down a histogram in base R. Here's an example with the free "crabs" dataset in the MASS package, testing frontal lobe size as a function of carapace length.

```{r}
m <- lm(FL ~ CL,MASS::crabs)
hist(residuals(m), breaks = 20)
```

##### *Normal!*

### 2A. If it's not normal, what is it?

If your data (and/or their residuals) aren't normally distributed, or they don't obviously belong to something like the Poisson distribution for count data, it can sometimes be difficult to tell which distribution they fit best. The **`fitdistr`** function in the indispensable **`MASS`** package is one function for assessing the fit of different distributions. There are also plotting functions in the [**`fitdistrplus` package**](https://cran.r-project.org/web/packages/fitdistrplus/vignettes/paper2JSS.pdf) that allow you to do this visually.

### 3. Homogeneity of variance

This is another common assumption of parametric analyses. In ANOVA, variance should be homogeneous (homoscedastic) among groups. In regression, variance should be homogeneous along the regression line. You can check for this in a regression context using the **residuals vs. fitted plot** (again, you can make one in `olsrr` or in base R).  If the residuals appear in a funnel shape that is close to the 0 line at one end and expands outward at the other, the variance is likely not homogeneous. 

You can also use tests including Levene's, Bartlett's, and Breusch-Pagan. Here are two [**useful**](http://www.cookbook-r.com/Statistical_analysis/Homogeneity_of_variance/#levenes-test) [**resources**](https://cran.r-project.org/web/packages/olsrr/vignettes/heteroskedasticity.html) for testing homoscedasticity.

### 4. Collinearity

Multicollinearity is a common issue in regression analyses. One way people check for collinearity is with a correlation matrix and some cutoff correlation value like 0.8. You can do this with the **`cor`** function or make attractive plots of your correlation matrix in the [**`corrplot`**](https://cran.r-project.org/web/packages/corrplot/vignettes/corrplot-intro.html) package.

You can also use variance inflation factors or tolerance values (you can [**calculate both**](https://cran.r-project.org/web/packages/olsrr/vignettes/regression_diagnostics.html) in `olsrr`).

This [**paper**](http://cescos.fau.edu/gawliklab/papers/GrahamMH2003.pdf) covers some methods (but not an exhaustive list) for analyzing data in the presence of multicollinearity. One really cool technique for isolating the unique vs. shared contributions of collinear variables is hierarchical partioning, which you can do with the [**`hier.part`**](https://cran.r-project.org/web/packages/hier.part/index.html) package. Here's an [**example**](https://naes.unr.edu/weisberg/old_site/publications/Weisberg2014Acta.pdf) of a paper that used this method with a concise explanation.

### 5. Scatterplots

You'll want to check out the shape of the relationship between x and y to see if you should be including a polynomial (or some other shape) term. Here's a [**quick-start guide**](http://www.cookbook-r.com/Graphs/Scatterplots_(ggplot2)/) and a [**more detailed guide**](http://tutorials.iq.harvard.edu/R/Rgraphics/Rgraphics.html) to scatterplots in `ggplot2`.

### 6. Interactions

If your study has a nested design of any kind, you will of course want to conduct your data exploration on your different groupings, using ordinary plotting functions or the **`interaction.plot`** function. But you should also consider possible interactions among variables even if you do not have a nested design. The effect of interacting independent variables on the dependent variable(s) may or may not already be a part of your set of research questions.

Sometimes the relationship between y and x is obscured by interactions. In other cases, an apparent relationship between x and y is actually structured by some other grouping variable. When the cryptic variable is included, the relationship may reverse direction. This is called Simpson's paradox and in ecology it can represent [scale-dependence](http://osenberglab.ecology.uga.edu/wp-content/uploads/2012/08/2000scheiner.pdf) among other things. In the example below, the overall pattern of the data show bird species richness declining over the course of a season. However, the within-site effect shows the opposite pattern. Richness actually increases over time as more migratory bird species arrive at each site, but sites surveyed later in the season (perhaps because they are at higher elevations) may have overall fewer species of birds.

![Image provided by Giancarlo Sadoti (https://gsadoti.wordpress.com/research/)](/Users/datateam/Desktop/simpsons.png){width=650px}
