---
title: 'Exercises'
teaching: 10
exercises: 2
---



:::::::::::::::::::::::::::::::::::::: questions 

- How can I get routine in probabilistic programming?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

The purpose of this Episode is to provide material for practicing probabilistic programming. The exercises are listed under the Episode they approximately refer to. 

::::::::::::::::::::::::::::::::::::::::::::::::

# 1. Basics

## 1.1 Grid approximation: normal model with unknown mean 

Generate 1000 data points from the normal model. Use a randomly generated mean parameter, $\mu \sim N(0,1)$ and set the standard deviation $\sigma=1$.

The grid approximation for this model was introduced in Episode XX but the implementation doesn't work for the generated data. Locate the source of error and make the necessary modifications to get the program working.

Plot the posterior of $\mu$. 


 
## 1.2 Grid approximation: Gamma-Poisson 

The Gamma-Poisson model can be stated as: 

$$y_i \sim \text{Pois}(\lambda) \\
 \lambda \sim \Gamma(1, 1), $$

where $y_i$ are non-negative data points, and Pois is the Poisson distribution with rate parameter $\lambda > 0$.
Implement a grid approximation for this model.

Apparently (https://en.wikipedia.org/wiki/Poisson_distribution), the number of chewing gum on a sidewalk tile is approximately Poisson distributed.

Estimate the average number of gum on a Reykjavik side walk tile (lambda), using the data 

``` r
y <- c(2,7,4,3,5,2,7,5,5,5)
```



## 1.3 Less data means bigger prior effect 

Show that as the amount of available data increases, the effect of the prior decreases. 

Instructions: 

  - Simulate a series of coin tosses:
    - Generate $p \sim \text{Uniform}(0, 1)$. 
    - Simulate a sequence of 50 tosses with Pr(heads) = p. 
  - Fit the grid approximation using the first 1, 5, 10, 15,..., 50 tosses. 
    - Use a Beta prior for p.
  - Compare the posteriors the prior.


  

## 1.4 Grid approximation: for a normal model with unknown mean and standard deviation 


The following data is a collection of daily milk yield (in liters) for dairy cows.


``` r
X <- c(30.25, 34.98, 29.66, 20.14, 23.92, 38.61, 36.89, 34.68, 25.83, 29.93)
```


Using the grid approximation for the normal model, estimate the average daily yield $\mu$.

Use some non-uniform priors.

Plot the marginal posterior for $\mu$ and compute the 90% credible interval for the marginal. 

What is the probability that the average daily milk yield is more than 30 liters?



## 1.5 Sampling the Gamma 

Let's model the following observations X with the exponential likelihood, $\text{Exp}(\lambda)$:


``` r
X <-  c(0.166, 1.08, 1.875, 0.413, 1.369, 0.463, 0.735, 
       0.24, 0.774, 1.09, 0.463, 0.916, 0.225,
        0.889, 0.051, 0.688, 0.119, 0.078, 1.624, 0.553, 0.523, 
       0.644, 0.284, 1.744, 1.468)
```




If we use a $\Gamma(2, 1)$ prior, the posterior distribution can be shown to be 

$p(\lambda | X) = \Gamma(2 + n, 1 + X_1 + X_2 + ... + X_n),$

where $n$ is the number of observations. 

Generate 5000 samples from the posterior and compute
1. the posterior mean and mode
2. the 50% and 95% credible intervals
3. the probabilities $Pr(\lambda > 1), Pr( 1 < \lambda < 1.5), Pr(\lambda < 1 \text{  or } \lambda > 1.5)$


## 1.6 Grid approximation: Cauchy distribution 

(Emulated from BDA3: p59. Ex.11)

Suppose y1,...,y5 are independent samples from a Cauchy distribution with scale 1 and unknown location $\theta$. Given the observations $(y_1, . . . , y_5) = (43, 44, 45, 46.5, 47.5)$:

a) Compute the unnormalized posterior density function, $p(\theta)p(y|\theta)$, on a grid of points $\theta = 0, 1/m , 2/m ,..., 100$, for some large integer $m$. Using the grid approximation, compute and plot the normalized posterior density function as a function of $\theta$. Assume a uniform prior for over [0, 100]. 
b) Generate 1000 samples of $\theta$ from the posterior and plot a histogram of the samples.
c) Use each of the samples generated in b) to generate a new data point $y_6$ from the likelihood function and plot a histogram of these draws.


## 1.7 Sampling the Normal 

