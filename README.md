# Exploratory Data Analysis Using Pandas

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

## Lab Goals

This lab covers the basic of data wrangling and manipulation in Python using `pandas`. 

By the end of this lab, students will be able to:
- Compute basic summary statistics in Python using `pandas`
- Understand the basics of working with time series data in Python using `pandas`
- Manipulate and reshape data using `pivot`, `melt`, `pivot_table`, `stack`, and `unstack`
- Combining and joining datasets using `concat` and `merge`
- Understand the basics of renaming, mapping, and reindexing structured data in Python using `pandas`

[Click here](https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/eda-pandas.ipynb) and select the "Save as" option to download this lab as a Jupyter Notebook.

## Acknowledgements

Information and exercises in this lab are adapted from the following resources:
- `pandas` package ["Getting started"](https://pandas.pydata.org/pandas-docs/stable/getting_started/intro_tutorials/) documentation.
- Wes McKinney's [*Python for Data Analysis: Data Wrangling With pandas, Numpy, and IPython*](https://www.oreilly.com/library/view/python-for-data/9781491957653/) (O'Reilly, 2017)
  * Chapter 5 "Getting Started with pandas" (125-168)
  * Chapter 7 "Data Cleaning and Preparation" (195-224)
  * Chapter 8 "Data Wrangling: Join, Combine, and Reshape" (225-256)
  * Chapter 10 "Data Aggregation and Group Operations" (293-322)
  
All figures shown in this lab are from the `pandas` "Getting Started" tutorials.

# Table of Contents

