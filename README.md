# Distance between Random Events

This package is for symbolically and numerically calculating the arbitrary order moments, pdf, cdf and their conditional counterparts of the distance between two random events in a given graph. The position of an event in a network is encoded in a tuple `(e, p)`, where `e={u,v}` (assume `u < v`) is the edge where the event happens and `p` is the relative location of the event on that edge, that is the length of the segment from `u` (the vertex with small index) to the location of the event divided by the length of the edge `e`. Since both events are random, we use `(X, P)` and `(Y, Q)` to denote both events respectively.


## Installation

Use pip to install the randist package
'''
pip install randist
'''

## Inputs
1. A data file whose rows are edges of the network with extra properties. There are five columns,
   * `i`: first vertex of the edge
   * `j`: second vertex of the edge
   * `l`: length of the current edge
   * `x`: the probability of event 1 happens on the current edge
   * `y`: the probability of event 2 happens on the current edge

2. The joint distribution of the relative locations of two events, ![alt text](https://latex.codecogs.com/gif.latex?\Phi_\scriptscriptstyle{P,Q}(p,q)). This should be provided as a `Phi` object,
```
from sympy.abc import p, q  # import symbols
import randist as rt        # import our randist package

phi_pq = 1  # the uniform distribution
phi_pq = 36 * p * (1 - p) * q * (1 - q)  # both are beta functions with parameters alpha = beta = 2

phi = rt.Phi('betapq', phi_pq=phi_pq)  # create a Phi object with a name

```
In current implementation, the random variables `X` and `Y` are assumed to be independent, but the formulas we developed do not have this restriction. Also, currently, we assume the joint pdf ![alt text](https://latex.codecogs.com/gif.latex?\Phi_\scriptscriptstyle{P,Q}(p,q)) are same for all pair of edges `(e, f)`, but the formulas in our paper do not have this restriction. We may relax both restrictions in the future version.

## Outputs
Several statistics about the random distance `D` between two events:
1. moments of arbitrary order 
2. cdf (point evaluation, or plotting against the distance `x`) 
3. pdf (point evaluation, or plotting against the distance `x`) 
4. conditional moments of arbitrary order  (point evaluation, or plotting against the relative location `p` given `X=e`) 
5. conditional cdf  (point evaluation or plotting against `x` given `(X, P) = (e, p)` ) 
6. conditional pdf  (point evaluation or plotting against `x` given `(X, P) = (e, p)` )

All these statistics can be computed either symbolically or numerically. We will explain their differences later.

## Main Interfaces
User can achieve most tasks with two interfaces, `Formulas` and `data_collector`. The former gives the freedom of calculating statistics individually, where the latter can collect data in batch.

### Formulas Object
There is an example of using formulas objects to compute:
```
from sympy.abc import p, q  # import symbols
import randist as rt        # import our package

gname = 'g0'  # data file name in the folder ./data
phi_pq = 36 * p * (1-p) * q * (1 - q)
phi = rt.Phi('betapq', phi_pq=phi_pq)  # create a joint pdf with a name

fls = rt.Formulas(gname, phi)                 # create a formulas object
moment = fls.get_formula(rt.Stats.MOMENT)     # get a moment formula object
cdf = fls.get_formula(rt.Stats.CDF)           # get a cdf formula object
pdf = fls.get_formula(rt.Stats.PDF)           # get a cdf formula object
cmoment = fls.get_formula(rt.Stats.MOMENT)    # get a moment formula object
ccdf = fls.get_formula(rt.Stats.CCDF)         # get a cdf formula object
cpdf = fls.get_formula(rt.Stats.CPDF)         # get a cdf formula object

moment.eval(3)                        # computing the 3rd order moment
moment.eval(2) - moment.eval(1) ** 2  # compute the variance
cdf.eval(9.5)                         # evaluate the cdf at the point x = 9.5
cdf.plot(show=True)                   # save the plot in the ./results folder and show it
pdf.eval(8.1)                         # evaluate the pdf at the point x = 9.5
pdf.plot()                            # save the plot in the ./results folder without showing
cmoment.eval(1, ('1', '2'), 0.5)      # the conditional expectation given (e, p) = (('1', '2'), 0.5)
cmoment.plot(1, ('1', '2'))           # plot the conditional expectation against the value of p
ccdf.eval(('2', '3'), 0.1, 3.5)       # evaluate the conditional cdf at x = 3.5 given (e, p) = (('2', '3'), 0.1)
ccdf.plot(('2', '3'), 0.1)            # plot the conditional cdf given (e, p) = (('2', '3'), 0.1)
cpdf.eval(('2', '3'), 0.1, 3.5)       # same but with conditional pdf
cpdf.plot(('2', '3'), 0.1)            # same but with conditional pdf
```



