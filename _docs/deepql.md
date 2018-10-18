---
title: DeepQL - A Language for Querying a Deep neural Network
permalink: /docs/deepql/
---

This project was for a grad-level Databases class through the CSE department and was completed by myself and Ryan Stoddard.  The repository can be found [here](https://github.com/nealde/deepql)

The purpose of this project was to be able to intuitively as 'What If...' of a database. For example, when applying for an H1B Visa, what if my salary were doubled? What if I were to work in Texas, or if I were an Engineer instead of a Teacher?  The goal was to write an interface language that allowed for these questions to be asked intuitively.

### This project can be broken down into a few key steps:
1. Find a data set and store it in a SQL database
2. Train a neural network on it
3. Interface them
4. Do some example queries

#### 1. Find a data set

For this project, the dataset we selected was the H1B Visa dataset. It contained 528,000 Visa applications made between the years 2011 and 2017.

The data arrived in a table and so was extremely easy to put into SQL. However, in order to pass the information to the Neural Network, every multi-option string input had to be changed to a one-hot encoded input. The final shape of the data input to the neural network was roughly 136 columns.

#### 2. Train the neural network

For this work, a simple deep neural network was used, created with Keras.
```python
model = Sequential()
model.add(Dense(191, activation='selu', input_shape=(X_train.shape[1],)))
model.add(Dropout(0.4))
model.add(Dense(128, activation='selu'))
model.add(Dropout(0.4))
model.add(Dense(128, activation='selu'))
model.add(Dropout(0.4))
model.add(Dense(128, activation='selu'))
model.add(Dropout(0.4))
model.add(Dense(y_train.shape[1], activation='sigmoid'))
```

The Visa is sunject to one of 4 classification labels: Withdrawn, Denied, Certified-Withdrawn, and Certified.
Unfortunately, the distribution was extremely nonuniform:

![figure1]({{ site.baseurl }}/img/deepql/pie.png)

This was accounted for during training time by using `class weights`:

```python
class_weight = [ 0.028143268,  3.66670427, 27.03184685,  8.23151107]
model.fit(X_train, y_train,
          epochs = epochs,
          batch_size = batch_size,
          class_weight = class_weight,
#              callbacks=callbacks_list,
          verbose = 1,
         validation_data = (X_test, y_test))
```

This allows the training algorithm to assign equal importances to all of the scores, otherwise the neural network may scale itself such that every output is `Certified`, since that was the dominant class.

Additionally, the loss function had to be changed to Mean Squared Error, since Categorical Cross Entropy was extremely susceptible to the 'always-guess-certified' problem, even with class weights.  This is likely due to the way the math works - the loss is extremely low when the network is able to get very close to 1 for the correct label, so the average error of guessing `[1,0,0,0]` every time is better than attempting to be correct.

Although Mean Squared Error does not inherently enforce that the probabilities sum to 1, it still performed well in this classification problem.  This could be enforced using a custom loss function, however.

The Confusion Matrix is shown below:

|                     | Certified | Certified-Withdrawn | Denied | Withdrawn |
|---------------------|-----------|---------------------|--------|-----------|
| Certified           | 93462     | 29                  | 47     | 113       |
| Certified-Withdrawn | 289       | 7074                | 0      | 0         |
| Denied              | 919       | 3                   | 155    | 356       |
| Withdrawn           | 652       | 388                 | 65     | 2075      |

The network performs fairly well, getting roughly 97.3% of the test answers correct.

#### 3. Write the interface

The interface is given in a flow chart below:

![flowchart]({{ site.baseurl }}/img/deepql/flowchart.png)

Essentially, a SQL-like query is given, which is then split into an SQL command and a DeepQL command. The function then returns the output of the neural network, which serves to approximate the cartesian product of every permutation of the columns.

The syntax actually embraces the use of this "What If" structure and breaks the input query up like so:

query("<span style="color:blue">select * from BASE where wage > 60000 </span> <span style="color:green">WHAT IF </span> <span style="color:red"> PREVAILING_WAGE = 0.5 TIMES DIFFERENCE </span")

The **<span style="color:blue"> SQL Query </span>** deals with selecting the initial set of data, and is passed directly to the SQL database.
The **<span style="color:green"> WHAT IF </span>** token separates the SQL query from the DeepQL query.
The **<span style="color:reg"> DeepQL Query </span>** tells the engine what data to change and how to change it - in this instance, wage is halved.  **<span style="color:reg"> DIFFERENCE </span>** asks the engine to return only **outcomes which have changed**

#### 4. Example Queries

`query("select * from BASE where jobName ='EDUCATION' WHAT IF SOC_NAME = SCIENTIST DIFFERENCE")`
 returns  
`[init size = 12370, 980 results]`  
meaning that of this initial 12,370 results, 980 are different now that the job name has changed.  Just how they were changed is reported in the table that is returned.

`query("select * from BASE where jobName = 'EDUCATION' WHAT IF SOC_NAME = SCIENTIST AND H-1B_DEPENDENT = N DIFFERENCE")` returns  
`[init size = 12370, 3927 results]`

Overall, this was a really fun project that required coming up with some fairly creative ways of turning words into new inputs to the network.