The posterior for the normal model with unknown $\mu$ and $\sigma$ can be sampled as follows (BDA3:p.65):

1. Sample the variance from $\sigma^2 \sim Inv-\chi^2(n-1, \text{Var}(X))$
2. Sample the mean from $\mu | \sigma^2 ~ N(\bar{X}, \frac{\sigma^2}{n})$

Here $\bar{X}$ is the mean of the data points $X = \{X_1, X_2, ..., X_n\}$.

a) Generate 5000 samples from the posterior using the data


``` r
X <- c(21.1, 20.8, 21.9, 20.5, 18.7, 24.1, 18.6, 15.4, 16.9, 20.8)
```



b) Compute the posterior for the coefficient of variation $CV = \frac{\sigma}{\mu}$.
c) Generate samples for an unseen data point $\tilde{X}$.
d) Compare the distribution in c) to one generated using only the MAP estimate. Is there a discrepancy and why?


## 1.8 HPDI 

Another approach to summarizing the posterior is to compute the *shortest* interval that contains $p\%$ of the posterior. Such interval is called highest posterior density interval (HPDI). 

Write a function that returns the highest posterior density interval, given a set of posterior samples.

Assume that the analytical form of a posterior is known to be Gamma(3, 20). Generate samples from this distribution and compute the 90% HPDI. Compare it to the 90% credible interval.

Hint: If you sort the posterior samples in order, each set of n consecutive samples contains $100 \cdot \frac{n}{N} \%$ of the posterior, where $N$ is the total number of samples.  


<!-- ************************************************************************ -->

# 2. Stan


## 2.1 Gamma-Poisson model 

The Gamma-Poisson model can be stated as: 

$$y_i \sim \text{Pois}(\lambda) \\
 \lambda \sim \Gamma(1, 1), $$

where $y_i$ are non-negative data points, and Pois is the Poisson distribution with rate parameter $\lambda > 0$.

Implement the model in Stan. Do it so that the parameter beta is input as part of data.
Heuristically, what effect does altering beta have on the prior shape?

Fit the model using the data 

``` r
y <- c(2,7,4,3,5,2,7,5,5,5)
```

Compute the MAP, mean and 90% CIs and include them in a figure with a histogram of posterior samples.


## 2.2 Dice

Write a Stan program that implements the following statistical model: 

$$y \sim \text{categorial}(\theta) \\
\theta \sim \text{Dir}(1, 1, ..., 1), $$
  
where $\theta$ is a 6-dimensional probability vector and Dir is the Dirichlet distribution.

Use the program to assess the fairness of a 6 sided dice. In other words, estimate the probabilities of each side on a roll using the following data.


``` r
X <- c(3, 2, 6, 3, 6, 2, 5, 6, 5, 6, 4, 1, 4, 2, 5, 4, 6, 6, 5, 4, 1, 3, 3, 4, 2, 3, 4, 4, 4, 1, 1, 3, 4, 4, 1, 6, 4, 6, 5, 5, 2, 6, 1, 1, 4, 4, 1,  6,  6, 1, 6, 4, 5, 5, 3, 4, 2, 6, 6, 5, 2, 6, 1, 1, 4, 4, 4, 6, 3, 5, 3, 6, 5, 3, 3, 2, 3, 3, 5, 3, 3, 4, 6, 4, 3, 6, 6, 4, 4, 6, 5, 1, 3, 5, 1, 2, 4, 4, 1) 
```



  
1. Plot the marginal posteriors for each $\theta_i$. 

2. Is the dice fair? Quantify this somehow

Hint: You can e.g. compute a posterior probability difference of some of the dice faces. 


## 2.3 Normal model 

Implement the Normal model in Stan.

In the generated quantities block:
  - Generate a posterior predictive distribution
  - Generate posterior for $CV = \frac{\mu}{\sigma}.$

Fit the model using the data

``` r
X <- c(3.7, 2.8, 4.03, 2.11, 2.58, 0.96, 1.74, 0.34, 0.75, 2.07)
```

Plot the posterior distribution and color the points according to the condition $CV < 1$.


## 2.4. Time series modeling 


The AR(1) process is defined by the recursion: 
$$x_i \sim N(\phi \cdot x_{i-1}, \sigma^2),$$
where $i$ is a time index. In other words, given some initial value $x_1$, the next value $x_2$ is generated from a normal distribution with mean $\phi\cdot x_1$ and variance $\sigma^2$. This pattern continues for the successive values.
 
Write a Stan program that estimates the parameters $\phi$ and $\sigma$ of the AR(1) process. 

