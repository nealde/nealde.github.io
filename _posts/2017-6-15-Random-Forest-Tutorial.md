---
layout: post
title: Random Forest Regression Tutorial
---

```python
%matplotlib inline
```


# Comparing random forests and the multi-output meta estimator


An example to compare multi-output regression with random forest and
the :ref:`multioutput.MultiOutputRegressor <multiclass>` meta-estimator.

This example illustrates the use of the
:ref:`multioutput.MultiOutputRegressor <multiclass>` meta-estimator
to perform multi-output regression. A random forest regressor is used,
which supports multi-output regression natively, so the results can be
compared.

The random forest regressor will only ever predict values within the
range of observations or closer to zero for each of the targets. As a
result the predictions are biased towards the centre of the circle.

Using a single underlying feature the model learns both the
x and y coordinate as output.



# Full code:


```python
# print(__doc__)

# Author: Tim Head <betatim@gmail.com>
#
# License: BSD 3 clause

import numpy as np
import matplotlib.pyplot as plt
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split
from sklearn.multioutput import MultiOutputRegressor


# Create a random dataset
rng = np.random.RandomState(1)
X = np.sort(200 * rng.rand(600, 1) - 100, axis=0)
y = np.array([np.pi * np.sin(X).ravel(), np.pi * np.cos(X).ravel()]).T
y += (0.5 - rng.rand(*y.shape))

X_train, X_test, y_train, y_test = train_test_split(X, y,
                                                    train_size=400,
                                                    random_state=4)

max_depth = 30
regr_multirf = MultiOutputRegressor(RandomForestRegressor(max_depth=max_depth,
                                                          random_state=0))
regr_multirf.fit(X_train, y_train)

regr_rf = RandomForestRegressor(max_depth=max_depth, random_state=2)
regr_rf.fit(X_train, y_train)

# Predict on new data
y_multirf = regr_multirf.predict(X_test)
y_rf = regr_rf.predict(X_test)

# Plot the results
plt.figure()
s = 50
a = 0.4
plt.scatter(y_test[:, 0], y_test[:, 1],
            c="navy", s=s, marker="s", alpha=a, label="Data")
plt.scatter(y_multirf[:, 0], y_multirf[:, 1],
            c="cornflowerblue", s=s, alpha=a,
            label="Multi RF score=%.2f" % regr_multirf.score(X_test, y_test))
plt.scatter(y_rf[:, 0], y_rf[:, 1],
            c="c", s=s, marker="^", alpha=a,
            label="RF score=%.2f" % regr_rf.score(X_test, y_test))
plt.xlim([-6, 6])
plt.ylim([-6, 6])
plt.xlabel("target 1")
plt.ylabel("target 2")
plt.title("Comparing random forests and the multi-output meta estimator")
plt.legend()
plt.show()
```

    Automatically created module for IPython interactive environment
    


![png]({{ site.baseurl }}/images/tut1/output_3_1.png)


# Code Breakdown:

Importing the packages is fairly straightforward, and if you wanted to use any regressor other than Random Forest or Multi Output, we'd only need to look up the library to import it from and the arguments that the function takes and you can just drop it into place.  We will do this further on in the tutorial.

# Import the packages


```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split
from sklearn.multioutput import MultiOutputRegressor
```

# Create the randomized data set

There are a few key factors here, which may be overlooked if we're not careful.  
* Some regressors are limited in the size of Y values (targets) that they can take in.  For example, a Gradient Boosted Machine Regressor (in my experience) can only tolerate estimating a single value, whereas a Random Forest or Decision Tree can handle any size vector for Y. 
* Randomization of the test and training set is important, as is the ratio between them.  If your data is greated, say, using a grid search, and you take the first 3/4 as the training set and the last 1/4 as the test set, the results will be poor because of the inherent differences between the data sets.  Luckily, SKLearn has a function that does this for us called train_test_split.  If we don't like black boxes, there's another way to do this in the next panel.


```python
# Creating the data set
rng = np.random.RandomState(1)
X = np.sort(200 * rng.rand(600, 1) - 100, axis=0)
y = np.array([np.pi * np.sin(X).ravel(), np.pi * np.cos(X).ravel()]).T
y += (0.5 - rng.rand(*y.shape))

X_train, X_test, y_train, y_test = train_test_split(X, y,
                                                    train_size=400,
                                                    random_state=4)
```

