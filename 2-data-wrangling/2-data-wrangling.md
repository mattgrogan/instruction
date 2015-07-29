# Data Wrangling



Prerequisite: please copy the car names into the mtcars dataframe:


```r
> mtcars$model <- rownames(mtcars)
```

## Tidy Data

https://cran.r-project.org/web/packages/tidyr/vignettes/tidy-data.html

> Tidy datasets provide a standardized way to link the structure of a dataset (its physical layout) with its semantics (its meaning).

Hadley Wickham provides a philosophical foundation for managing data in his Tidy data paper. We'll work through examples similar to the data shown in the paper.

### Data Structure

The data are from http://www.pricescope.com/wiki/diamond-grading/appendix-ii


```r
> pricescope_data <- read.delim('pricescope_data.txt', stringsAsFactors = FALSE)
> pricescope_data
  lab  X1  X2  X3  X4  X5   X6  X7  X8  X9  X10 X11 X12 X13 X14  X15 X16
1 GIA SI1 VS2 SI2 SI2 VS1 VVS2 VS1 VS2 SI2  VS1 SI1 SI1 SI1 SI2 VVS2 VS1
2 AGS SI2 VS2 SI2 SI2 VS2 VVS2 VS2 SI1 SI2 <NA> SI2 SI2 SI1 SI1  VS1 VS2
3 EGL SI1 VS2 SI2 SI2 VS1  VS1 VS2 VS2 SI2  VS2 SI1 SI2 SI1 SI1 VVS2 VS2
  X17
1 VS2
2 SI1
3 SI1
```

Another way to represent the same data:


```r
> pricescope_data2 <- read.delim('pricescope_data2.txt', stringsAsFactors = FALSE)
> pricescope_data2
   STONE  AGS  EGL  GIA
1      1  SI2  SI1  SI1
2      2  VS2  VS2  VS2
3      3  SI2  SI2  SI2
4      4  SI2  SI2  SI2
5      5  VS2  VS1  VS1
6      6 VVS2  VS1 VVS2
7      7  VS2  VS2  VS1
8      8  SI1  VS2  VS2
9      9  SI2  SI2  SI2
10    10 <NA>  VS2  VS1
11    11  SI2  SI1  SI1
12    12  SI2  SI2  SI1
13    13  SI1  SI1  SI1
14    14  SI1  SI1  SI2
15    15  VS1 VVS2 VVS2
16    16  VS2  VS2  VS1
17    17  SI1  SI1  VS2
```

In tidy data:

1. Each variable forms a column
2. Each observation forms a row
3. Each type of observational unit forms a table

In relational databases, this is Third Normal Form.


```
   lab stone grade
1  AGS     1   SI2
2  AGS     2   VS2
3  AGS     3   SI2
4  AGS     4   SI2
5  AGS     5   VS2
6  AGS     6  VVS2
7  AGS     7   VS2
8  AGS     8   SI1
9  AGS     9   SI2
10 AGS    10  <NA>
11 AGS    11   SI2
12 AGS    12   SI2
13 AGS    13   SI1
14 AGS    14   SI1
15 AGS    15   VS1
16 AGS    16   VS2
17 AGS    17   SI1
18 EGL     1   SI1
19 EGL     2   VS2
20 EGL     3   SI2
21 EGL     4   SI2
22 EGL     5   VS1
23 EGL     6   VS1
24 EGL     7   VS2
25 EGL     8   VS2
26 EGL     9   SI2
27 EGL    10   VS2
28 EGL    11   SI1
29 EGL    12   SI2
30 EGL    13   SI1
31 EGL    14   SI1
32 EGL    15  VVS2
33 EGL    16   VS2
34 EGL    17   SI1
35 GIA     1   SI1
36 GIA     2   VS2
37 GIA     3   SI2
38 GIA     4   SI2
39 GIA     5   VS1
40 GIA     6  VVS2
41 GIA     7   VS1
42 GIA     8   VS2
43 GIA     9   SI2
44 GIA    10   VS1
45 GIA    11   SI1
46 GIA    12   SI1
47 GIA    13   SI1
48 GIA    14   SI2
49 GIA    15  VVS2
50 GIA    16   VS1
51 GIA    17   VS2
```