Using the data in `data/time_series.txt`, do the following: 
 
1. Estimate the parameters $\phi$ and $\sigma$.
2. Starting from $x_{new} = 5$ predict the process 50 values into the future and plot the predictions. 



# 3. MCMC

## 3.1 Binomial model 

Implement the Metropolis-Hastings algorithm for the beta-binomial model.

Assume there are 50 people of whom 7 are left-handed. Estimate the probability of being left-handed in the wider population. 

Use $p(\theta^* | \theta_{now}) = \text{Beta}(\theta^* | 2, 2)$ as your proposal distribution, where $\theta^*$ is the proposal and $\theta_{now}$ the current sample. 

Compute the proportion of accepted proposals for the sampler. 



## 3.2. Gibbs sampler 


Consider the distribution 
$$ p(x, y) = C \cdot \exp^{-(x^2y^2 + x^2 + y^2 -8x -8y)/2},$$ where $C$ is a normalizing constant. It is known that the conditional distribution of x given y is the normal distribution $p(x|y) = N(\mu, \sigma^2)$, where the mean $\mu = 4/(1 + y^2)$ and the standard deviation $\sigma = \sqrt{1/(1 + y^2)}$. Due to symmetry, $p(y|x)$ can be recovered simply by changing $y$'s to $x$'s in $p(x|y)$. 
 
The Gibbs sampler is a special case of the Metropolis-Hastings algorithm. It can be stated as follows:

 1) Choose some initial values for the parameters, $x_0$ and $y_0$ in our case. 
 2) For $i = 1, ..., N$ do:
    a) Draw $x_i$ from $p(x_i|y_{i-1})$
    b) Draw $y_i$ from $p(y_i|x_i)$

(In this notation $x_i$ refers to the $x$ sample at step $i$, $y_{i-1}$ to the y sample at step $i-1$ and so on)
 
Build a Gibbs sampler that draws samples from $p(x, y)$. Visualize the resulting distribution. 



## 3.3. Gamma with discrete rate 

The data

``` r
X <- c(10.61, 4.76, 11.25, 5.55, 23.81, 7.45, 17.31, 15.08, 8.19, 15, 4.29, 10.95, 15.45, 10.09, 7.96, 12.35, 11.43, 7.33, 8.17, 21)
```

     
is modelled with a $\Gamma(\alpha, \beta)$ distribution, 
where the shape $\alpha = 5$, but the rate $\beta$ is unknown. 
However, it is known that beta can only take values $1.1^N$,
where $N$ is an integer. 

Implement a Metropolis-Hastings sampler for this model.

Use a proposal distribution that gives equal probability
for moving one step up $(N^* = N+1)$ or down $(N^* = N-1)$,
and some positive probability for staying still $(N^*=N)$.

Generate the initial value randomly: $N_0 \sim \text{Uniform}(-100, 100)$ and compute an estimate for $\beta$. 



## 3.4. $\hat{R}$

Write a function that returns the $\hat{R}$ statistic for a collection of MCMC chains. Compute $\hat{R}$ for the samples from one of the exercises 3.1-3.3, and plot the trace plots.    

See p.285 in BDA3 for the definition of $\hat{R}$. 

# 4. Hierarchical models

## 4.1. Model analysis 

Examine the following statistical models.
Determine if they exhibit a hierarchical structure. If not, introduce modifications to make them hierarchical.

Below, the subscript $i$ denotes a data point, while $g$ designates a population subgroup.

a)

$$
y_{g, i} \sim N(a_g + b \cdot x_{g,i}, \sigma^2) \\
a_g \sim N(0, 1) \\
b \sim N(0, 1)  \\
\sigma \sim \text{Exponential}(1) \\
$$

b)

$$
y_{g,i} \sim \text{Binom}(N, p_{g,i})  \\
\text{logit}(p_{g,i}) = a_g + b \cdot x_i  \\ 
a_{g} \sim N(\alpha, 1)   \\
\alpha \sim N(0, 10)   \\
b \sim N(0, 1)
$$

c)

$$
X_i \sim \Gamma(\alpha, \beta)   \\
\alpha \sim \Gamma(1, 1)   \\
\beta \sim \Gamma(1, 1)
$$



## 4.2. Hierarchical Gamma-Poisson 

A hierarchical Gamma-Poisson model can be stated as follows:

$$
y_i \sim \text{Pois}(\lambda) \\
\lambda \sim \Gamma(1, \beta) \\
\beta \sim \Gamma(2, 1) \\
$$

