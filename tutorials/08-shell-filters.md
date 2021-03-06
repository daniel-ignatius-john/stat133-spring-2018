Introduction to Unix Filters
================
Gaston Sanchez

> ### Learning Objectives
>
> -   Basic manipulation of data tables with command line
> -   Get to know basic unix filters
> -   Redirect a command's output to a file
> -   Construct command pipelines with two or more stages

------------------------------------------------------------------------

Introduction
------------

Sooner or later you will need to manipulate files from the command line: it could be `Rmd` files, `R` script files, image files, data files, etc. In this tutorial, we'll see new ways to manipulate data table files with shell commands and pipelines.

Crash Example
-------------

Let's get our feet wet with a working example. The first thing you'll need to do is create a directory for this tutorial:

``` bash
mkdir pipes
cd pipes
```

Download the file `nba2017-players.csv` from the course github repository, and then invoke `ls` to check that the data file was successfully downloaded. :

``` bash
curl -O https://raw.githubusercontent.com/ucb-stat133/stat133-spring-2018/master/data/nba2017-players.csv

ls
```

Now that you have a data file, you can apply the basic commands that we've used in lab for inspecting the file's contents. Here's a table with some of those commands, as well as functions in R that have a similar use:

| Command                     | Description                   | R alternative             |
|-----------------------------|-------------------------------|---------------------------|
| `wc nba2017-players.csv`    | count lines, words, and bytes | `object.size()`, `nrow()` |
| `wc -l nba2017-players.csv` | count number of lines         | `nrow()`                  |
| `head nba2017-players.csv`  | inspect first 10 rows         | `head()`                  |
| `tail nba2017-players.csv`  | inspect last 10 rows          | `tail()`                  |
| `less nba2017-players.csv`  | see contents with a paginator | `View()`                  |

In addition to these inspection-oriented commands, you can also use other unix tools to carry out common manipulation of data tables: select rows and columns, sort information, determine frequencies, find unique values, etc.

### Redirecting output to a new file with `>`

For demo purposes, let's subset the data file `nba2017-players.csv` by taking the first 11 rows:

``` bash
head -n 11 nba2017-players.csv
```

    ## "player","team","position","height","weight","age","experience","college","salary","games","minutes","points","points3","points2","points1"
    ## "Al Horford","BOS","C",82,245,30,9,"University of Florida",26540100,68,2193,952,86,293,108
    ## "Amir Johnson","BOS","PF",81,240,29,11,"",1.2e+07,80,1608,520,27,186,67
    ## "Avery Bradley","BOS","SG",74,180,26,6,"University of Texas at Austin",8269663,55,1835,894,108,251,68
    ## "Demetrius Jackson","BOS","PG",73,201,22,0,"University of Notre Dame",1450000,5,17,10,1,2,3
    ## "Gerald Green","BOS","SF",79,205,31,9,"",1410598,47,538,262,39,56,33
    ## "Isaiah Thomas","BOS","PG",69,185,27,5,"University of Washington",6587132,76,2569,2199,245,437,590
    ## "Jae Crowder","BOS","SF",78,235,26,4,"Marquette University",6286408,72,2335,999,157,176,176
    ## "James Young","BOS","SG",78,215,21,2,"University of Kentucky",1825200,29,220,68,12,13,6
    ## "Jaylen Brown","BOS","SF",79,225,20,0,"University of California",4743000,78,1341,515,46,146,85
    ## "Jonas Jerebko","BOS","PF",82,231,29,6,"",5e+06,78,1232,299,45,69,26

Instead of displaying output on the screen, we can use the output redirection operator `>` to a new file:

``` bash
# redirection to new file
head -n 11 nba2017-players.csv > data10.csv
```

You can use `cat` to check that `data10.csv` contains the column names, and the firts 10 lines of data:

``` bash
# display contents on screen
cat data10.csv
```

    ## "player","team","position","height","weight","age","experience","college","salary","games","minutes","points","points3","points2","points1"
    ## "Al Horford","BOS","C",82,245,30,9,"University of Florida",26540100,68,2193,952,86,293,108
    ## "Amir Johnson","BOS","PF",81,240,29,11,"",1.2e+07,80,1608,520,27,186,67
    ## "Avery Bradley","BOS","SG",74,180,26,6,"University of Texas at Austin",8269663,55,1835,894,108,251,68
    ## "Demetrius Jackson","BOS","PG",73,201,22,0,"University of Notre Dame",1450000,5,17,10,1,2,3
    ## "Gerald Green","BOS","SF",79,205,31,9,"",1410598,47,538,262,39,56,33
    ## "Isaiah Thomas","BOS","PG",69,185,27,5,"University of Washington",6587132,76,2569,2199,245,437,590
    ## "Jae Crowder","BOS","SF",78,235,26,4,"Marquette University",6286408,72,2335,999,157,176,176
    ## "James Young","BOS","SG",78,215,21,2,"University of Kentucky",1825200,29,220,68,12,13,6
    ## "Jaylen Brown","BOS","SF",79,225,20,0,"University of California",4743000,78,1341,515,46,146,85
    ## "Jonas Jerebko","BOS","PF",82,231,29,6,"",5e+06,78,1232,299,45,69,26