### Tidying Data when Column Headers are Values, Not Variable Names

Our dataset has three variables: lab, stone and grade. To tidy it, we need to _gather_ the values from the headers.

The `gather` function takes three arguments:

1. The name of the _key_ column. That is, the new name of the column that is currently encoded in the headers.
2. The name  of the _value_ column. That is, the new name of the column that is currently held in the data points.
3. The columns to gather.


```r
> pricescope_tidy_data <- pricescope_data %>%
+   gather(stone, grade, X1:X17)
> pricescope_tidy_data
   lab stone grade
1  GIA    X1   SI1
2  AGS    X1   SI2
3  EGL    X1   SI1
4  GIA    X2   VS2
5  AGS    X2   VS2
6  EGL    X2   VS2
7  GIA    X3   SI2
8  AGS    X3   SI2
9  EGL    X3   SI2
10 GIA    X4   SI2
11 AGS    X4   SI2
12 EGL    X4   SI2
13 GIA    X5   VS1
14 AGS    X5   VS2
15 EGL    X5   VS1
16 GIA    X6  VVS2
17 AGS    X6  VVS2
18 EGL    X6   VS1
19 GIA    X7   VS1
20 AGS    X7   VS2
21 EGL    X7   VS2
22 GIA    X8   VS2
23 AGS    X8   SI1
24 EGL    X8   VS2
25 GIA    X9   SI2
26 AGS    X9   SI2
27 EGL    X9   SI2
28 GIA   X10   VS1
29 AGS   X10  <NA>
30 EGL   X10   VS2
31 GIA   X11   SI1
32 AGS   X11   SI2
33 EGL   X11   SI1
34 GIA   X12   SI1
35 AGS   X12   SI2
36 EGL   X12   SI2
37 GIA   X13   SI1
38 AGS   X13   SI1
39 EGL   X13   SI1
40 GIA   X14   SI2
41 AGS   X14   SI1
42 EGL   X14   SI1
43 GIA   X15  VVS2
44 AGS   X15   VS1
45 EGL   X15  VVS2
46 GIA   X16   VS1
47 AGS   X16   VS2
48 EGL   X16   VS2
49 GIA   X17   VS2
50 AGS   X17   SI1
51 EGL   X17   SI1
```

### The Pipe

The `%>%` pipe helps code readability by piping data from one function to the next.

`f(x, y)` is the same as `x %>% f(y)`

The above code could have been written as:


```r
> gather(pricescope_data, stone, grade, X1:X17)
```

### Spreading Tidy Data

The `tidyr` package makes it easy to pivot data back into wide format.


```r
> morley
    Expt Run Speed
001    1   1   850
002    1   2   740
003    1   3   900
004    1   4  1070
005    1   5   930
006    1   6   850
007    1   7   950
008    1   8   980
009    1   9   980
010    1  10   880
011    1  11  1000
012    1  12   980
013    1  13   930
014    1  14   650
015    1  15   760
016    1  16   810
017    1  17  1000
018    1  18  1000
019    1  19   960
020    1  20   960
021    2   1   960
022    2   2   940
023    2   3   960
024    2   4   940
025    2   5   880
026    2   6   800
027    2   7   850
028    2   8   880
029    2   9   900
030    2  10   840
031    2  11   830
032    2  12   790
033    2  13   810
034    2  14   880
035    2  15   880
036    2  16   830
037    2  17   800
038    2  18   790
039    2  19   760
040    2  20   800
041    3   1   880
042    3   2   880
043    3   3   880
044    3   4   860
045    3   5   720
046    3   6   720
047    3   7   620
048    3   8   860
049    3   9   970
050    3  10   950
051    3  11   880
052    3  12   910
053    3  13   850
054    3  14   870
055    3  15   840
056    3  16   840
057    3  17   850
058    3  18   840
059    3  19   840
060    3  20   840
061    4   1   890
062    4   2   810
063    4   3   810
064    4   4   820
065    4   5   800
066    4   6   770
067    4   7   760
068    4   8   740
069    4   9   750
070    4  10   760
071    4  11   910
072    4  12   920
073    4  13   890
074    4  14   860
075    4  15   880
076    4  16   720
077    4  17   840
078    4  18   850
079    4  19   850
080    4  20   780
081    5   1   890
082    5   2   840
083    5   3   780
084    5   4   810
085    5   5   760
086    5   6   810
087    5   7   790
088    5   8   810
089    5   9   820
090    5  10   850
091    5  11   870
092    5  12   870
093    5  13   810
094    5  14   740
095    5  15   810
096    5  16   940
097    5  17   950
098    5  18   800
099    5  19   810
100    5  20   870
> 
> morley %>%
+   spread(Run, Speed)
  Expt   1   2   3    4   5   6   7   8   9  10   11  12  13  14  15  16
1    1 850 740 900 1070 930 850 950 980 980 880 1000 980 930 650 760 810
2    2 960 940 960  940 880 800 850 880 900 840  830 790 810 880 880 830
3    3 880 880 880  860 720 720 620 860 970 950  880 910 850 870 840 840
4    4 890 810 810  820 800 770 760 740 750 760  910 920 890 860 880 720
5    5 890 840 780  810 760 810 790 810 820 850  870 870 810 740 810 940
    17   18  19  20
1 1000 1000 960 960
2  800  790 760 800
3  850  840 840 840
4  840  850 850 780
5  950  800 810 870
```