Randomizing the data in our own way is fairly simple as well:

First, sort a random sample in range(0,length of X) whose total number is 3/4th of the length of X.  This results in a list of values.
Numpy has this awesome functionality where you can give an array a list of integers and it will pull out just those values. For example:


```python
import random
xx = np.array([10,11,12,13,14,15,16,17,18,19,20])
xx_indcs = sorted(random.sample(range(xx.shape[0]), int(xx.shape[0]*(3.0/4))))
print xx_indcs
print xx[xx_indcs]
```

    [1, 3, 4, 6, 7, 8, 9, 10]
    [11 13 14 16 17 18 19 20]
    

Applying this to our data...


```python
import random
train = sorted(random.sample(range(X.shape[0]), int(X.shape[0]*(3.0/4.))))
test = [x for x in range(X.shape[0]) if x not in train]
print(len(train), len(test), X.shape[0])
print(test[0:10])
```

    (450, 150, 600L)
    [5, 7, 19, 21, 28, 35, 38, 45, 47, 54]
    

Perfect, our ratio is 3:1 and the values appear random!

One thing to note is that this method, as implemented, does not have a static random state, so there may be slight differences in the training set content from run to run.

Now, we need to assign our training and test values using these list sof indices just like in the earlier example.


```python
X_train = X[train,:]
y_train = y[train,:]

X_test = X[test,:]
y_test = y[test,:]
print(X_train.shape, X_test.shape)
```

    ((450L, 1L), (150L, 1L))
    

# Instantiate and train the models

This is the power of SK-Learn.  It makes creating a model and fitting it extremely simple - 2 lines, in fact.  Simply look up the list of arguments that particular model takes in the documentation and modify these arguments (called hyperparameters) as necessary.

Note that X_train and y_train do not need to be the same width (i.e. you can go from 5 "features" -> 1 target value, or from 3 features -> 15 target values) but they do need to have the same length (450 total samples for both).

In order to fit the model, call ` model_name.fit(X_train, y_train)`

To get a score for the model, call ` model_name.score(X_test, Y_test)`

Knowing the training score is important for understanding how well the model understands the form of the data, but the really important bit is the test score - the score on data that the model hasn't seen.



```python
max_depth = 30 # set the maximum depth of the trees.  Helpful to avoid overfitting, but not always necessary

# I find it useful to time and print the scores for these functions:
import time
print("Multi-Output RF Regressor")
st = time.time()
regr_multirf = MultiOutputRegressor(RandomForestRegressor(max_depth=max_depth,
                                                          random_state=0))
regr_multirf.fit(X_train, y_train) # fit the model which accepts X as an input and spits out Y
print("Training time: %f seconds" % (time.time()-st))
print("Training score: R^2 = %f, Test score: R^2 = %f" % (regr_multirf.score(X_train,y_train),
                                                          regr_multirf.score(X_test,y_test)))

print(" ")
print("Random Forest Regressor:")
st = time.time()
regr_rf = RandomForestRegressor(max_depth=max_depth, random_state=2)
regr_rf.fit(X_train, y_train)
print("Training time: %f seconds" % (time.time()-st))
print("Training score: R^2 = %f, Test score: R^2 = %f" % (regr_rf.score(X_train,y_train),
                                                          regr_rf.score(X_test,y_test)))

```

    Multi-Output RF Regressor
    Training time: 0.125000 seconds
    Training score: R^2 = 0.977798, Test score: R^2 = 0.893520
     
    Random Forest Regressor:
    Training time: 0.034000 seconds
    Training score: R^2 = 0.978257, Test score: R^2 = 0.899668
    

# Now that the models are trained, what can we do with them?

Well, we can call model_name.predict with any number of data points, as long as the input vectors are the same length.  For instance, in this case, the input X is a vector of length 1, so any length 1 array will be acceptable.  I say array to highlight this important difference:

float: 5.235  
Vector: [5.235]  
Array: [[5.235]]

SK-Learn will throw an error unless we ask it to predict an array, no matter how few points we have. 

However, calling ` X_test[0]` will give a vector, which can be turned into an array by calling:  ` X_test[0].reshape(1,-1)`, where `(1` represents the desired array number of rows, and `-1)` represents that we will let numpy figure out how long the vector should be.


```python
print(X_test[0], X_test[0].reshape(1,-1))
```

    (array([-98.13028441]), array([[-98.13028441]]))
    

