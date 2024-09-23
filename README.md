# R-basics
This is a study guide that helps new R programmers understand the basics of R for data analytics. 



# Data Analysis with Tidyverse

This document outlines the key data analysis techniques using the `tidyverse` package, including data manipulation, visualization, and handling missing data.

## 1. Loading Libraries and Data Exploration

We start by loading the `tidyverse` package, which provides essential tools for data manipulation (`dplyr`) and visualization (`ggplot2`).

Exploring Data

You can inspect datasets using the following commands:

```
library(tidyverse)

names(): #Displays the column names of the dataset.
names(mpg): 

head(): #Shows the first few rows of the dataset.
head(mpg)

str(): #Provides a concise summary of the data structure.
str(tom.data)

dim(): #Returns the dimensions of the dataset (number of rows and columns).
dim(tom.data)

```


## 2. Data Visualization with ggplot2
- Histograms
  - Histograms visualize the distribution of numeric variables. Here's an example plotting displ (engine displacement) from the mpg dataset:
  - Why this is helpful: Zero values can skew the results and create misleading visualizations. By excluding zero values, you ensure the true distribution of non-zero data is accurately represented.

```
ggplot(mpg, aes(x = displ)) +
  geom_histogram(col = "red", fill = "green") +
  ggtitle("MPG Histogram")

```

- Handling Distribution Issues: Removing Zero Values
  - When creating histograms or boxplots for numeric data, it’s important to account for values like zero that can distort the distribution. In this example, we filter out zero values from the aroma column in the coffee_ratings dataset.

```
hist(coffee_ratings$aroma[coffee_ratings$aroma > 0]) #zeros can cause distribution issues
boxplot(coffee_ratings$aroma[coffee_ratings$aroma > 0]) #zeros can cause distribution issues
```
- Bar Plots
  - Bar plots display the frequency of categorical variables. Below is a plot for the vehicle class:

```
ggplot(mpg, aes(x = class)) +
  geom_bar(col = "purpled", fill = "cyan") +
  ggtitle("Class of Vehicle")
```
- Scatter Plots
  - Scatter plots visualize the relationship between two numeric variables, like displ (engine displacement) and cty (city miles per gallon):

```
ggplot(mpg, aes(x = displ, y = cty)) +
  geom_point(size = 3, col = "red") +
  ggtitle("Displ vs Cty")
```

- Box Plots
  - Box plots display the distribution of a numeric variable for each category of a categorical variable. Here's an example showing displ across different vehicle classes:

```
ggplot(mpg, aes(x = class, y = displ)) +
  geom_boxplot() +
  ggtitle("Class vs Displ")
```

## 3. Data Manipulation with dplyr
Summarizing Data

The summarize_all() function calculates summary statistics (mean, standard deviation) for all numeric variables.

```
tom_data_mean_sd <- tom.data %>%
  select_if(is.numeric) %>%
  summarize_all(funs(mean(., na.rm = TRUE), sd(., na.rm = TRUE)))
```

Extracting Top N Observations

You can use slice_max() to extract the top n observations from a dataset based on a given column, such as getting the top 10 varieties from cat.dat$variety.

```
cat.dat$variety %>% slice_max(n, n=10)

    #Why this is helpful: This function helps you quickly filter the top n observations, making it easy to focus on the most relevant data (e.g., most common varieties in the dataset).
```

Grouping and Summarizing

Use group_by() and summarize() to calculate summary statistics within groups. For example, summarizing price for different room_type categories in the airbnb dataset:

```
airbnb.data %>%
  group_by(room_type) %>%
  summarize(mean = mean(price, na.rm = TRUE), sd = sd(price, na.rm = TRUE))
```

Finding Most Frequent Categories

Use table() and which.max() to find the most frequent value in a categorical column, such as the most common property_type in the airbnb.data.

```
table(airbnb.data$property_type)
which.max(table(airbnb.data$property_type))

    #Why this is helpful: Knowing the most frequent category in a dataset can provide quick insights, such as understanding the most common property type or item in your data.
```

