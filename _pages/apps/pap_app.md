---
layout: splash
title: Designing optimal pre-analysis plans
permalink: /pap_app/
---
 



<iframe src="https://maxkasy.shinyapps.io/MetaStudiesApp/" style="border:none;width:1000px;height:800px;">
The app is loading. This should not take more than a minute.
</iframe>




# How to use the PAP App

Consider the following scenario:
You are an empirical social scientist who is going to run a field experiment, in order to test some hypothesis.
You want to **do the right thing**. This means that you will not p-hack your results, and you will pre-register your statistical analysis. This way, you will control size of your test under the null.
At the same time you *would* like to **maximize the probability of rejecting your null hypothesis**. You believe there is an effect there, and you want to prove it to the world. What should you do? This tutorial will show how to use the PAP App to answer this question.
For further details, please refer to our working paper [Optimal Pre-Analysis Plans: Statistical Decisions Subject to Implementability](https://maxkasy.github.io/home/files/papers/optimal_preanalysis_plans.pdf).

The easiest way to use the app is to use the interactive app on this website.
In the following, we will however instead use the Python script `PAP_Optimal.py` to obtain the same results.
This script can be downloaded from [https://github.com/maxkasy/The_PAP_App](https://github.com/maxkasy/The_PAP_App). 
The following loads the functions from the Python script, as well as the required packages (`numpy`, `pandas`, `scipy`, `itertools`, and `cvxpy`):


```python
run PAP_Optimal
```

Next we need to specify the parameters of our problem, which will then determine the optimal PAP. These parameters pin down (1) the null hypothesis to be tested, (2) prior beliefs over the true parameter, and (3) prior beliefs over the availability of specific tests or estimates for the data that you will obtain.

In the app, we have implemented two leading scenarios.
The first scenario involves "data" in the form of binary decisions $X_1,X_2,\ldots$. 
Most commonly, these will correspond to the outcomes of component hypothesis tests. Our goal is then to implement a corresponding test of the joint null.
The second scenario involves "data" in the form of normal point estimates (or t-statistics) $X_1,X_2,\ldots$.
Our goal is again to implement a joint test based on these point estimates.

## Binary data 

Suppose you plan to run an experiment at three different sites, for instance in three different villages. 
In the experiment you have a binary treatment, and a main outcome of interest.
You will calculate a test for each village separately, reporting whether the treatment had a significant positive effect in this village. This gives three testing decisions $X_1$, $X_2$, and $X_3$.

However, there is some chance that the experiment will not be implementable in a given village. That is, there is a chance that you will not be able to calculate $X_i$ for all of the $i$.
Your prior probabilities for the experiments to be successfully implemented, for each of the villages, equal .9, .7, and .5, respectively. We collect these probabilities in the vector $\eta_J$ (`etaJ` in the code below).

Under the null of no effect, the probability of rejecting the null in each of the villages is given by $\underline \theta$ (`minp` below), where $\underline \theta = .05$.

Lastly, we need to make assumptions about your prior distribution for the rejection probability $\theta$ in each village. This will not turn out to matter in this example, but we shall assume a uniform prior for $\theta$, which is the same thing as a Beta-prior with parameters $\alpha = \beta = 1$.
These parameter specifications are collected in the following Python dictonary:


```python
input_binary = {
    'etaJ': np.array([.9, .7, .5]),
    'minp': .05,
    'alpha': 1,
    'beta': 1
}
```

It turns out these parameters are all that we need to calculate the optimal PAP. The function `pap_binary_data` takes the dictonary `input_binary`, and uses it to calculate the relevant distributions of data availability and test realizations under the null, as well as averaged over the prior distributions:


```python
test_args_binary = pap_binary_data(input_binary)
```

We can now calculate the optimal test, based on these distributions. This optimal test solves the linear programming problem described in our paper, maximizing expected power subject to size control and incentive compatibility. The Argument `size` refers to the desired size of the joint hypothesis test.


```python
solution_binary = optimal_test(test_args_binary, size = .05)
```

Let us look at the optimal pre-analysis plan that we have calculated:


```python
t_bin = solution_binary["t"]
t_bin[t_bin["t"]>0].sort_values(by = ["t", "X1", "X2"])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>X1</th>
      <th>X2</th>
      <th>X3</th>
      <th>t</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.947</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.000</td>
    </tr>
  </tbody>
</table>
</div>



To save this pre-analysis plan in a spreadsheet, run the following:


```python
t_bin.to_csv("optimal_pap_binary.csv", index=False)
```

### How to interpret this PAP

We now have a spreadsheet, as displayed in the table above, of rejection probabilities for different combinations of the $X$s. How do we use this spreadsheet?
The key point to remember is that we need to make worst-case assumptions about any components which are not available in the data that we actually obtain, after pre-registering our PAP.
For example, suppose you registered the above testing rule in a PAP, before seeing the data. 
Suppose further that, based on your data, you can calculate the test $X_1$, and the test $X_3$, but $X_2$ is not available for some reason. In this case we need to act as if $X_2$ equals 0.

Suppose additionally that $X_1 = 1$ but $X_3 = 0$. Can we reject the null?
Looking in the above table, we see that the row corresponding to $(1,0,0)$ has $t=1$, so we can indeed reject.
Suppose alternatively that $X_1 = 0$ but $X_3 = 1$. Can we reject the null in this case?
Looking in the above table, we see that the row corresponding to $(0,0,1)$ has $t=0$, so we cannot reject.

## Normal data 

The discussion above was based on binary data, motivated by the case where we calculate a joint testing decision based on multiple individual hypothesis tests.
An alternative, which uses the data more efficiently, is to calculate a joint testing decision based on multiple individual point estimates.
Motivated by standard asymptotic arguments, we assume that these point estimates are normally distributed.

As before, `etaJ` refers to the probabilities that each of the components will be available.
The parameters $\mu_0$ and $\Sigma_0$ (`mu0` and `Sigma0`) specify the mean vector and covariance matrix of $X$ under the null.
The parameters $\mu$ and $\Sigma$ (`mu` and `Sigma`) specify the prior marginal distribution of $X$. In the present setting, this prior distribution is important for calculating the test with highest expected power.


```python
input_normal = {
    'etaJ': np.array([.9, .5]),
    'mu0': np.array([0, 0]),
    'Sigma0': np.array([[1, 0], [0, 1]]),
    'mu': np.array([1, 1]),
    'Sigma': np.array([[2, 1], [1, 2]])
}
```

The remainder of the calculation proceeds as before.
To apply the proposed linear programming approach, we need to discretize the support of the vector $X$. This is done automatically by the function `pap_normal_data`.


```python
test_args_normal = pap_normal_data(input_normal)

solution_normal = optimal_test(test_args_normal, size = .05)
```


```python
t_norm = solution_normal["t"]
# t_norm[t_norm["t"]>0].sort_values(by = ["t", "X1", "X2"])
t_norm.to_csv("optimal_pap_normal.csv", index=False)
```
