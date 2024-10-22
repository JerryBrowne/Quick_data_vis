# Introduction

This is a quick introduction to R! 

As a resource, here's the link to the [base R cheat sheet](https://iqss.github.io/dss-workshops/R/Rintro/base-r-cheat-sheet.pdf)

# R, RStudio, packages and R Markdown
## R packages

A feature about R is R has a lot of packages.
A package is a collection of R functions that other people wrote and published.
We can use pre-existing packages, so that we don't have to re-invent the wheels.
A difference between R and many other coding languages is that in R you learn more about how to use other packages, and less about coding stuff yourself.
This is very advantageous for those who do not have a traditional computer science background.

When you need to install packages, you use the `install.packages()` command.
For example, if you need to install a package called `dplyr`,
which is a package that we'll use.
You will type `install.packages("dplyr")` at the console.

Alternatively, you can click the Package tab at the lower right.
Then you select Install.
In the pop-up window, type in the package you need to install and click install.
We will talk about how to load an installed package later.

## The R Markdown (.Rmd) file

R Markdown is an enhanced R script file.
You will need the `rmarkdowm` package. 
R markdown files are more user-friendly than a common R script.

In a .Rmd file, there are two kinds of text.
One is plain text, which is what you are reading right now.

The other is code chunk, which R will actually run.

To insert a code chunk, you can click the Code drop-down menu from the top.
Then select Insert Chunk.

Alternatively, you can do Ctrl+Alt+I on a PC, or Cmd+Opt+I on a Mac.
When you do that, this will show up:

```{r}

```

The above is a code chunk.
If you click the green triangle at the upper right, it will run the entire chunk.
The advantage of having chunks is that you can run codes chunk-by-chunk, and de-bug chunk by chunk.
This is very user-friendly.
If you want to run a particular line in the chunk, you do Ctrl+Enter on a PC or Cmd+Enter on a Mac.
I think Opt+Enter also works on a Mac.
To run the whole chunk, do Ctrl+Shift+Enter on PC or Cmd+Shift+Enter on a Mac.

When you start a new script, the first thing you want to do is to load your packages.
Say we want to load the dplyr that we just installed, we will do:

```{r}
library(dplyr) 
```

The command is `library()`.
It's not very intuitive, but this is the command to load packages that we already installed.
The dplyr package is very useful in data arrangement, which we will cover in depth in the next lesson.

## Sections in a .Rmd file

You can see the section headers are in blue color.
To make a new section header, you can type `#` followed by a space, and then any text at the first position of any line in the plain text area.
In addition, `##` will be a sub-header, and `###` will be a sub-sub-header, and so on.

You can view all the sections at the file outline.
The shortcut is Ctrl+Shift+O for PC and Cmd+Shift+O for Mac.
You'll see that section titles will show up at the right to the script area.
If you click on any section titles, RStudio will jump to that section.

# Basic syntax in R

## Single value items

In R, there are 3 types of single value items: logical, numeric and character.
For example,

*   TRUE or FALSE are logical
*   1, 2, 3 ... are numeric 
*   a, b, c ... are character

This is pretty self-explanatory.

## Vectors

The term vector in R is a bit different from the traditional sense of vector in math.
It stands for a list of items that are the same type,
i.e., a list that is all numeric, or all character, but not mixed.

To create a vector, you use the `c()` command.
For example, I want to create a vector of 1, 2, 3, the code will be

```{r}
c(1, 2, 3)
```

Now, to save my vector (or anything) as an R item, you will use the `<-` syntax.
This is called the left arrow.
Let's say we are saving this vector of c(1, 2, 3) as an item called `a` then

```{r}
a <- c(1, 2, 3)
```

When you run this, something happens.
If you look at the environment at the upper right, you should see 'a' shows up as a saved item.
Remember to use a different name for different items.
If you save them as the same name, the later item will just overwrite the previous.

Say I want a vector consists of apple, banana, and orange, and save it as an item named `b`, what do I do?

```{r}
b <- c("apple", "banana", "orange")
b
```

Note that apple, banana and orange all have to be in quotes `" "`.
The quotes `" "` specify the terms apple, banana or oranges are characters,
not items in the environment.
Forgetting to use quotes is a very common source of bugs.

### Subsetting vectors

Say if you want to select a member of a vector by position,
you will use the square bracket `[ ]` syntax.
Say I want the 2nd member in vector `a`, the code will be

```{r}
a[2]
```

If I want anything but the 2nd member, it will be

```{r}
a[-2]
```

If I want the 1st and 2nd member, it will be

```{r}
a[c(1, 2)]
```

If I want anything but the 1st and 2nd member, it will be

```{r}
a[-c(1, 2)]
```

You don't have to memorize how to do anything.
If you ever get confused, you can always go look at the base R cheat sheet.

## Matrix

Matrix is rectangular data that is all the same type, e.g., all numeric.
For example, if I want a matrix like this:

| 1 | 2 | 3 | 4 |   
| 1 | 2 | 1 | 2 |   
| 2 | 3 | 2 | 4 |   

What do I do?
An easy way to do is combining different vectors.

```{r}
my_matrix <- rbind(
  c(1, 2, 3, 4),
  c(1, 2, 1, 2),
  c(2, 3, 2, 4)
)

my_matrix
```