## 4. Handling Missing Data
Removing Missing Data

Functions like mean() and sd() include the na.rm = TRUE argument to exclude missing values from calculations.

```
mean(tom.data$rating, na.rm = TRUE)
```
Counting Missing Values

You can count missing values in each column using apply():

```
missing_counts <- colSums(is.na(quant_vars))
```

Example: Counting and Sorting Missing Values

This example uses apply() to count missing values in the coffee_ratings dataset, converting the result into a data frame, and sorting it in descending order.

```
apply(coffee_ratings, 2, function(x) sum(is.na(x))) %>%
  data.frame() %>%
  `colnames<-`(c("missing_values")) %>%
  arrange(desc(missing_values))

    #Why this is helpful: Identifying which columns have the most missing values helps focus your cleaning efforts and determine whether to drop or impute those columns.
```

## 5. Categorical Data Summary
Frequency Tables

table() and prop.table() functions generate frequency and proportion tables for categorical variables:

```
table(tom.data$actor)
prop.table(table(tom.data$actor))
```
Filtering and Summarizing Categorical Data

Use filter() to subset data based on conditions. Here's an example filtering rows where the country_of_origin is "Mexico" or "Brazil":

```
coffee_ratings_reduced <- coffee_ratings %>%
  filter(country_of_origin == "Mexico" | country_of_origin == "Brazil")
```

## 6. Data Frames: What and Why?

A data frame is a two-dimensional structure (like a table) used to store datasets in R. Each column represents a variable, and each row represents an observation.
Creating a New Data Frame

You might want to create a new data frame that contains only the relevant columns for your analysis. For example, the code below creates a data frame with the total_cup_points and altitude columns from the coffee_ratings dataset:

```
coffee_ratings_cup_alt_frame <- coffee_ratings %>% select(total_cup_points, altitude)
names(coffee_ratings_cup_alt_frame)

    Why this is helpful: Creating a new data frame with only the required columns simplifies your analysis by reducing the dataset to the variables you're interested in.
```

## 7. Advanced Data Wrangling
Calculating Profit and Summarizing by Category

You can create new variables by performing calculations within mutate(). In this example, we calculate the profit for men's and women's sports and then summarize the average profit for each category.

``` 
sports %>% 
  filter(grepl("Division I", classification_name)) %>%
  mutate(men_profit = rev_men - exp_men, women_profit = rev_women - exp_women) %>%
  summarize(mens_sports = mean(men_profit, na.rm = TRUE), 
            womens_sports = mean(women_profit, na.rm = TRUE))

    #Why this is helpful: This code not only filters the dataset for Division I sports but also adds new columns that calculate profit for each gender. Summarizing the data afterwards gives insights into average profitability across categories.
```
Excluding Categories from Analysis

Sometimes, you may want to exclude specific categories from your analysis. The ! operator is used to perform the opposite of the filter condition, excluding unwanted data.

```
sports %>% 
  filter(!grepl("Division III", classification_name)) %>%
  filter(grepl("NCAA", classification_name)) %>%
  tabyl(classification_name)

    #Why this is helpful: This code filters out Division III schools while focusing on NCAA categories, refining your dataset for more targeted analysis.
```

## 8. Saving Data

You can save the processed data to a CSV file using write.csv():

```
write.csv(tom.data, "tom.data.csv")
```

## 9. Applying Functions Across Multiple Variables

- apply() and sapply() functions are useful for applying a function across all columns in a dataset. For example, calculating the mean of all numeric columns in tom.data:

```
sapply(Filter(is.numeric, tom.data), mean, na.rm = TRUE)
```

- Summarizing data with sapply() function
```
  #summary statistics - multiple variables
  sapply(Filter(is.numeric,tom.data), mean, na.rm=T)
  sapply(Filter(is.numeric,tom.data), sd, na.rm=T)
  sapply(Filter(is.numeric,tom.data), fivenum)
  sapply(Filter(is.numeric,tom.data), median, na.rm=T)
```
