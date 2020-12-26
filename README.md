# Lab #10: Exploratory Data Analysis Using Pandas

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

## Lab Goals

## Acknowledgements

# Table of Contents

# Sorting

`pandas` includes a few different built-in sorting operations.

We can sort by an index for either axis of our `DataFrame` (i.e. we can sort based on row index labels or by column name).

Going back to our Titanic passenger data, let's say we wanted to sort by passenger age.

```Python
# import pandas
import pandas as pd

# load titanic data from url
titanic = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/titanic.csv)

# show first 5 rows of newly-loaded dataframe
titanic.head(5)

# sort by passenger age and show first five rows of the sorted data
titanic.sort_values(by="Age").head()
```

The default for `.sort_values` is to sort in ascending order.

We can use `ascending=False` to sort in descending order.
```Python
titanic.sort_values(by=['Age'], ascending=False)
```

NOTE: When sorting, we are returning a sorted `DataFrame`. We ARE NOT updating the `DataFrame` in place. 

We have a couple of options to sort in place.
```Python
# create new dataframe with sorted results
titanic_by_age = titanic.sort_values(by="Age")

# check newly-created dataframe
titanic_by_age.head()

# sort values in place
titanic.sort_values(['Age'], inplace=True)

# check newly-created dataframe
titanic.head()
```

We can also sort by multiple fields.

To sort by class cabin and age, in descending order:
```Python
titanic.sort_values(by=['Pclass', 'Age'], ascending=False).head()
```

When sorting by fields with string data, `a-z` is considered `ascending` and `z-a` would be `descending`.

# Subsetting

## Select

To review, we can select specific columns from a `DataFrame`. 
```Python
# creates Series object with age values
ages = titanic["Age"]
```

We can use `[" "]` to select a specific single column of interest. 

Python returns this single column's data as a `Series` object.

We can also create a new data frame based on multiple columns.
```Python
# selects multiple columns to form new dataframe
age_sex = titanic[["Age", "Sex"]]

# checks first five rows of new dataframe
age_sex.head()
```

When selecting multiple columns, the inner brackets (`[]`) define the column names to subset or select.

The outer brackets select data from a dataframe.

In this multi-column example, `age_sex` is a `DataFrame` because it is a two-dimensional object.

For more on sorting operations in `pandas`, check out the package's ["Sorting" documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/basics.html#sorting).

## Filter

As with SQL, we can return rows in our `DataFrame` that meet specific conditions.

Let's say we wanted to create a new `DataFrame` only containing data for passengers older than 35 years.
```Python
above_35 = titanic[titanic["Age"] > 35]

# check first five rows of newly-created dataframe
above_35.head()
```

We use brackets (`[]`) to set a condition rows must meet to be assigned to the new dataframe.

If we just wanted to see whether rows meet this condition in the original `DataFrame`, we could just test for the condition without creating a new `DataFrame`.
```Python
titanic["Age"] > 35
```

Maybe we want to create a new data frame containing data on passegers in cabin class 2 and 3.
```Python
class_23 = titanic[titanic["Pclass"].isin([2, 3])]

# check first five rows of newly-created dataframe
class_23.head()
```

The `isin()` conditional function on its own would return a `True` or `False` value.

By nesting the `isin()` function in brackets (`[]`), we are filtering rows based on rows  that meet the function critera, or return as `True` from this function.

We could also break out the chained or compound conditional statement using an `OR` operator, `|`.
```Python
class_23_alternate =  titanic[(titanic["Pclass"] == 2) | (titanic["Pclass"] == 3)]

class_23_alternate.head()
```

For more on Boolean indexing and the `isin()` function:
- ["Boolean indexing," Pandas documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#indexing-boolean)
- ["Indexing with isin," Pandas documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#indexing-basics-indexing-isin)

We could also create a new dataframe with passenger data for only passengers that have a known age.
```Python
age_known = titanic[titanic["Age"].notna()]

age_known.head()
```

`.notna()` is a conditional function that returns `True` for rows that do not have a `Null` value.

For more on missing values and related functions, check out [the "Working with missing data" package documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/missing_data.html#missing-data).

## Selecting Specific Rows and Columns

Selecting lets us isolate columns, and filtering identifies specific rows.

But we can imagine a scenario in which we would want to combine these elements.

We might want to create a new dataframe containing only the names of passengers who are over 35 years old.
```Python
over_35_names = titanic.loc[titanic["Age"] > 35, "Name"]
```

`.loc` identifies the rows we are writing to the new dataframe.

What we are passing to the `loc` operator includes the row filter condition (`titanic["Age"] > 35`) and the column we are writing to the new dataframe (`Name`).

We can also select rows and columns based on their index position.

We could isolate rows 10-25 and columns 3-5 with the following expression:
```Python
titanic.iloc[9:25, 2:5]
```

We can also assign new values to our selection using `loc` or `iloc`.

`loc` isolates rows based on their value, while `iloc` isolates rows based on their index position or label.

Let's say we wanted to anonymize the first three names in the dataset.

We could do this using `titanic.iloc[0:3, 3] = "anonymous"`.

Consult the ["Different choices for indexing"](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#indexing-choice) documentation for more on indexing options.

A few key takeaways:
- We use square brackets `[]` to subset data.
- We can specify single rows or columns, a list of rows/columns, or conditional expression within those brackets
- Select specific rows and/or columns using `loc` when working with row/column names
- Select specific rows and/or columns usign `iloc` when working with index positions
- You can assign new values to selection using `loc` or `iloc`

# Reshaping Data

# Combining Data

# Manipulating Data


# Indexing

# Summary Statistics





Once within Pandas

How we could do SQL-like things with query/join/filter/etc within pandas

Where do we go from here

# Lab Notebook Questions