### Selecting columns with `cut`

How do we select a specific column, say `position`, from `data10.csv`? We can use the `cut` command for this purpose, using the flag `-d ","` to specify the field-delimiter, and the flag `-f 3` to indicate that we want to extract the third column:

``` bash
# select third column
cut -d "," -f 3 data10.csv
```

    ## "position"
    ## "C"
    ## "PF"
    ## "SG"
    ## "PG"
    ## "SF"
    ## "PG"
    ## "SF"
    ## "SG"
    ## "SF"
    ## "PF"

In the same way we created `data10.csv`, we can redirect the output of `cut` to a new file `positions10.txt`

``` bash
# positions (first attempt)
cut -d "," -f 3 data10.csv > positions10.txt

cat positions10.txt
```

    ## "position"
    ## "C"
    ## "PF"
    ## "SG"
    ## "PG"
    ## "SF"
    ## "PG"
    ## "SF"
    ## "SG"
    ## "SF"
    ## "PF"

### Sorting lines with `sort`

Another useful command is `sort`, which as you may guess, allows us to sort the lines of a stream of data:

``` bash
sort positions10.txt
```

    ## "C"
    ## "PF"
    ## "PF"
    ## "PG"
    ## "PG"
    ## "SF"
    ## "SF"
    ## "SF"
    ## "SG"
    ## "SG"
    ## "position"

Notice that the name of the column `position` is also part of the output. But what if we just want to play with the positions values, excluding the column name?

We can use `tail +2` to exclude the first value (i.e. the column name) . To do this with the column of positions, we must use the pipe operator `|` that enables us to take the output of a command and send it as the input of another command:

``` bash
cut -d "," -f 3 data10.csv | tail +2
```

    ## "C"
    ## "PF"
    ## "SG"
    ## "PG"
    ## "SF"
    ## "PG"
    ## "SF"
    ## "SG"
    ## "SF"
    ## "PF"

Now let's mix `|` and `>` to rebuild `positions10.txt` without the column name:

``` bash
# positions (second attempt)
cut -d "," -f 3 data10.csv | tail +2 > positions10.txt

cat positions10.txt
```

    ## "C"
    ## "PF"
    ## "SG"
    ## "PG"
    ## "SF"
    ## "PG"
    ## "SF"
    ## "SG"
    ## "SF"
    ## "PF"

Let's go back to the sorting operation:

``` bash
sort positions10.txt
```

    ## "C"
    ## "PF"
    ## "PF"
    ## "PG"
    ## "PG"
    ## "SF"
    ## "SF"
    ## "SF"
    ## "SG"
    ## "SG"

### Listing unique occurrences with `sort -u`

What if we want to list only the unique values (i.e. the unique categories)? `sort` has the flag `-u` to display only the unique occurrences:

``` bash
sort -u positions10.txt
```

    ## "C"
    ## "PF"
    ## "PG"
    ## "SF"
    ## "SG"

### Counting unique occurrences with `sort` and `uniq`

And what if we want the counts of those unique values (i.e. the frequencies)? To find the answer we pipe the output of `sort` as the input of the command `uniq` with the flag `-c`. Here's the entire pipe:

``` bash
sort positions10.txt | uniq -c
```

    ##    1 "C"
    ##    2 "PF"
    ##    2 "PG"
    ##    3 "SF"
    ##    2 "SG"

Now, let's apply it on the entire data file, step by step:

``` bash
# select column of positions (excluding column name)
cut -d "," -f 3 nba2017-players.csv | tail +2 > positions.txt

# get position frequencies
sort positions.txt | uniq -c
```

    ##   89 "C"
    ##   89 "PF"
    ##   85 "PG"
    ##   83 "SF"
    ##   95 "SG"

### All in one pipeline

Finally, let's pipe all the commands in a single line, without creating the intermediate file `positions10.txt`:

``` bash
# count unique position values, in a single pipe
cut -d "," -f 3 nba2017-players.csv | tail +2 | sort | uniq -c
```

    ##   89 "C"
    ##   89 "PF"
    ##   85 "PG"
    ##   83 "SF"
    ##   95 "SG"

### More examples

What if you want to do the same but now for the teams? In other words, count the number of players in each team?

``` bash
# count unique team values, i.e. number of players
cut -d "," -f 2 nba2017-players.csv | tail +2 | sort | uniq -c
```

    ##   14 "ATL"
    ##   15 "BOS"
    ##   15 "BRK"
    ##   15 "CHI"
    ##   15 "CHO"
    ##   15 "CLE"
    ##   15 "DAL"
    ##   15 "DEN"
    ##   15 "DET"
    ##   15 "GSW"
    ##   14 "HOU"
    ##   14 "IND"
    ##   15 "LAC"
    ##   15 "LAL"
    ##   15 "MEM"
    ##   14 "MIA"
    ##   14 "MIL"
    ##   14 "MIN"
    ##   14 "NOP"
    ##   15 "NYK"
    ##   15 "OKC"
    ##   15 "ORL"
    ##   15 "PHI"
    ##   15 "PHO"
    ##   14 "POR"
    ##   15 "SAC"
    ##   15 "SAS"
    ##   15 "TOR"
    ##   15 "UTA"
    ##   14 "WAS"