## Dplyr

Most of this content is from the Introduction to Dplyr at http://rstudio-pubs-static.s3.amazonaws.com/11068_8bc42d6df61341b2bed45e9a9a3bf9f4.html

> When working with data you must:
>
> - Figure out what you want to do.
> - Precisely describe what you want in the form of a computer program.
> - Execute the code.

> dplyr aims to make each of these steps as fast and easy as possible by:
>
> - Elucidating the most common data manipulation operations, so that your options are helpfully constrained when thinking about how to tackle a problem.
> - Providing simple functions that correspond to the most common data manipulation verbs, so that you can easily translate your thoughts into code.
> - Using efficient data storage backends, so that you spend as little time waiting for the computer as possible.

### Five Basic Verbs

Dplyr provides five basic verbs: `filter()`, `arrange()`, `select()`, `mutate()` and `summarise()`

#### Filter()

Filter rows.


```r
> pricescope_tidy_data %>%
+   filter(lab == 'GIA')
   lab stone grade
1  GIA    X1   SI1
2  GIA    X2   VS2
3  GIA    X3   SI2
4  GIA    X4   SI2
5  GIA    X5   VS1
6  GIA    X6  VVS2
7  GIA    X7   VS1
8  GIA    X8   VS2
9  GIA    X9   SI2
10 GIA   X10   VS1
11 GIA   X11   SI1
12 GIA   X12   SI1
13 GIA   X13   SI1
14 GIA   X14   SI2
15 GIA   X15  VVS2
16 GIA   X16   VS1
17 GIA   X17   VS2
```

> Exercise: Using Dplyr syntax, filter the `mtcars` dataset for cars that achieve 25 mpg or greater.


```r
> mtcars %>%
+   filter(mpg >= 25)
   mpg cyl  disp  hp drat    wt  qsec vs am gear carb          model
1 32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1       Fiat 128
2 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2    Honda Civic
3 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1 Toyota Corolla
4 27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1      Fiat X1-9
5 26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2  Porsche 914-2
6 30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2   Lotus Europa
```

#### Arrange()

Arrange rows in order.


