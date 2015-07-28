# Data Wrangling



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
3. Each type of observational unit forms a row

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
