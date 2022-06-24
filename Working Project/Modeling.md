## Modeling

I started with creating a list of numerical variables, then filled missing values with a zero. From there, I split the data into training and testing sets. I made it an .80 training size, obviously with the result variable being All-Star.

```python
# Split the data into training and testing

X_train, X_test, y_train, y_test = train_test_split(results[xcols], 
                                                    results['All-Star'], 
                                                    train_size = 0.8, 
                                                    random_state = 1)
print('training data:', X_train.shape)
print('test data:', X_test.shape)
```

I fit a logistic regression model with 3000 iterations to assure the validity of this model. I came out with about a 98% accuracy score. I listed the top and worst coefficients to help identify the most influential in prediciting All-Stars. I added the top five and the bottom five coefficients to a new list. I will use these in the next test/train set.

```python
log_reg = LogisticRegression(solver = 'lbfgs', max_iter = 3000)

# Fit the model to the training data
clf = log_reg.fit(X_train, y_train)

print('training accuracy: {}'.format(clf.score(X_train, y_train).round(3)))
print('test accuracy: {}'.format(clf.score(X_test, y_test).round(3)))
```

Next, was somee regularization and cross validating. The next little bit of code includes recall, precision, a confusion matrix, etc. This helps identify a little more accuracy and such. 

```python
# Confusion matrix

cm = confusion_matrix(y_test, y_pred_test)
sample = np.array([['TN', 'FP'], ['FN', 'TP']])
print('CM key:\n', sample, '\n')
print('CM for test:\n', cm)
```