When calling multiple data points, no reshape is needed because the data is already formatted as an array:


```python
y_multirf = regr_multirf.predict(X_test)
y_rf = regr_rf.predict(X_test)
print(X_test.shape, y_multirf.shape, y_train.shape)
```

    ((150L, 1L), (150L, 2L), (450L, 2L))
    

As we can see, our input shape was a vector of length 1, but the output is a vector of length 2, which matches the shape of our Y data. Perfect!

# Now we can plot our data


```python
# Plot the results
plt.figure(figsize=(10,10))
s = 50
a = 0.4
plt.scatter(y_test[:, 0], y_test[:, 1],
            c="navy", s=s, marker="s", alpha=a, label="Data")
plt.scatter(y_multirf[:, 0], y_multirf[:, 1],
            c="cornflowerblue", s=s, alpha=a,
            label="Multi RF score=%.2f" % regr_multirf.score(X_test, y_test))
plt.scatter(y_rf[:, 0], y_rf[:, 1],
            c="c", s=s, marker="^", alpha=a,
            label="RF score=%.2f" % regr_rf.score(X_test, y_test))
plt.xlim([-6, 6])
plt.ylim([-6, 6])
plt.xlabel("target 1")
plt.ylabel("target 2")
plt.title("Comparing random forests and the multi-output meta estimator")
plt.legend()
plt.show()
```


![png]({{ site.baseurl }}/images/tut1/output_21_0.png)


Wait, what happened between what we did and the original plot? The scores there were lower! Have we done anything better?

Well, their train size was set at 400, and ours was 450, so the nature of allowing the model to train on more data both decreases the variance of the test set as well as increases the likelihood that data in our test set are similar to data in our training set, which results in a higher score.

# What about the other regressors you promised???

Don't worry, here they are.  Because our Y data is a pair of points, we may be limited in which SK-Learn regressors we can use.  Among ones we can are:
* LinearRegressor
* ExtraTreesRegressor
* RandomForestRegressor

However, we don't want to just have more one-off examples, we want to understand how to acquire these ourselves!  So, this is the process:
* Learn the name of the regressor you want
* Find the SK-Learn documentation
* Read the SK-Learn documentation
* Import the model
* Leave the default hyperparameters, they're usually pretty good.
* Call model.fit(X_train, y_train)

Here is an entire walkthrough of the process.  We are interested in the Linear Regressor, so we google it and we find the [documentation](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html).  
We look at it and we see this:

*class* `sklearn.linear_model` **LinearRegression**(*fit_intercept=True, normalize=False, copy_X=True, n_jobs=1*)

What do these agruments mean? We look a bit lower and see that they are fairly well documented:

**Parameters:**	
*fit_intercept :* boolean, optional
whether to calculate the intercept for this model. If set to false, no intercept will be used in calculations (e.g. data is expected to be already centered).

*normalize :* boolean, optional, default False
If True, the regressors X will be normalized before regression. This parameter is ignored when fit_intercept is set to False. When the regressors are normalized, note that this makes the hyperparameters learnt more robust and almost independent of the number of samples. The same property is not valid for standardized data. However, if you wish to standardize, please use preprocessing.StandardScaler before calling fit on an estimator with normalize=False.

*copy_X :* boolean, optional, default True
If True, X will be copied; else, it may be overwritten.

*n_jobs :* int, optional, default 1
The number of jobs to use for the computation. If -1 all CPUs are used. This will only provide speedup for n_targets > 1 and sufficient large problems.

Linear regressors train pretty fast, so n_jobs can be left at 1, but if we are looking to train something large, like a large Random Forest, it would be advisable to change that to -1, so it will be done below:


```python
from sklearn.linear_model import LinearRegression

import time
st=time.time()
print("Linear Regressor")
lin = LinearRegression(n_jobs=-1)
lin.fit(X_train,y_train)
print("Training time: %f seconds" % (time.time()-st))
print("Training score: R^2 = %f, Test score: R^2 = %f" % (lin.score(X_train,y_train),
                                                          lin.score(X_test,y_test)))
```

    Linear Regressor
    Training time: 0.001000 seconds
    Training score: R^2 = 0.000183, Test score: R^2 = -0.000056
    

As expected, a linear fit is not a good solution to a sine-cosine based data set.  Let's try something a bit more flexible: a special variant of a Random Forest called an **Extra Random Forest**.

