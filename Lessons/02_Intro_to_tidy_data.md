# Introduction

The first important aspect of data analysis is to get the data in the correct format.
This is sometimes referred to as getting the data into the tidy format.
A tidy dataset is a prerequisite for the downstream data visualization and statistical analyses.
Almost always, the first thing you would do when you start a data analysis is to tidy the data first.

In this lesson,
you will learn how to use the `dplyr` + `tidyr` packages to achieve tidy datasets from "raw" datasets, as well as basic operations to data tables.
We will cover:

1.  Basic concepts of tabular data and what is a tidy dataset?
2.  Basic operations to a data table: `select()`, `filter()`, `mutate()`,
    `group_by()` and `summarise()`.
3.  Basic operations to multiple tables: binding, joining

As a resource, you can download [the data wrangling cheat sheet](https://rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)

# load packages

You might need to install some of these packages.

```{r}
library(tidyverse)
library(readxl)
```
The `tidyverse` a collection of R packages that share the same syntax and coding style. 
These packages work in harmony and provide simple solutions to data analysis problems. 
The `readxl` package allows us to read directly from excel files.  

These are what we need for now. We will install more as we move into more complex activities.
Again the codes in text are highlighted in `grey`. 

# Tidy dataset makes your life easy!

First let's start with an example.

```{r}
USDA_2022 <- read_csv(file = "../Data/USDA_2022_matrix.csv")

head(USDA_2022)
```

Here are data queried from USDA [National Agriculture Statitics Service](https://www.nass.usda.gov/Data_and_Statistics/)

In this table, each row is a crop, and each column is the yield at a given year. 

## Data frames

This table is called a data frame in the jargon of R.
A data frame is simply a data table. It has rows and columns.
An important aspect of data frame is that rows of the same column must be the same class of variable.
For example, in column "Crop", all values in column "Crop" are characters.
In column "2022", all values in that column are numeric.  
This is just one thing to keep in mind when working with data frames.

## Tidy data

Not all data frames are tidy. The idea of tidy data means

1.  Each row has to be an observation 
2.  Each column has be a variable

Getting your data into the tidy format will save you *A LOT OF TIME* in all your downstream analyses.
A good practice is to tidy your data FIRST before you do any analyses.

Let's look at this table again:

```{r}
head(USDA_2022)
```

Is this table a tidy data frame?

No.
Because there are up to 78 years of data for each crop, but the observations are spread out in 78 columns.
Observations spreading out in columns is sometimes called the wide format.
The wide format is easy for data collection, but unfortunately it's not how R would like to read data.
R likes tidy data, and tidy data frames need each observation to be in its individual row.

To convert a wide table into a tidy table, the command is `pivot_longer()` The syntax is `pivot_longer(columns, names_to = "name", values_to = "value")`

```{r}
USDA_tidy <- USDA_2022 %>% #the %>% ("pipe") operator means taking the output of previous row and use it as input of next row 
  pivot_longer(names_to = "Year", values_to = "Yield", cols = !Crop) 

# cols = !Crop specifies all columns other than Crop. 

head(USDA_tidy)
```


What this code chunk did is that it made two new columns:

1.  The "name" column (sometimes called the label column) is "Year":
    it houses which original columns the data came from (all the years).

2.  The "value" column is "Yield": it is the variable that was actually measured.

3.  The columns to collect data from were all the years, i.e., not the "Crop" column. 

Now this is a tidy data frame. Each row is an observation. Each column is a variable.
We have 3 variables:

*   Crop,
*   Year (many years),
*   Yield (numeric).

However, there is just one problem. We should always check each variables are recorded as the correct type of data (e.g., character vs numeric).
A quick way is calling `str()` (stands for "structure")
```{r}
str(USDA_tidy)
```
You will realize in the output it says "$ Year : chr [1:624]". 
"chr" stands for character. We need it to be numeric. 
We will talk about how to change that in a second. 


Let's look at another example. This time it's a hypothetical example that I made up.

```{r}
#ignore this chunk. It simulates the data.
example_data <- data.frame(
  "sample1" = c(1, 2, 3),
  "sample2" = c(2, 2, 2),
  "sample3" = c(3, 2, 1)
) %>% 
  mutate(gene = c("gene1", "gene2", "gene3"))

head(example_data)
```

In this experiment, 
you are looking at the expression of three genes (gene1 to gene3) in three samples (sample1 to sample3).
So you have 3 * 3 = 9 observations.
This is not a tidy data frame because the 9 measurements of expression are spread out in 3 columns.
We can tidy it using the `pivot_longer` command:

```{r}
example_data_tidy <- example_data %>% 
  pivot_longer(names_to = "sample", values_to = "expression", cols = c(sample1, sample2, sample3)) 

example_data_tidy
```

1.  The "name" column now is "sample": it houses which original columns the data came from (sample1 to sample3).
2.  The "value" column is "expression": it is the variable that was actually measured.
3.  The columns to collect data from were sample1 to sample3. Alternatively, `cols = !gene` (all columns but gene) should also work. 

Now this is a tidy data frame. Each row is an observation. Each column is a variable.
We have 3 variables: sample (3 samples), genes (gene1 to gene3), and expression (numeric).

# Basic operations to a data frame.

Now let's talk about some of the basic operations for data frames, which includes:

*   How to subset a data frame,
*   How to create new columns in a data frame and summarise a data frame.

These are very useful and you will probably use these functions all the time. 

## Subsetting

There are actually two ways to subset a data frame: subsetting by columns and subsetting by rows.
Let's do subsetting by columns first.

Using our tidy USDA data now,
say we only want the columns count and treatment and leave out the rest of the columns.
The command is simple, just `select()` the columns you want.

```{r}
head(USDA_tidy)

USDA_tidy_2 <- USDA_tidy %>% 
  select(Crop, Yield) #just selecting Crop and Yield 

head(USDA_tidy_2)
```

Now we just get two columns.

The more useful subsetting function is subsetting by rows based on the values of one or more columns.
The command is `filter()`. 
In the USDA data, let's say we only want years that have data because data were not collected for some years for some crops.  

```{r}
USDA_tidy_3 <- USDA_tidy %>% 
  filter(is.na(Yield) == FALSE) # filter for rows that the Yield value is not NA  

nrow(USDA_tidy)
nrow(USDA_tidy_3)
```

Now the data frame went from 624 rows to 578 rows.
So there were actually a total of 578 of data.
Note: in `filter()`, you should use `==` to mean "equal".

Let's say we only want corn. 

```{r}
USDA_tidy_corn <- USDA_tidy_3 %>% 
  filter(Crop == "corn") 

nrow(USDA_tidy)
nrow(USDA_tidy_corn)
```

Looks like there are 78 years of data for corn. 

Let's say you want to filter for corn and wheat: 

```{r}
USDA_tidy_corn_wheat <- USDA_tidy_3 %>% 
  filter(Crop == "corn" |
           Crop == "wheat") 

nrow(USDA_tidy)
nrow(USDA_tidy_corn_wheat) 
```

The vertical bar `|` means "or" in R. In this chunk you are filtering for either corn or wheat.
You shouldn't use `&` ("and") here, because there is no crop that is both corn and wheat.

## Making new columns

Oftentimes you will need to make new variables based on existing ones.
The command is `mutate()`.
As a biologist, I don't think "mutate" sound too intuitive, but it is what it is.

We realized earlier the "Year" column is specified as a character variable. 
We need to convert that to a numeric variable. 

```{r}
USDA_tidy_3 <- USDA_tidy_3 %>% 
  mutate(Year = as.numeric(Year)) 

head(USDA_tidy_3)
```

Now you get a new column Year that is recorded as numeric.  
As you can imagine, you can do any mathematical operations to a
numerical column within `mutate()`.

Let's do another example. 
The yield for every crop other than cotton, canola, and rice are recorded in bushel/acre. Cotton, canola, and rice were recorded in lbs.  
The number of pounds in a bushel vary by grain.  
Let's say there are 60 lbs in 1 bushel of wheat but only 56 lbs for corn.
48 lbs/bu for barley, 60 lbs/bu for soybean, and 56 lbs for sorghum. 
Let's convert all the yield units to lbs/acre.  

```{r}
USDA_tidy_lbs <- USDA_tidy_3 %>% 
  mutate(Yields_lbs = case_when(
    Crop == "barley" ~ Yield * 48,
    Crop == "corn" ~ Yield * 56, 
    Crop == "soybean" ~ Yield * 60,
    Crop == "sorghum" ~ Yield * 56, 
    Crop == "wheat" ~ Yield * 60,
    T ~ Yield
  ))

head(USDA_tidy_lbs)
```

`case_when()` is an extremely useful command within `mutate()`.
The syntax is `case_when(condition ~ outcome)`.
For example, when Crop is barley, it applies a value of 48 lbs/bu. 
The `T ~ ...` inside `case_when()` stands for "for the rest that are not the above". 

## Summarise

Before you do real statistical inference, maybe you want to just take a look at the summary statistics. 
This can be done easily using the `group_by()` and `summarise()` commands.

Let's simulate an experiment. In this experiment, let's say there are two groups: one experimental treatment and a control. 
The experiment was conducted in two locations: A and B. 
And let's say we measured some kind of response. 
Say I would like the average, standard deviation and number of observations for each group. 

```{r}
# You can ignore this chunk. 
trt <- rnorm(n = 10, mean = 10, sd = 1)
ctrl <- rnorm(n = 10, mean = 6, sd = 1)
location <- c(rep("A", 5), rep("B", 5))

example_experiment <- data.frame(
  ctrl,
  trt,
  location
) %>% 
  pivot_longer(names_to = "group", values_to = "response", cols = 1:2)

head(example_experiment)
```

```{r}
example_experiment %>% 
  group_by(group) %>% 
  summarise(
    mean = mean(response),
    sd = sd(response),
    n = n()
  ) %>% 
  ungroup()
```


1.  `group_by()` selects which columns you want to summarise from.
    In this case you just want each group to be a group.
2.  `summarise()` allows you to call the statistical functions, such as mean, sd, and n.
3.  It's a good practice to `ungroup()` after you are done.
    Most of the time it doesn't matter, but it will create problems if you don't and you try to switch/modify grouping later.

Now you just get 2 rows and 4 columns, each row is a group. 
Say I would like to average the counts for each group and for each location. Can I do that?

```{r}
example_experiment %>% 
  group_by(group, location) %>% 
  summarise(
    mean = mean(response),
    sd = sd(response),
    n = n()
  ) %>% 
  ungroup()
```

1.  This time you need to select group and treatment in `group_by()`.
    Because you want the mean for each group and each location.
2.  In `summarise()` you call the mean function.
3.  It's a good practice to `ungroup()` after you are done.

Now each group by treatment combination is a row.

# Basic operations to multiple data frames.

In data analyses, the data might not be all in one file.
It is common that the data of interest were recorded in multiple spreadsheets.
This is extremely common when you are using data from multiple publications.
Each publication may only have one aspect of the data you are interested in.
After you download their tables, it's on you to integrate them into one workable data frame in R.

## Binding

When you need to add more rows (observations) into a data frame, you
need to bind them as rows.
Let's use our hypothetical example again

```{r}
example_data_tidy
```

Let's say you are also interested in gene4 and gene5.
And say the expression of gene4 and gene5 were studied in another experiment.

```{r}
# ignore this chunk. It simulates the data 
example_data_tidy_2 <- data.frame(
  "sample1" = c(4, 5),
  "sample2" = c(2, 2),
  "sample3" = c(5, 4)
) %>% 
  mutate(gene = c("gene4", "gene5")) %>%
  gather("sample", "expression", c(sample1, sample2, sample3))

example_data_tidy_2
```

What you'll do is simply bind them as rows using the `rbind()` command.
"rbind" stands for "row bind".

```{r}
example_data_tidy_3 <- rbind(
  example_data_tidy,
  example_data_tidy_2
)

example_data_tidy_3
```

Now they are in one table. When you do `rbind()`, just make sure they have the same columns.
But other than that, its pretty simple.

## Joining

More often you'll need to add different columns from another data frame.
The jargon for that is joining. 
This sounds abstract, so let's look at an example.

```{r}
child_mortality <- read_csv("../Data/child_mortality_0_5_year_olds_dying_per_1000_born.csv", col_types = cols()) 
babies_per_woman <- read_csv("../Data/children_per_woman_total_fertility.csv", col_types = cols()) 
```

These are two datasets downloaded from the [Gapminder foundation](https://www.gapminder.org/data/).
The Gapminder foundation has datasets on life expectancy, economy, education, and population across countries and years.
The goal is to remind us not only the "gaps" between developed and developing worlds, but also the amazing continuous improvements of quality of life through time.

1.  Child mortality (0 - 5 year old) dying per 1000 born.
2.  Births per woman.

These were recorded from year 1800 and projected all the way to 2100.

Let's look at them.

```{r}
head(child_mortality)
head(babies_per_woman)
```

In each table, you have a column for country.
Then each year is a column, in which the numbers were recorded for the respective tables.

Say you want to know if births per woman is correlated with child mortality.
You may hypothesize that in countries with high child mortality, women give more birth.

To test this hypothesis, you need to first get the two datasets in one data frame.
There are a few commands for joining in R:

1.  `left_join(X, Y)` keeps all the rows in X.
2.  `right_join(X, Y)` keeps all the rows in Y.
3.  `inner_join(X, Y)` only keeps rows that are in common.
4.  `full_join(X, Y)` keeps all rows, filling in `NA` when values are missing from either one.

Usually, `inner_join()` will work well. When you visualize data you filter out `NA` anyways.

So now let's inner_join them. But wait...   
These tables are not in the tidy format!
The year values are spread out across many columns, so you need to pivot them to "long" or tidy format first.

```{r}
babies_per_woman_tidy <- babies_per_woman %>% 
  pivot_longer(names_to = "year", values_to = "birth", cols = c(2:302)) 

head(babies_per_woman_tidy)

child_mortality_tidy <- child_mortality %>% 
  pivot_longer(names_to = "year", values_to = "death_per_1000_born", cols = c(2:302)) 

head(child_mortality_tidy)
```

Now each table has a year column, a value column (either birth or death).
The `c(2:302)` argument in `pivot_longer()` specifies gathering data from 2nd through the 302nd column.
Now we can join them.

We will use `X %>% inner_join(Y, by = common columns)` syntax.
In this case we have two common columns, country and year.
This is asking R to match the countries and years.

```{r}
birth_and_mortality <- babies_per_woman_tidy %>% 
  inner_join(child_mortality_tidy, by = c("country", "year"))

head(birth_and_mortality)
```

So now you have a table with 4 variables: countries, year, birth per woman and death per 1000 born.
Now you have all the data together.

To quickly test our hypothesis that higher morality is correlated with more birth per woman, we can plot it. 
There are too much data to look at, but let's just pull out the year 1945, when WWII ended.
We will talk about how to use ggplot later, so you can ignore this chunk for now.

```{r}
birth_and_mortality %>% 
  filter(year == 1945) %>% 
  ggplot(aes(x = birth, y = death_per_1000_born)) +
  geom_point(alpha = 0.8) +
  geom_smooth(method = "lm", se = F) +
  labs(x = "No. children per woman",
       y = "Child mortality/1000 born",
       title = "Year 1945") +
  theme_classic()

ggsave("../Results/02_scatter_plot.png", width = 2.5, height = 2.5)
```

![scatter plot](https://github.com/cxli233/Quick_data_vis/blob/main/Results/02_scatter_plot.png) 

You do see a upward trend, supporting our hypothesis.
In countries with higher child mortality, women also tended to give more births.

# Exercise

You have learned data arrangement! Let's do an exercise to practice what
you have learned today. 
As the example, this time we will use income per person dataset from Gapminder foundation.

```{r}
income <- read_csv("../Data/income_per_person_gdppercapita_ppp_inflation_adjusted.csv", col_types = cols()) 
head(income)
```

## Tidy data

Is this a tidy data frame?
Make it a tidy data frame using this code chunk.
Hint: the years are spread out from columns 2 to 242.

```{r}
 
```

## Joining data

Combine the income data with birth per woman and child mortality data using this code chunk.
Name the new data frame "birth_and_mortality_and_income".

```{r}
 
```

## Filtering data

Filter out the data for Bangladesh and Sweden, in years 1945 (when WWII ended) and 2010.
Name the new data frame BS_1945_2010.
How has income, birth per woman and child mortality rate changed during this 55-year period?

```{r}
 
```

## Mutate data

Let's say for countries with income between 1000 to 10,000 dollars per year, they are called "fed".
For countries with income above 10,000 dollars per year, they are called "wealthy".
Below 1000, they are called "poor".

Using this info to make a new column called "status".
Hint: you will have to use case_when() and the "&" logic somewhere in this chunk.

```{r}
 
```

## Summarise the data

Let's look at the average child mortality and its sd in year 2010. 
across countries across different status that we just defined. 
Name the new data frame "child_mortality_summmary_2010".

```{r}

```

How does child mortality compare across income group in year 2010?