The `rbind()` command takes vectors of the same length, and bind them as rows.
The matrix has 3 rows, so I bind 3 vectors together.
(Note: there is a `cbind()` command, which binds vectors as columns.)

### Subsetting a matrix

Now let's talk about selecting members of a matrix.
We will use the `[ ]` syntax again.
Since a matrix has two dimensions, now the `[ ]` takes two inputs, which is [rows, columns]

Say I want the 1st and 2nd rows of this matrix, what do I do?

```{r}
my_matrix[c(1,2), ]
```

In the rows area of the `[ ]`, I put in `c(1,2)`, which selects the 1st and 2nd rows.
I want all the columns, so I left the column area blank.

Now if I want the 2nd and 4th column of this matrix, what do I do?

```{r}
my_matrix[, c(2,4)] 
```

Now I left the rows area blank, and specify `c(2, 4)` in the columns area.

So if I want the 1st and 2nd rows and 2nd and 4th columns, what do I do?

```{r}
my_matrix[c(1,2), c(2,4)]    
```

Pretty straightforward.

## Data frame

The last type of items I will cover today is data frame.
Data frame is the technical jargon for data tables in R.
We will cover data frames more in depth in the next lesson.
Today we will just have a brief introduction. 

Say I have 3 apples, 4 banana and 2 oranges,
and I want to organize this info into a table, and called this table fruits.
What do I do?

```{r}
fruits <- data.frame(
  name = c("apple", "banana", "orange"),
  count = c(3, 4, 2)
)

fruits
```

That will do it.

To make a new table, you can use the `data.frame()` command.
Inside `data.frame()`, you then type out each columns as vectors.
In this example, the columns are name and count.

Notice that in a data frame, all members of a column are the same type.
For example, the count column is all numeric.
This is different from a matrix,
because in a matrix, all members are the same type across rows and columns.
Also notice data frames have column names.
In this case, the column names are name and count.

You can pull out a particular column using its name.
For example, say I want to pull out the count column, what do I do?

You will use the `$` syntax.

```{r}
fruits$count
```

If I want the name column, it would be `fruits$name` instead.

# Operations

Now let's go over some basic operations.
Say I want to add 0.5 to each member of my matrix,
then take the log2 of each member then take the square root, what do I do?

```{r}
sqrt(log2(my_matrix + 0.5))
```

See, nothing complicated, just an interactive calculator.
You see how the parentheses of `log2()` and `sqrt()` are nested.
You can imagine if you do this for many steps, there will be too many parentheses to keep track of.
To avoid the too many nested parentheses problem, you can use the pipe syntax.

The pipe is `%>%`
The pipe belongs to the dplyr package,
so if you want to use the pipe, you have to load the dplyr package first.
We already did that earlier when we did `library(dplyr) `.

The shortcut is Ctrl+Shift+M on a PC and Cmd+Shift+M on a Mac.
I think Ctrl+Shift+M also works on a Mac.
When you use the pipe, it takes the output of the previous line and feed it as the input for the next line.
In our example, it should look like:

```{r}
(my_matrix + 0.5) %>% 
  log2() %>% 
  sqrt() 
```

See how this is much clearer and easier to read than nested parentheses?
It's a good practice to start a new line after `%>%`, which makes your code easier to read.
`%>%` is a good trick.
Missing parentheses is another common source of bugs, and `%>%` really reduces that.

## add comments or skip lines in the code chunk

Sometimes it's nice to annotate your code in-line as well.
The way to do it is using the `#` symbol in the code chunk.
Any text on the same line after a `#` sign will be ignore in a code chunk by R.
For example:

```{r}
(my_matrix + 0.5) %>% #add 0.5 to every value first
  log2() %>% #then take log2()
  sqrt() #lastly take square root 
```

If you add the `#` sign at the very start of the line, it will skip that entire line.

```{r}
c(4, 4, 4) %>% 
  # log2() %>%
  # mean() %>% 
  sqrt()
```

This is helpful when you realize you want to skip a step (or steps),
but you don't want to remove the code.
You can simply add a `#` sign to the start of the lines that you want to skip, and R will ignore them.
The shortcut is Ctrl+Shift+C on a PC and Cmd+Shift+C on a Mac.

# Excerice

Today you have learned some basic syntax of R.
Now it's time for you to practice.

## Q1:

1.  Insert a new code chunk
2.  Make this matrix

| 1  | 1 | 2 | 2 |   
| 2  | 2 | 1 | 2 |   
| 2  | 3 | 3 | 4 |   
| 1  | 2 | 3 | 4 |   

and save it as an item called `my_mat2`.

3. Select the 1st and 3rd rows and the 1st, 2nd and 4th columns, and save it as an item.

4. Take the square root for each member of my_mat2, then take log2(), and lastly find the maximum value.
Use the pipe syntax. The command for maximum is `max()`.

## Q2:

1.  Use the following info to make a data frame and save it as an item called "grade".
    Adel got 85 on the exam, Bren got 83, and Cecil got 93.
    Their letter grades are B, B, and A, respectively.
    (Hint: How many columns do you have to have?)

2. Pull out the column with the scores.
    Use the `$` syntax.