Use data 

``` r
y <-  c(2,7,4,3,5,2,7,5,5,5)
```

Implement the model in Stan. 

Identify the data subgroups. Visualize the posterior distribution of beta. Generate samples from the population distribution of $\lambda$ in the generated quantities block and plot them in a histogram.



## 4.3. Hierarchical Poisson regression 

A Poisson regression model can be specified as $y_i \sim \text{Pois}(\exp^{\alpha + \beta x_i})$, where $x_i$ and $y_i$ are corresponding data points.

Apply Poisson regression to model the annual rental count $(y_i)$ for homestay apartments located at a distance $x_i$ from the city center.

The data comprises 25 randomly selected apartments in 10 different cities. The file `data/apartment_x.txt` contains the distances of apartments to the center, with rows representing cities and columns representing apartments. The file `data/apartment_y.txt` contains the number of rentals during the surveyed year for the same apartments.

Build a Stan program for hierarchical Poisson regression, with hierarchical structure for both $\alpha$ and $\beta$.

Generate and visualize posteriors for the population distributions of $\alpha$ and $\beta$. What is the probability that $\beta > 0$ in the population? What implications does $\beta > 0$ have in terms of the application?


## 4.4. Hierarchical binomial model 

As commonly acknowledged, multiple humanoid species inhabit various solar systems within the Milky Way galaxy.

The file, `data/handedness.txt`, contains data on the handedness of some of these species, with $N$ representing the sample size and $x$ denoting the count of left-handed specimens. The objective is to estimate the prevalence of left-handedness, $\theta$.

Develop Stan programs for both unpooled and partially pooled binomial models. Utilize the unpooled model to fit the completely pooled model.

Incorporate Beta priors for $\theta$. Compare the estimates, such as means, derived from the distinct pooling strategies.



# 5. Model comparison




## 5.1 Prior predictive check 

In a salmon farm research facility, the relationship between the length of salmons (in meters, $y$) and the amount of food provided (in grams, $x$) is studied. The amount of food administered in 21 salmon pools is meticulously controlled, ranging from 40 to 60 grams per individual salmon in one-gram increments.

The chosen statistical model is linear regression: 

$$
y \sim N(a + bx, \sigma^2) \\
a, b \sim N(0, 1) \\
\sigma \sim \Gamma(2, 1) \\
$$