We find the [documentation](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.ExtraTreesRegressor.html) and we see that this one takes waaaaaay more arguments, which can be intimidating because they all sound like tree attributes - leaves and branches? What do those mean? That's out of the scope of this tutorial, but you don't need to worry about those - they are responsible for the last 3% of performance, not the 97%.

The four parameters we care about are: *n_estimators*, *max*depth*, *random_state*, and *n_jobs*.  Large random forests can take a while to train, although this data set is very small so we may not notice.  However, we will examine the importance of this hyperparameter by increasing it and storing the scores as a function of *n_estimators*:


```python
from sklearn.ensemble import ExtraTreesRegressor

import time
st=time.time()
etr = ExtraTreesRegressor(n_estimators = 10, n_jobs = -1, random_state=12)
etr.fit(X_train,y_train)
print("Training time: %f seconds" % (time.time()-st))
print("Training score: R^2 = %f, Test score: R^2 = %f" % (etr.score(X_train,y_train),
                                                          etr.score(X_test,y_test)))
```

    Training time: 0.253000 seconds
    Training score: R^2 = 1.000000, Test score: R^2 = 0.926087
    

### A PERFECT TRAINING SCORE?!? WHAT GIVES??
Typically, unless Random Forests are given a *max_depth*, they will perfectly "memorize" **(read: OVERFIT)** the training data.  by setting a max_depth, we can potentially improve the test score while simultaneously reducing the training score:

*Note:* it is important to have the *random_state* constant in order to ensure that the random forest would have otherwise been identical to the previous one if not for the changing hyperparameters.


```python
list_training_scores=[]
list_test_scores=[]
max_d = range(10,25)

for i in range(10,25):
    etr = ExtraTreesRegressor(n_estimators = 10, n_jobs = -1, max_depth=i, random_state=12)
    etr.fit(X_train,y_train)
    list_training_scores.append(etr.score(X_train,y_train))
    list_test_scores.append(etr.score(X_test,y_test))
fig = plt.figure(figsize=(10,10))
ax1=fig.add_subplot(1,1,1)
ax1.plot(max_d,list_training_scores, label='Training Score')
ax1.set_ylabel("R^2 Coefficient", fontsize=16)
ax1.set_xlabel("max_depth", fontsize=16)
ax1.set_xlabel("")
ax1.plot(max_d,list_test_scores, label="Test Score")
ax1.legend(fontsize=16)
```




    <matplotlib.legend.Legend at 0xd90b6a0>




![png]({{ site.baseurl }}/images/tut1/output_28_1.png)


We can see that there is a maximum for the test score, and that it is not at the highest maximum depth.  But, we can see that the change from the default is very small, because the risk of overfitting for this particular data set is low.

# On to the most important hyperparameter: *n_estimators*


```python
list_training_scores=[]
list_test_scores=[]
list_train_time=[]
max_d = range(1,100,2)
import time

for i in range(1,100,2):
    st=time.time()
    etr = ExtraTreesRegressor(n_estimators = i, n_jobs = -1, max_depth=15, random_state=12)
    etr.fit(X_train,y_train)
    list_training_scores.append(etr.score(X_train,y_train))
    list_test_scores.append(etr.score(X_test,y_test))
    list_train_time.append(time.time()-st)
fig = plt.figure(figsize=(20,10))
ax1=fig.add_subplot(1,2,1)
ax2=fig.add_subplot(1,2,2)
ax1.plot(max_d,list_training_scores, label='Training Score')
ax1.set_ylabel("R^2 Score", fontsize=16)
ax1.set_xlabel("n_estimators", fontsize=16)
ax1.plot(max_d,list_test_scores, label="Test Score")
ax2.plot(max_d, list_train_time)
ax2.set_ylabel("Training time (s)", fontsize=16)
ax2.set_xlabel("n_estimators", fontsize=16)
ax1.legend(fontsize=16)
ax2.legend(fontsize=16)
```


![png]({{ site.baseurl }}/images/tut1/output_30_0.png)


# Trying a Gradient Boosted Machine

We look at the [documentation](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingRegressor.html) and we see how to implement it:


