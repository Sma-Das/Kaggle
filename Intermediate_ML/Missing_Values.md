# Missing Values Code Summary

Number of missing values per column

```python
missing_values_columns = X_train[X_train.isna().sum() > 0] # pd.Series
```

Numer of columns with missing values

```python
X_train[X_train.isna().sum() > 0].shape[0]
```

Total number of missing entries

```python
X_train[X_train.isna().sum() > 0].sum()
```

### Drop columns with missing values

```python
empty_cols = [col for col, empty in X_train.isna().any().items() if empty]

reduced_X_train = X_train.copy().drop(empty_cols, axis=1)
reduced_X_valid = X_valid.copy().drop(empty_cols, axis=1)
```

_**OR**_

```python
reduced_X_train = X_train.dropna(axis=1, how='any')
reduced_X_valid = X_valid.dropna(axis=1, how='any')

'''
df.dropna autmagically makes a copy of the DataFrame as I understand it.
You can use "inplace=True" as an additional parameter to change that behaviour
'''
```

`MAE: 17837`

---

### Simple Imputation

```python
from sklearn.impute import SimpleImputer

imputer = SimpleImputer()

imputed_X_train = pd.DataFrame(imputer.fit_transform(X_train.copy()), columns=X_train.copy().columns)
imputed_X_valid = pd.DataFrame(imputer.fit_transform(X_valid.copy()), columns=X_valid.copy().columns)
```

`MAE: 18062`

Surprisingly, imputation performed _worse_ than dropping columns, this can probably be attributed to noise in the data or certain data points not being translated properly. Imagine filling in for `Total_Garages` as 1.5 when the house does not even have one.
There is also the factor of alternate strategies, such as using `mean` instead of `median`

---

### Extended Imputation

Virtually the same steps as before

```python
from sklearn.impute import SimpleImputer

imputer = SimpleImputer(strategy='median')

for col in empty_cols:
  extended_X_train = extended_X_train[col].isna()
  extended_X_valid = extended_X_valid[col].isna()

''' a True or False value if there is a missing value or not '''

extended_X_train = pd.DataFrame(imputer.fit_transform(X_train.copy()), columns=X_train.columns.copy())
extended_X_valid = pd.DataFrame(imputer.fit_transform(X_valid.copy()), columns=X_valid.columns.copy())
```

`MAE: 17791`

Better but still not as good as outright dropping those columns