```r
> pricescope_tidy_data %>%
+   arrange(lab, stone)
   lab stone grade
1  AGS    X1   SI2
2  AGS    X2   VS2
3  AGS    X3   SI2
4  AGS    X4   SI2
5  AGS    X5   VS2
6  AGS    X6  VVS2
7  AGS    X7   VS2
8  AGS    X8   SI1
9  AGS    X9   SI2
10 AGS   X10  <NA>
11 AGS   X11   SI2
12 AGS   X12   SI2
13 AGS   X13   SI1
14 AGS   X14   SI1
15 AGS   X15   VS1
16 AGS   X16   VS2
17 AGS   X17   SI1
18 EGL    X1   SI1
19 EGL    X2   VS2
20 EGL    X3   SI2
21 EGL    X4   SI2
22 EGL    X5   VS1
23 EGL    X6   VS1
24 EGL    X7   VS2
25 EGL    X8   VS2
26 EGL    X9   SI2
27 EGL   X10   VS2
28 EGL   X11   SI1
29 EGL   X12   SI2
30 EGL   X13   SI1
31 EGL   X14   SI1
32 EGL   X15  VVS2
33 EGL   X16   VS2
34 EGL   X17   SI1
35 GIA    X1   SI1
36 GIA    X2   VS2
37 GIA    X3   SI2
38 GIA    X4   SI2
39 GIA    X5   VS1
40 GIA    X6  VVS2
41 GIA    X7   VS1
42 GIA    X8   VS2
43 GIA    X9   SI2
44 GIA   X10   VS1
45 GIA   X11   SI1
46 GIA   X12   SI1
47 GIA   X13   SI1
48 GIA   X14   SI2
49 GIA   X15  VVS2
50 GIA   X16   VS1
51 GIA   X17   VS2
```

> Exercise: Order the `mtcars` dataframe by mpg using the `arrange()` function.


```r
> mtcars %>%
+   arrange(mpg)
    mpg cyl  disp  hp drat    wt  qsec vs am gear carb               model
1  10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4  Cadillac Fleetwood
2  10.4   8 460.0 215 3.00 5.424 17.82  0  0    3    4 Lincoln Continental
3  13.3   8 350.0 245 3.73 3.840 15.41  0  0    3    4          Camaro Z28
4  14.3   8 360.0 245 3.21 3.570 15.84  0  0    3    4          Duster 360
5  14.7   8 440.0 230 3.23 5.345 17.42  0  0    3    4   Chrysler Imperial
6  15.0   8 301.0 335 3.54 3.570 14.60  0  1    5    8       Maserati Bora
7  15.2   8 275.8 180 3.07 3.780 18.00  0  0    3    3         Merc 450SLC
8  15.2   8 304.0 150 3.15 3.435 17.30  0  0    3    2         AMC Javelin
9  15.5   8 318.0 150 2.76 3.520 16.87  0  0    3    2    Dodge Challenger
10 15.8   8 351.0 264 4.22 3.170 14.50  0  1    5    4      Ford Pantera L
11 16.4   8 275.8 180 3.07 4.070 17.40  0  0    3    3          Merc 450SE
12 17.3   8 275.8 180 3.07 3.730 17.60  0  0    3    3          Merc 450SL
13 17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4           Merc 280C
14 18.1   6 225.0 105 2.76 3.460 20.22  1  0    3    1             Valiant
15 18.7   8 360.0 175 3.15 3.440 17.02  0  0    3    2   Hornet Sportabout
16 19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4            Merc 280
17 19.2   8 400.0 175 3.08 3.845 17.05  0  0    3    2    Pontiac Firebird
18 19.7   6 145.0 175 3.62 2.770 15.50  0  1    5    6        Ferrari Dino
19 21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4           Mazda RX4
20 21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4       Mazda RX4 Wag
21 21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1      Hornet 4 Drive
22 21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2          Volvo 142E
23 21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1       Toyota Corona
24 22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1          Datsun 710
25 22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2            Merc 230
26 24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2           Merc 240D
27 26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2       Porsche 914-2
28 27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1           Fiat X1-9
29 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2         Honda Civic
30 30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2        Lotus Europa
31 32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1            Fiat 128
32 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1      Toyota Corolla
```

> Exercise: Do the filter (>= 25 mpg) and arrange in one statement


```r
> mtcars %>%
+   filter(mpg >= 25) %>%
+   arrange(mpg)
   mpg cyl  disp  hp drat    wt  qsec vs am gear carb          model
1 26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2  Porsche 914-2
2 27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1      Fiat X1-9
3 30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2    Honda Civic
4 30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2   Lotus Europa
5 32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1       Fiat 128
6 33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1 Toyota Corolla
```

#### Select()

Select columns.


```r
> mtcars %>%
+   filter(mpg >= 25) %>%
+   arrange(mpg) %>%
+   select(model, mpg, cyl, disp)
           model  mpg cyl  disp
1  Porsche 914-2 26.0   4 120.3
2      Fiat X1-9 27.3   4  79.0
3    Honda Civic 30.4   4  75.7
4   Lotus Europa 30.4   4  95.1
5       Fiat 128 32.4   4  78.7
6 Toyota Corolla 33.9   4  71.1
```