```python
from sklearn.ensemble import GradientBoostingRegressor

import time
st=time.time()
gbr = GradientBoostingRegressor()
gbr.fit(X_train,y_train)
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-72-fe8df1e7f64d> in <module>()
          4 st=time.time()
          5 gbr = GradientBoostingRegressor()
    ----> 6 gbr.fit(X_train,y_train)
    

    C:\Users\Neal\Anaconda2\lib\site-packages\sklearn\ensemble\gradient_boosting.pyc in fit(self, X, y, sample_weight, monitor)
        971 
        972         # Check input
    --> 973         X, y = check_X_y(X, y, accept_sparse=['csr', 'csc', 'coo'], dtype=DTYPE)
        974         n_samples, self.n_features = X.shape
        975         if sample_weight is None:
    

    C:\Users\Neal\Anaconda2\lib\site-packages\sklearn\utils\validation.pyc in check_X_y(X, y, accept_sparse, dtype, order, copy, force_all_finite, ensure_2d, allow_nd, multi_output, ensure_min_samples, ensure_min_features, y_numeric, warn_on_dtype, estimator)
        524                         dtype=None)
        525     else:
    --> 526         y = column_or_1d(y, warn=True)
        527         _assert_all_finite(y)
        528     if y_numeric and y.dtype.kind == 'O':
    

    C:\Users\Neal\Anaconda2\lib\site-packages\sklearn\utils\validation.pyc in column_or_1d(y, warn)
        560         return np.ravel(y)
        561 
    --> 562     raise ValueError("bad input shape {0}".format(shape))
        563 
        564 
    

    ValueError: bad input shape (450L, 2L)


# Bad input shape? But this worked with the other ones!

For reasons I don't understand, this Gradient Boosted Machines can only be used with a single length vector in the target data set.  Is there a way to work around this?  What if we train a single Gradient Boosted Machine for each target value?


```python
# Separate the training and test data into arrays of a single target value
y_train_0 = y_train[:,0]
y_train_1 = y_train[:,1]

y_test_0 = y_test[:,0]
y_test_1 = y_test[:,1]

# Import the model
from sklearn.ensemble import GradientBoostingRegressor

import time
st=time.time()

# Train the individual models with the default hyperparameters
gbr0 = GradientBoostingRegressor()
gbr0.fit(X_train,y_train_0)

gbr1 = GradientBoostingRegressor()
gbr1.fit(X_train,y_train_1)

y_gbr0 = gbr0.predict(X_test)
y_gbr1 = gbr1.predict(X_test)

# Combine the predicted values so that we get an array of (150L, 2L)
y_gbr = np.concatenate((y_gbr0.reshape(-1,1),y_gbr1.reshape(-1,1)),axis=1)

# Plot compared to the other examples
plt.figure(figsize=(10,10))
s = 50
a = 0.4
plt.scatter(y_test[:, 0], y_test[:, 1],
            c="navy", s=s, marker="s", alpha=a, label="Data")
plt.scatter(y_multirf[:, 0], y_multirf[:, 1],
            c="cornflowerblue", s=s, alpha=a,
            label="Multi RF score=%.2f" % regr_multirf.score(X_test, y_test))
plt.scatter(y_rf[:, 0], y_rf[:, 1],
            c="c", s=s, marker="^", alpha=a,
            label="RF score=%.2f" % regr_rf.score(X_test, y_test))
plt.scatter(y_gbr[:, 0], y_gbr[:, 1],
            c="r", s=s, marker="^", alpha=a,
            label="GBR score=%.2f" % gbr0.score(X_test, y_test_0))
plt.xlim([-6, 6])
plt.ylim([-6, 6])
plt.xlabel("target 1")
plt.ylabel("target 2")
plt.title("Comparing random forests and the multi-output meta estimator")
plt.legend()
plt.show()

```


![png]({{ site.baseurl }}/images/tut1/output_34_0.png)


# Summary

Today, we walked through an [example](http://scikit-learn.org/stable/auto_examples/ensemble/plot_random_forest_regression_multioutput.html#sphx-glr-auto-examples-ensemble-plot-random-forest-regression-multioutput-py) from SK-Learn and broke it down in order to understand how to apply it to other data sets.  We learned a little bit about hyperparmeters for Random Forests, how to apply similar regressors to the same problem, and some (but definitely not all) of the pitfalls to avoid when looking at new regressors, because some can only handle y-vectors of length one.  We demonstrated a work-around for this shortcoming, but without hyperparameter tuning, the results were sub-par.


```python

```