Generate the [prior predictive distribution](https://mc-stan.org/docs/2_26/stan-users-guide/prior-predictive-checks.html), plot it, and visually assess the validity of the priors.



## 5.2. Posterior predictive check 

The white noise process is perhaps the simplest non-trivial time series model. If $i$ is the time index, then the model can be specified as $x_i \sim N(0, \sigma^2)$ for all $i = 1, \ldots ,N$. 

The file `data/white_noise.txt` contains a time series. Plot the data. 

Build a Stan program for the white noise model to estimate $\sigma$ and use it on the provided data.

Perform a posterior predictive check by using lag-1 autocorrelation as the test statistic. Compute the posterior predictive p-value. 

Hint: lag-1 autocorrelation is simply the Pearson correlation between $x_{1:(N-1)}$ and $x_{2:N}$ 














# 6. Gaussian processes


## 6.1. GP prediction 

Consider the following data $(x, y)$:


``` r
x <- c(6.86, -7.88, 1.59, 5.95, -2.55, -1.96, 3.77, -6.74, -6.83, 8.42, -4.95, -6.29, 9.58, -1.95, 7.6, 1.97, 7.75, 8.34, 9.22, -6.31, 1.73, 9.58, 6.86, 1.46, -6.7, -9.9, 6.81, -6.38, 3.52, -4.36, -3.46, 7.7, -7.63, 3.51, 5.57, 3.2, 2.04, 6.33, -7.84, -2.85, -7.86, -5.14, -6.18, -9.35, -5.59, -6.86, 4.2, 8.83, 8.04 )

y <- c(2.95, -6.7, 0.8, -1.47, -0.03, -0.26, -1.03, -2.8, -3.01, 6.75, 1.68, -0.93, -0.88, -1.03, 6.88, -0.1, 7.32, 7.59, 3.09, -0.44, 0.69, -0.27, 3.45, 0.46, -2.49, 3.33, 3.28, -1.48, -0.11, 1.76, 0.25, 6.01, -6.53, 0.62, -1.01, 0.26, 1.31, 0.47, -6.7, -0.29, -6.09, 2.03, -0.22, -1.24, 1.46, -3.73, -0.87, 5.27, 6.59)
```


Model the data as $y \sim N(f(x), \sigma^2)$ and give $f(x)$ a Gaussian process prior. Use the squared exponential covariance function with hyperparameters $\lambda = 1$ (length-scale), $\alpha = 1$ (standard deviation), $\sigma = 0.5$. 

Plot the posterior for $f$ along with the data, and compute the posterior probability for $f(0) > 0$. 


## 6.2. GP prediction 

The file `data/nytemp.txt` contains daily maximum temperatures ($Temp$) in New York from May to September 1973 [1]. The column $x$ gives the day number for the date (Jan 1st is day number 1 etc). 

Do Gaussian process regression on the data and estimate the temperature trend for the year 1973. Use a periodic covariance kernel with $\alpha = 100$ and $\lambda = 10$, and set a suitable value for the period. Set $\sigma$ (deviance from the trend) as an unknown parameter in Stan. 

Plot the data with the posterior for temperature. Plot the marginal posterior for temperature for the last day of the year and compare it against the true value (which was 38F). 


Hint: 

See https://mc-stan.org/docs/functions-reference/gaussian-process-covariance-functions.html for info on periodic kernel in Stan. 

[1] (Chambers, J. M., Cleveland et al. (1983) Graphical Methods for Data Analysis)



## 6.3. GP prediction 

The data file `data/stock.txt` contains the (scaled and transformed) stock price of a company over 250 days.

Implement the following model and use it to predict the stock 150 days into the future. 

$$
y \sim N(f, \sigma^2) \\
f = f_{trend} + f_{noise} \\
f_{trend} \sim GP(0, K_1(\alpha_1, \lambda_1)) \\
f_{noise} \sim GP(0, K_2(\alpha_2, \lambda_2)) \\
\sigma \sim \Gamma(2, 10)
$$

Set $K_1$ to squared exponential covariance function and $K_2$ to exponential covariance. The point of $f_{trend}$ is to capture the longer term trend in the data, while $f_{noise}$ targets shorter-term variations around the trend. 

Set the hyperparameters $\alpha_{1}, \alpha_{2}$ and $\lambda_{1}, \lambda_{2}$ appropriately. 

Is such a model appropriate for predicting stock prices into the future?

Hint: Running the Stan program can take quite a long time, so do the initial testing using only 1 chain and, for example, 500 iterations. 











# 7. Other topics

## 7.1 Hierarchical models using `brms`

a) 

Use `brms` to fit the hierarchical Gamma-Poisson model fitted in exercise 4.2. After fitting the model plot the histogram of lambda and compare that the results are similar to the model fit in 4.2.

(Hint: You can check [this](https://discourse.mc-stan.org/t/how-to-set-prior-for-hyper-parameter/15306/5) forum post for how to implement prior for hyper-parameter when using `brm`. You can also get a warning when fitting the model, which you can ignore.)

b)

Fit the hierarchical Poisson regression model fitted in exercise 4.3 using `brms`. After fitting the models plot the city specific estimates for alpha and beta.

## 7.2 Zero-inflated poisson model 

Sometimes count data has more zeros than is expected when using, for example, Poisson or negative binomial model. This type of data can be called zero-inflated. An example of this type of data is `DoctorVisits` dataset from `AER` R package. The data describes the amount of doctor visits based on Australian Health Survey in 1977—1987.


``` r
# Install the package to get the data
install.packages(AER)
```

``` error
Error in eval(call, envir = parent.frame()): object 'AER' not found
```

``` r
# Get data
data("DoctorVisits", package = "AER")
```

``` error
Error in find.package(package, lib.loc, verbose = verbose): there is no package called 'AER'
```

a)

Use `brms` to fit an zero-inflated poisson model to the data while using gender, age, health and income as explanatory variables. Use weakly informative prior for the population-level coefficients. Comment the fit summary. Also plot the conditional effects.

b) 

Part of zero-inflated models is the zero-inflated probability. This probability can also be predicted when fitting the model. Fit the same model, but this time also predict zero-inflated probability using age and health. Again comment the fit summary and especially how age and health affect probability of zero visits. Compare the results to the first model.

c) 

To end fit a zero-inflated negative binomial model with the same explanatory variables as in the second model. Compare results to the two previous models. Use loo and comment which model is the best. Running the model can take a while. 

(Hint: Use `adapt_delta = 0.95`. Also if your computer has 4 or more cores you can use `cores = 4` to speed up the fit.)