#### Mutate()

Add new columns.


```r
> mtcars %>%
+   mutate(rank = dense_rank(desc(mpg))) %>%
+   select(model, mpg, rank)
                 model  mpg rank
1            Mazda RX4 21.0   10
2        Mazda RX4 Wag 21.0   10
3           Datsun 710 22.8    7
4       Hornet 4 Drive 21.4    9
5    Hornet Sportabout 18.7   13
6              Valiant 18.1   14
7           Duster 360 14.3   23
8            Merc 240D 24.4    6
9             Merc 230 22.8    7
10            Merc 280 19.2   12
11           Merc 280C 17.8   15
12          Merc 450SE 16.4   17
13          Merc 450SL 17.3   16
14         Merc 450SLC 15.2   20
15  Cadillac Fleetwood 10.4   25
16 Lincoln Continental 10.4   25
17   Chrysler Imperial 14.7   22
18            Fiat 128 32.4    2
19         Honda Civic 30.4    3
20      Toyota Corolla 33.9    1
21       Toyota Corona 21.5    8
22    Dodge Challenger 15.5   19
23         AMC Javelin 15.2   20
24          Camaro Z28 13.3   24
25    Pontiac Firebird 19.2   12
26           Fiat X1-9 27.3    4
27       Porsche 914-2 26.0    5
28        Lotus Europa 30.4    3
29      Ford Pantera L 15.8   18
30        Ferrari Dino 19.7   11
31       Maserati Bora 15.0   21
32          Volvo 142E 21.4    9
```

#### Summarize()

Collapse dataframe into a single row.


```r
> mtcars %>%
+   summarize(median_mpg = median(mpg))
  median_mpg
1       19.2
```

### Add in some Grouping

In dplyr, you use the group_by() function to describe how to break a dataset down into groups. You can then use the resulting object in the exactly the same functions as above.

What are the mean miles per gallon by cylinder?


```r
> mtcars %>%
+   group_by(cyl) %>%
+   summarize(mean_mpg = mean(mpg))
Source: local data frame [3 x 2]

  cyl mean_mpg
1   4 26.66364
2   6 19.74286
3   8 15.10000
```

> Exercise: Extend the previous statement to find the minimum and maximum mpg for each cylinder group.


```r
> mtcars %>%
+   group_by(cyl) %>%
+   summarize(mean_mpg = mean(mpg),
+             max_mpg = max(mpg),
+             min_mpg = min(mpg))
Source: local data frame [3 x 4]

  cyl mean_mpg max_mpg min_mpg
1   4 26.66364    33.9    21.4
2   6 19.74286    21.4    17.8
3   8 15.10000    19.2    10.4
```

> Exercise: Rank the cars by mpg (with the highest mpg = 1) within each cylinder group. Hint: use `mutate()`. Order the rows by rank and cyl.

Since we are only adding a column, use mutate.

It doesn't work! By default, `dplyr`'s arrange will respect grouping. You must `ungroup()` first.


```r
> mtcars_ranked <- mtcars %>%
+   group_by(cyl) %>%
+   mutate(rank = dense_rank(desc(mpg))) %>%
+   select(model, cyl, mpg, rank) %>%
+   ungroup() %>%
+   arrange(rank, cyl)
> mtcars_ranked
Source: local data frame [32 x 4]

               model cyl  mpg rank
1     Toyota Corolla   4 33.9    1
2     Hornet 4 Drive   6 21.4    1
3   Pontiac Firebird   8 19.2    1
4           Fiat 128   4 32.4    2
5          Mazda RX4   6 21.0    2
6      Mazda RX4 Wag   6 21.0    2
7  Hornet Sportabout   8 18.7    2
8        Honda Civic   4 30.4    3
9       Lotus Europa   4 30.4    3
10      Ferrari Dino   6 19.7    3
..               ... ...  ...  ...
```

### Window Functions

We've only brushed the surface of what is possible. Try the Window functions vignette. 

https://cran.r-project.org/web/packages/dplyr/vignettes/window-functions.html