Find the minimum age (6th column)

``` bash
# minimum age
cut -d "," -f 6 nba2017-players.csv | tail +2 | sort | head -n 1
```

    ## 19

Find the maximum age (6th column)

``` bash
# maximum age
cut -d "," -f 6 nba2017-players.csv | tail +2 | sort -r | head -n 1
```

    ## 40

Frequencies of ages:

``` bash
# age frequencies
cut -d "," -f 6 nba2017-players.csv | tail +2 | sort | uniq -c
```

    ##    9 19
    ##   20 20
    ##   26 21
    ##   35 22
    ##   42 23
    ##   46 24
    ##   36 25
    ##   34 26
    ##   32 27
    ##   35 28
    ##   24 29
    ##   25 30
    ##   24 31
    ##   16 32
    ##    6 33
    ##    8 34
    ##    6 35
    ##   11 36
    ##    1 37
    ##    1 38
    ##    3 39
    ##    1 40

------------------------------------------------------------------------

Filters
=======

In the above examples we use a set of commands that are formally known as **filters**:

-   `sort`
-   `cut`
-   `uniq`
-   *etc* (there are more filters)

Filters are a particular type of unix program that expects to work either with file redirection or as a part of a pipeline. These programs read input from standard input, write output to standard output, and often don't have any starting arguments.

Extracting columns with `cut`
-----------------------------

When working with files that have a tabular structure (e.g. csv, tsv, field delimited) it is very common to focus on one or more "columns". To pull vertical columns from a file, you can use the `cut` command.

`cut` operates based either on character position within the column when using the `-c` flag, or on delimited fields when using the `-f` flag. By default, `cut` expects tabs as the delimiter. If a file separates fields with spaces or commas or any other delimiter, you need to use the option `-d` indicating the character used as field delimiter between quote marks.

| Option   | Description                                    |
|----------|------------------------------------------------|
| `-f` 1,5 | return columns 1 and 5, delimited by tabs.     |
| `-f` 1,5 | return columns 1 through 5, delimited by tabs. |
| `-d ","` | use commans as the delimiters.                 |
| `-c 2-7` | return characters 2 through 7 from the file.   |

``` bash
# return columns 1 and 3 (tsv file)
cut -f 1,3 data.tsv
```

``` bash
# return columns 2 and 5 (tsv file)
cut -f 2-5 data.tsv
```

``` bash
# return columns 1 and 3 (csv file)
cut -f 1,3 -d "," data.csv
```

``` bash
# return characters 1 through 6 (fixed-width format file)
cut -c 1-6 data.dat
```

------------------------------------------------------------------------

Sorting lines with `sort`
-------------------------

The output stream produced by many commands, as well as the lines of a file or of a series of files, can be sorted into alphabetical order with the `sort` command. In other words, `sort` reads information and sorts it alphabetically. You can customize the behavior of `sort` to ignore the case of words, and to reverse the order of a sort. This command also enables you to sort lists of numbers. The table below shows some of the common options for the `sort` command:

| Option   | Description                                            |
|----------|--------------------------------------------------------|
| `-n`     | sort in numerical order rather than alphabetically.    |
| `-r`     | sort in reverse order, z to a or decreasing numbers.   |
| `-f`     | fold uppercase into lowercase (i.e. ignore case).      |
| `-u`     | return a unique representative of repeated items.      |
| `-k 3`   | sort lines based on column 3 (tab or space delimiters) |
| `-t ","` | use commas for delimiters.                             |
| `-b`     | ignore leading blanks.                                 |
| `-d`     | sort in dictionary order.                              |

------------------------------------------------------------------------

Isolating unique lines with `uniq`
----------------------------------

Another useful command for extracting a subset of values from a file, or summarizing a stream of text, is `uniq`. This command removes consecutive identical lines from a file, leaving one unique representative. More precisely, what `uniq` does is compare each line it reads with the previous line. If the lines are the same, `uniq` does not list the second line. You can use options with `uniq` to get more specific results:

| Option | Description                                        |
|--------|----------------------------------------------------|
| `-c`   | adds a count of how many times each line occurred. |
| `-u`   | lists only lines that are not repeated.            |
| `-d`   | lists only lines that are duplicated.              |
| `-i`   | ignore case when determining uniqueness            |
| `-f 4` | ignore the first 4 fields (space delimiter)        |

To get a single representative of each unique line from the entire file, in most cases you would need to first sort the lines with the `sort` command to group matching lines together. Interestingly, `uniq` can be used with the flag `-c` to count the number of occurrences of a line. This gives a quick way, for example, to assess the frequencies of values in a given column.