- [Summary Statistics and Calculations](#summary-statistics-and-calculations)
  * [Creating New Columns Based on Existing Columns](#creating-new-columns-based-on-existing-columns)
- [Manipulating and Reshaping Data](#manipulating-and-reshaping-data)
  * [Working with Time Series Data](#working-with-time-series-data)
  * [`.pivot()`](#pivot)
  * [`.melt()`](#melt)
  * [`.pivot_table()`](#pivot_table)
  * [`.stack()` and `.unstack()`](#stack-and-unstack)
- [Combining Data](#combining-data)
  * [`.concat()`](#concat)
  * [`.merge()`](#merge)
- [Renaming, Mapping, and Reindexing](#renaming-mapping-and-reindexing)
  * [Renaming Columns](#renaming-columns)
- [Practice Problems](#practice-problems)
- [Lab Notebook Questions](#lab-notebook-questions)  

# Summary Statistics and Calculations

1. `pandas` comes with built-in functionality for performing common mathematical and statistical calculations.

2. Most of these mathematical methods fall under the umbrella of summary statistics.

3. We could use `.mean()` to get the average age of Titanic passengers.
```Python
# load titanic data from url
titanic = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/pandas-intro/main/titanic.csv)

# show first 5 rows of newly-loaded dataframe
titanic.head(5)

# calculate mean
titanic["Age"].mean()
```

4. We could also calculate the median age and ticket fair using `.median()`.
```Python
titanic[["Age", "Fare"]].median()
```

5. By default, these descriptive statistics will ignore missing data.

6. In a situation where missing data or `NaN` values need to be part of the calculation, we would set `skipna` to `False`.
```Python
titanic.sum(axis=0, skipna=True)
```

7. In the `.sum()` example, we use `axis=` to specify what axis to perform the mathematical operation on.
- `axis=0` indicates columns
- `axis=1` indicates rows

8. `skipna=True` means rows with missing data will not be part of the mathematical operation.

9. Remember `.describe()` returns aggregate information on the entire dataset.

10. We can use `.agg()` to return specific combinations of aggregate statistics for specific columns.

11. Say we wanted to compute `min`, `max`, `median`, and `skew` for the `Age` column and `min`, `max`, `median`, and `mean` for the `Fare` column.

12. We can specify those statistics and those columns using `.agg()`.
```Python
titanic.agg(
  {
    "Age": ["min", "max", "median", "skew"],
    "Fare": ["min", "max", "median", "mean"],
   }
 )
```

13. Consult `pandas` ["Descriptive statistics"](https://pandas.pydata.org/pandas-docs/stable/user_guide/basics.html#descriptive-statistics) documentation for more on descriptive statistics in `pandas`.

14. We can also aggregate summary statistics by category, using `.groupby()`.

15. To find out the average age for female versus male passengers:
```Python
titanic[["Sex", "Age"]].groupby("Sex").mean()
```

16. The first set of brackets (`[]`) isolates a subselection with only these two columns.

17. Then we apply the `.groupby()` method to the `Sex` column to calculate `.mean()` for each unique value represented in the `Sex` field.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_1.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_1.svg?raw=true" /></a></p>

18. Generally speaking, this type of `pandas` operation follows a `split-apply-combine` pattern.
- First we `split` the data into groups.
- Then we `apply` a function to each group independently.
- Then we `combine` the function results into a data structure.

19. `pandas` syntax combines the `apply` and `combine` steps.

20. Another way to calculate average age by gender:
```Python
titanic.groupby("Sex")["Age"].mean()
```

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_2.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_2.svg?raw=true" /></a></p>

21. In this alternate example, the column name in the `.groupby()` parenthesis specifies the column to group by.

22. The column name in brackets (`[]`) specifies the column to perform the mathematical function on.

23. As a last example, let's say we want the mean ticket fare price for each of the gender and cabin class combinations.

24. We can think through the underlying logic for that program.
- First we need to `split` the data into groups for gender and cabin class
- Then we need to `apply` a mean calculation to the ticket fare column for each of those  groups
- The last step will be to `combine` all that information into a data structure

25. To express that programatically in Python:
```Python
titanic.groupby(["Sex", "Pclass"])["Fare"].mean()
```

26. Consult `pandas` ["Group by" documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/groupby.html#groupby) to learn more about this function.

27. We can also count the number of records by category using `.value_counts()`.

28. The `.value_counts()` method returns the number of records for each category in a column.

29. Let's say we wanted to know the number of passengers for each cabin class.

30. Thinking through the underlying logic, we want Python to count how many records are contained for each unique value in the `Pclass` column.

31. That requires identifying the unique values, counting the number of instances for each, and returning those values as a sum.

32. To express that progrmatically in Python:
```Python
titanic["Pclass"].value_counts()
```

32. We could also break out these steps using a combination of `.groupby()` and `.count()`:
```Python
titanic.groupby("Pclass")["Pclass"].count()
```

33. In the second example, we are explicitly categorizing the data by category in the `Pclass` column, then performing a `.count()` operation on the records for each `Pclass` category.

34. A few key takeaways:
- We can calculate aggregate statistics for entire rows or columns
- `.groupby()` follows a `split-apply-combine` pattern
- `.value_counts()` can be a shorthand for getting the number of entries for each category in a field

<blockquote>Q1: Using the titanic dataset (or another dataset), write code that calculates at least 3 unique summary statistics (.agg() counts as one). Include code + comments for each.</blockquote>

## Creating New Columns Based on Existing Columns

35. Let's introduce a new sample dataset, this time with air quality data for measurement stations in London, Paris, and Antwerp.

36. Values in this dataset include nitrogen dioxide (<code>NO<sub>2</sub></code>) concentration expressed as parts per million (`ppm`).

```Python
# load air quality data from url
air_quality = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/data/air_quality_no2.csv", index_col=0, parse_dates=True)

# show first 5 rows of newly-loaded dataframe
air_quality.head()
```

37. Let's say we wanted to express the London station's <code>NO<sub>2</sub></code> concentration as milligrams per cubic meter (mg/m<sup>3</sup>).

38. For our purposes, we are assuming a temperature of 25 degrees Celsius and pressure of 1013 hPa, which means the conversion factor is 1.882.

39. We would need to convert all of the `station_london` column values from `ppm` to <code>mg/m<sup>3</sup></code>.

40. And we would want to store the results of that calculation in a newly-created column.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_2.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_2.svg?raw=true" /></a></p>

To express those steps programatically in Python:
```Python
air_quality["london_mg_per_cubic"] = air_quality["station_london"] * 1.882

air_quality.head()
```

41. Note that we do not need to iterate over all rows for a specific dataframe column to perform this calculation.

42. `pandas` performs the calculation `element_wise`, that is on all of the values in the column at once.

43. Let's say we wanted to calculate the ratio of the Paris versus Antwerp station values and store that result in a new column.

44. We would need to calculate the ratio for each row and store the results of the calculation in a new column.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_3.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_3.svg?raw=true" /></a></p>

45. To express those steps programatically in Python:
```Python
air_quality["ratio_paris_antwerp"] = (air_quality["station_paris"] / air_quality["station_antwerp"])

air_quality.head()
```

46. Again, because this is an element-wise calculation, the division operation is applied to all rows in the data frame.

47. Python's other mathematical (`+`, `-`, `*`, `/`) and logical (`<`, `>`, `=`, etc.) all work element-wise.

<blockquote>Q2: Describe element-wise calculation in your own words.</blockquote>

<blockquote>Q3: Using the air quality data or another dataset, write code that generates a new column based on an existing column(s). Include code + comments.</blockquote>

# Manipulating and Reshaping Data

48. We've already covered how to sort or group by values in a dataframe.

49. We can also perform more drastic data manipulations and transformations using `pandas`.

## Working with Time Series Data

50. At this point you may be wondering what exactly we are supposed to do with the convoluted string of numbers and characters in the air quality data's `datetime` field.

51. Let's first break down the information contained in this field.

52. Looking at a single `datetime` value: `2019-06-21 00:00:00+00:00`

53. Data contained in this string:
- year (2019)
- month (6)
- day (21)
- hour (00 before first colon)
- minute (00 between two colons)
- second (00 after second colon)
- UTC offset, or time zone (00:00 after + symbol)

54. We can imagine a number of different scenarios in which we would want to interact with this information as a time series object, rather than just a string of characters.

55. Initially, `pandas` is treating the `datetime` field as a character string.

56. We can use the `.to_datetime()` function to convert this field to a datetime object.

```Python
air_quality["datetime"] = pd.to_datetime(air_quality["datetime"])

# check column data type
air_quality["datetime"]
```

57. Now that we have the `datatime` field as a datatime object, we can access a number of specialized commands.

```Python
# identify start and end times for time series data
air_quality["datetime"].min(), air_quality["datetime"].max()

# calculate length of time series
air_quality["datetime"].max() - air_quality["datetime"].min()
```

58. If we know we are loading data with a datatime like field, we can specify how `pandas` should parse the datetime field in the newly-created data frame using `parse_dates`.

```Python
pd.read_csv("../data/air_quality_no2_long.csv", parse_dates=["datetime"])
```

59. For more on datatime objects and time series data in Python:
- [`pandas` documentation on time related concepts](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html)
- [`pandas` documentation on timestamps](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp)
- [Python documentation on datetime](https://docs.python.org/3/library/datetime.html)

## `.pivot()`

60. Let's look at an alternate structure for the `air_quality` data.

61. In `air_quality_no2.csv`, each row is a unique `datetime` and there are distinct columns for each station location.

62. By comparison, navigate to https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/air_quality_long.csv in a web browser.

63. This dataset includes the following variables or columns:
- `city`: city where the sensor is used, either Paris, Antwerp or London
- `country`: country where the sensor is used, either FR, BE or GB
- `location`: the id of the sensor, either FR04014, BETR801 or London Westminster
- `parameter`: the parameter measured by the sensor, either <code>NO<sub>2</sub></code> or Particulate matter
- `value`: the measured value
- `unit`: the unit of the measured parameter, in this case ‘µg/m³’
- `datetime`: the index for this DataFrame

64. For this example, we only want the first two measurements for each location's <code>NO<sub>2</sub></code> data observations.

65. Our first step is to select only the rows with <code>NO<sub>2</sub></code> observations.

66. Then we need to group by station location and isolate the first two observations for each unique location.

67. To express that programatically in Python:

```Python
air_quality = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/data/air_quality_long.csv")

# create subset with only no2 data
no2 = air_quality[air_quality["parameter"] == "no2"]

# group by station locations
no2_subset = no2.sort_index().groupby(["location"]).head(2)

# verify the output for our no2_subset dataframe
no2_subset
```

68. Let's imagine we want the three station values for <code>NO<sub>2</sub></code> to appear as adjacent columns.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_4.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_4.svg?raw=true" /></a></p>

69. We can accomplish this transformation using the `.pivot()` function.
```Python
no2_subset.pivot(columns="location", values="value")
```

70. In this example `.pivot()` is only reshaping the data--nothing about the underlying data values is changed.

71. Another example of `.pivot()`:

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_5.png?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_5.png?raw=true" /></a></p>

72. We create a new data frame in which the unique values in `foo` our row index, and the `bar` categories our columns.

```Python
new_data_frame = old_data_frame.pivot(index="foo", columns="bar", values="baz")
```

73. Consult the `pandas` documentation ["Pivoting DataFrame objects"](https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html#reshaping-reshaping) for more on this function.

## `.melt()`

74. We can also reshape data using `.melt()`, which is helpful when we need to make column names a unique field value.

75. `.melt()` is the inverse of `.pivot()`-- it transforms wide tables into long tables. 

76. We'll use an example data frame that has height and weight information for two individuals.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_8.png?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_8.png?raw=true" /></a></p>

77. In this example, we might want to be able to isolate height and weight in ways that the original data structure will not let us.

78. We want to isolate some of our columns as identifier variables and have other columns be measured variables.

79. We can use `.melt()` to transform this data so each row is a unique observation.
```Python
old_data_frame.melt(id_vars=['first_identifier_variable_column', 'second_identifier_variable_column'])
```

80. We could also rename this newly-created identifier variable column:
```Python
old_data_frame.melt(id_vars=['first_identifier_variable_column', 'second_identifier_variable_column'], var_name="new_identifier_variable_column_name")
```

81. Applying this to our air quality data, we can take the wide table format created by `.pivot()` and transform it back into a long data format using `.melt()`.
```Python
# create wide data format
no2_pivoted = no2.pivot(columns="location", values="value").reset_index()

# verify wide data
no2_pivoted.head()
```

82. We can transform our pivoted wide data back into a long data format using `.melt()`.
```Python
no_2 = no2_pivoted.melt(id_vars="date.utc")

no_2.head()
```

83. In this example, the column headers become variable names in a newly-created column.

84. Consult the `pandas` documentation ["Reshaping by melt"](https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html#reshaping-melt) for more details on using this function.

## `.pivot_table()`

85. In some cases, we might want to create a new table with calculations derived from an original data frame.

86. For example, let's say we want the concentrations for <code>NO<sub>2</sub></code> and <code>PM<sub>2.5</sub></code> for each station stored as a table.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_9.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_9.svg?raw=true" /></a></p>

87. We can create a pivot table that is based on the underlying dataframe to display these aggregate calculations.

88. We need to specify the values, index, columns, and aggregate function we are using to create the pivot table.

89. To express this programatically in Python:
```Python
air_quality.pivot_table(values="value", index="location", columns="parameter", aggfunc="mean")
```

We could also use `.groupby()` and `.mean()` to generate the same output.
```Python
air_quality.groupby(["parameter", "location"]).mean()
```

90. Consult the `pandas` [documentation on pivot tables](https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html#reshaping-pivot) to learn more about this function.

## `.stack()` and `.unstack()`

91. We can also reshape data using `.stack()` and `.unstack()`.

92. In these examples, our data frame has multiple indeces, or a hierarchical multi-index.

93. We might want to reconfigure how Python handles the hierarchical labels.

94. We can do this using `.stack()` and `.unstack()`.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_6.png?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_6.png?raw=true" /></a></p>

95. To reshape this data using `.stack()`:
```Python
stacked_data_frame.unstack()
```

96. `.unstack()` is the inverse operation of `.stack()`.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_7.png?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_7.png?raw=true" /></a></p>

97. To reshape this data using `.unstack()`:
```Python
unstacked_data_frame.stack()
```

<blockquote>Q4: In your own words, provide a brief description/explanation for each function: .pivot(), .melt(), .pivot_table(), .stack(), and .unstack().</blockquote>

<blockquote>Q5: Write sample code for both functions. Include code + comments.</blockquote>

<blockquote>Q6: Describe how you are thinking about needing to use these functions in relation to data you're interested in working with as part of the final project.</blockquote>

# Combining Data

98. The SQL queries and joins lab covered how we can use joins in a relational database system to create new data structures.

99. `pandas` has somewhat similar functionality that allows you to merge and combine data from multiple tables.

100. `pandas.merge` connects rows in DataFrames based on one or more key fields. This is similar to SQL JOIN operations.

101. `pandas.concat` concatenates or "stacks" objects together along an axis.

102. `combine_first` allows you to splice overlapping data to fill in missing values.

103. Let's load more new air quality datasets.
- The `air_quality_no2_long.csv` file provies NO<sub>2</sub> values for three measurement stations.
- The `air_quality_pm25_long.csv` file provides PM<sub>25</sub> values (particulate matter less than 2.5 micrometers) for the same three measurement stations.
- The `air_quality_stations.csv` file provides latitude and longitude coordinates for five different measurement stations.
- The `air_quality_parameters.csv` file provides parameter full description and name for five different element types.

104. To load these datasets:
```Python
# load no2 observation data
air_quality_no2 = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/data/air_quality_long.csv", parse_dates=True)

# set column names
air_quality_no2 = air_quality_no2[["data.utc", "location", "parameter", "value"]]

# make sure columns are renamed and no2 data is loaded
air_quality_no2.head()

# load pm25 data
air_quality_pm25 = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/data/air_quality_pm25_long.csv", parse_dates=True)

# rename columns
air_quality_pm25 = air_quality_pm25[["data.utc", "location", "parameter", "value"]]

# make sure columns are renamed and pm25 data is loaded
air_quality_pm25.head()

# load coordinates data
stations_coord = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/data/air_quality_stations.csv")

# make sure coordinate data is loaded
stations_coord.head()

# load parameter data
air_quality_parameters = pd.read_csv("https://raw.githubusercontent.com/kwaldenphd/eda-pandas/main/data/air_quality_parameters.csv")

# make sure parameter data is loaded
air_quality_parameters.head()
```

## `.concat()`

105. Let's say we want to combine the NO<sub>2</sub> and PM<sub>25</sub> measurements in a single table.

106. Since the two original tables have a similar strucure, we can perform a concatenation operation on multiple tables using one of the axes.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_10.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_10.svg?raw=true" /></a></p>

107. We can do this using the `.contact()` function.
```Python
air_quality = pd.concat([air_quality_pm25, air_quality_no2], axis=0)

air_quality.head()
```

108. The default for `.concat()` is axis 0, so the resulting table combines the input table rows.

109. Remember in a `pandas` dataframe axis `0` is vertical (rows) and axis `1` is horizontal (columns).

110. We can verify the concatenation operation worked by checking the same of each original table and the concatenated table.
```Python
print('Shape of the ``air_quality_pm25`` table: ', air_quality_pm25.shape)

print('Shape of the ``air_quality_no2`` table: ', air_quality_no2.shape)

print('Shape of the resulting ``air_quality`` table: ', air_quality.shape)
```

111. Some fast math tells us that 1110 + 2068 is 3178, the number of rows in the combined table.

112. We can sort the new table to see the combined data.
```Python
air_quality = air_quality.sort_values("date.utc")

air_quality.head()
```

113. The data sorted by `data.utc` shows observations for both NO<sub>2</sub> and PM<sub>25</sub>.

114. In this example, the resulting values in the `parameter` column make it easy to see what data came from each original table.

115. In a situation where we don't have something like the `parameter` column, we can add an additional row index can help identify the data source.
```Python
air_quality_ = pd.concat([air_quality_pm25, air_quality_no2], keys=["PM25", "NO2"])

air_quality_.head()
```

116. By giving a `keys` argument to the `.concat()` function, we create a hierarchical index or a MultiIndex.

117. We encountered MultiIndex previously when looking at `.stack()` and `.unstack()`.

118. Consult the `pandas` [documentation on object concatenation](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html#merging-concat) for more on this function.

## `.merge()`

119. We can also join table using a common identifier with `.merge()`.

120. Let's say we wanted to add station location coordinates to corresponding rows in the measurements table.

121. We have this data loaded as `stations_coord`.

<p align="center"><a href="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_11.svg?raw=true"><img class="aligncenter" src="https://github.com/kwaldenphd/eda-pandas/blob/main/figures/Figure_11.svg?raw=true" /></a></p>

122. We can merge the `stations_coord` and `air_quality` data frames based on a common field, `location`.

123. To express this programatically in Python
```Python
air_quality = pd.merge(air_quality, stations_coord, how="left", on="location")

air_quality.head()
```

124. You might notice some similarities with SQL join syntax, in that we're are specifying the origin and target tables, the type of join, and the key field.

125. Specifying a left join means only locations in the original `air_quality` dataframe will be retained.

126. Let's say we want to add the parameter full description and name to the measurements table.

127. We already have the parameter data loaded in the `air_quality_parameters` dataframe.

128. In this example, there is no common column name.

129. However, we can `parameter` column in the `air_quality` data frame has a common format with the `id` column in the `air_quality_parameters` datarame.

130. We can use the `left_on` and `right_on` arguments to specify the fields to join on.

```Python
air_quality = pd.merge(air_quality, air_quality_parameters, how='left', left_on='parameter', right_on='id')

air_quality.head()
```

131. For more on different join/merge types:
- [database style merging `pandas` documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html#merging-join)
- [SQL comparison page in `pandas` documentation](https://pandas.pydata.org/pandas-docs/stable/getting_started/comparison/comparison_with_sql.html#compare-with-sql-join)

132. Our key takeaways from this section:
- Mutliple tables can be concatenated row-rise or column-wise using the `.concat()` function
- SQL-style joins can be accomplished using `.merge()`

<blockquote>Q7: In your own words, provide a description for .concat() and .merge(). What do these functions do? How are they different?</blockquote>

<blockquote>Q8: Write sample code for both functions. Include code + comments.</blockquote>

<blockquote>Q9: Describe how you are thinking about needing to use these functions for the data you plan to work with in the final project.</blockquote>

# Renaming, Mapping, and Reindexing

133. As a result or as part of data wrangling operations, you may need to rename columns or renumber the rows in a dataframe.

## Renaming Columns

134. We can use `.rename()` to rename columns in a dataframe.

135. We use a dictionary's key-value pairs to specify the new name for an existing column.

136. To do this in place and change the names in an existing dataframe:
```Python
data_frame.rename(columns={"old_name_1": "new_name_1", "old_name_2": "new_name_2", "old_name_3": "new_name_3"})

data_frame.head()
```

137. We can also create a new dataframe with the renamed columns.
```Python
new_data_frame = old_data_frame.rename(columns={"old_name_1": "new_name_1", "old_name_2": "new_name_2", "old_name_3": "new_name_3"})

new_data_frame.head()
```

138. We can also standardize axis labels using `str` case methods (`upper`, `lower`, `title`, etc.)
```Python
# relabel index labels to upper case version of existing labels
data_frame.rename(str.upper)

# relabel index labels to lower case version of existing labels
data_frame.rename(str.lower)
```

139. We can also rename row index labels usign `.rename()`.
```Python
data_frame.rename({"old_row_1": "new_row_1", "old_row_2": "new_row_2", "old_row_3": "new_row_3"}, axis="index")
```

140. In the `data_frame.rename()` examples, the `.rename()` method is applied to a copy of the data--the underlying data is not changed.

141. To alter the underlying data, we would set the `inplace` parameter to `True`.
```Python
data_frame.rename(RENAMING OPERATION, inplace=True)
```

142. For more on renaming functions, consult the `pandas` documentation for [`pandas.DataFrame.rename`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.rename.html#pandas.DataFrame.rename).

143. We can also imagine a scenario in which you have sliced or filtered the data and now have row index labels that no longer make sense.

144. For example, if your original rows had sequential numerical index labels, the transformed data will retain the original index labels.

145. In these situations, we can reset the index to a simple ascending integer index using `.reset_index()`.

```Python
data_frame.reset_index()
```

146. For more on indexing, consult `pandas` ["Different choices for indexing"](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html?highlight=reindex#different-choices-for-indexing) documentation.

# Practice Problems

147. No practice problems. Spend this time working on the final project.

# Lab Notebook Questions

Q1: Using the titanic dataset (or another dataset), write code that calculates at least 3 unique summary statistics (.agg() counts as one). Include code + comments for each.

Q2: Describe element-wise calculation in your own words.

Q3: Using the air quality data or another dataset, write code that generates a new column based on an existing column(s). Include code + comments.

Q4: In your own words, provide a brief description/explanation for each function: .pivot(), .melt(), .pivot_table(), .stack(), and .unstack().

Q5: Write sample code for both functions. Include code + comments.

Q6: Describe how you are thinking about needing to use these functions in relation to data you're interested in working with as part of the final project.

Q7: In your own words, provide a description for .concat() and .merge(). What do these functions do? How are they different?

Q8: Write sample code for both functions. Include code + comments.

Q9: Describe how you are thinking about needing to use these functions for the data you plan to work with in the final project.

