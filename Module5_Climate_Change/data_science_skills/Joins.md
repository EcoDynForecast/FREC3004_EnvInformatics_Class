Joins
================
FREC 3004: Environmental Informatics
3/20/2020

First create two data frames: `ecosystem_type_table` and `bird_count`.  
Notice how they share two of the same column names (`siteID` and
`plotID`) but the values in the two columns are not exactly similar. In
this example, `ecosystem_type_table` is a table of field plots from one
dataset and `bird_count` is the number of bird species in a set of
plots. Not all plots with bird species counts were in the set of plots
where the ecosystem type was evaluated.

``` r
ecosystem_type_table <- tibble(siteID = c("MLBS","MLBS","BART","TOOK"),
            plotID = c("A1","A2", "A1", "B10"),
            ecosystem_type = c("forest","forest","grassland","grassland"))
ecosystem_type_table
```

    ## # A tibble: 4 x 3
    ##   siteID plotID ecosystem_type
    ##   <chr>  <chr>  <chr>         
    ## 1 MLBS   A1     forest        
    ## 2 MLBS   A2     forest        
    ## 3 BART   A1     grassland     
    ## 4 TOOK   B10    grassland

``` r
bird_count_table <- tibble(siteID = c("MLBS","BART","TALL"),
             plotID = c("A1", "A1", "B30"),
            bird_count = c(30,20,5))
bird_count_table
```

    ## # A tibble: 3 x 3
    ##   siteID plotID bird_count
    ##   <chr>  <chr>       <dbl>
    ## 1 MLBS   A1             30
    ## 2 BART   A1             20
    ## 3 TALL   B30             5

The goal of joins is to combine these two table. However, there are
different ways to combined tables based on the desired outcome.

## Left join

Now, you want to join the two columns so that you merge the
`bird_count_table` table into the `ecosystem_type_table` table. The key
is that you want to keep all rows from `ecosystem_type_table` but merge
in rows from `bird_count_table` that match rows from
`ecosystem_type_table` (i.e., the same `siteID` and `plotID`). To do
this we use an left join. The reason “left” is used is because we want
to keep all rows in the left table of the function
call

``` r
left_join(ecosystem_type_table, bird_count_table, by = c("siteID","plotID"))
```

    ## # A tibble: 4 x 4
    ##   siteID plotID ecosystem_type bird_count
    ##   <chr>  <chr>  <chr>               <dbl>
    ## 1 MLBS   A1     forest                 30
    ## 2 MLBS   A2     forest                 NA
    ## 3 BART   A1     grassland              20
    ## 4 TOOK   B10    grassland              NA

See how `siteID = TOOK` has an `NA` for `bird_count`. This is because
`TOOK` is not in the `bird_count_table` table and the `bird_count_table`
table was on the *right* side of the function call. Similarly, the
`siteID = TALL` is missing because it is only in the `bird_count_table`
table. Notice how all the rows in the `ecosystem_type_table`.

In the example, you specified `by = c("siteID","plotID")`. If you set
`by = NULL` then it would have automatically found the columns in the
two tables that were the same

``` r
left_join(ecosystem_type_table, bird_count_table, by = NULL)
```

    ## Joining, by = c("siteID", "plotID")

    ## # A tibble: 4 x 4
    ##   siteID plotID ecosystem_type bird_count
    ##   <chr>  <chr>  <chr>               <dbl>
    ## 1 MLBS   A1     forest                 30
    ## 2 MLBS   A2     forest                 NA
    ## 3 BART   A1     grassland              20
    ## 4 TOOK   B10    grassland              NA

which is the same as

``` r
left_join(ecosystem_type_table, bird_count_table)
```

    ## Joining, by = c("siteID", "plotID")

    ## # A tibble: 4 x 4
    ##   siteID plotID ecosystem_type bird_count
    ##   <chr>  <chr>  <chr>               <dbl>
    ## 1 MLBS   A1     forest                 30
    ## 2 MLBS   A2     forest                 NA
    ## 3 BART   A1     grassland              20
    ## 4 TOOK   B10    grassland              NA

## Right join

The right join is the exact opposite of the left join. Where you want to
keep all rows from the table that is on the right side of the function
call

``` r
right_join(ecosystem_type_table, bird_count_table, by = c("siteID","plotID"))
```

    ## # A tibble: 3 x 4
    ##   siteID plotID ecosystem_type bird_count
    ##   <chr>  <chr>  <chr>               <dbl>
    ## 1 MLBS   A1     forest                 30
    ## 2 BART   A1     grassland              20
    ## 3 TALL   B30    <NA>                    5

Notice how this has the `siteID = TALL` which is only in the
`bird_count_table` table. If you switch the order of the table you can
get the same answer with a left
join.

``` r
left_join(bird_count_table, ecosystem_type_table, by = c("siteID","plotID"))
```

    ## # A tibble: 3 x 4
    ##   siteID plotID bird_count ecosystem_type
    ##   <chr>  <chr>       <dbl> <chr>         
    ## 1 MLBS   A1             30 forest        
    ## 2 BART   A1             20 grassland     
    ## 3 TALL   B30             5 <NA>

## Inner join

If you want to only include rows that are in both tables, you would use
an inner join. For example, we would use an inner join if we only want
the `SiteID` and `plotID` that are in both the `bird_count_table` and
`ecosystem_type_table` tables. You will see inner joins referred to as
“filtering” joins because the resulting table is a subset of the
original
tables.

``` r
inner_join(ecosystem_type_table, bird_count_table, by = c("siteID","plotID"))
```

    ## # A tibble: 2 x 4
    ##   siteID plotID ecosystem_type bird_count
    ##   <chr>  <chr>  <chr>               <dbl>
    ## 1 MLBS   A1     forest                 30
    ## 2 BART   A1     grassland              20

## Full join

If you combine all rows from both tables, you would use a full join.
This join will result in the most `NA`
values

``` r
full_join(ecosystem_type_table, bird_count_table, by = c("siteID","plotID"))
```

    ## # A tibble: 5 x 4
    ##   siteID plotID ecosystem_type bird_count
    ##   <chr>  <chr>  <chr>               <dbl>
    ## 1 MLBS   A1     forest                 30
    ## 2 MLBS   A2     forest                 NA
    ## 3 BART   A1     grassland              20
    ## 4 TOOK   B10    grassland              NA
    ## 5 TALL   B30    <NA>                    5

## Issues with Joins

There can be challenges with joins. The most important is when you join
two tables and they have columns with the same names but not all columns
with the same names are in the `by =`. For example, we could just join
our tables by `siteID`.

``` r
left_join(ecosystem_type_table, bird_count_table, by = c("siteID"))
```

    ## # A tibble: 4 x 5
    ##   siteID plotID.x ecosystem_type plotID.y bird_count
    ##   <chr>  <chr>    <chr>          <chr>         <dbl>
    ## 1 MLBS   A1       forest         A1               30
    ## 2 MLBS   A2       forest         A1               30
    ## 3 BART   A1       grassland      A1               20
    ## 4 TOOK   B10      grassland      <NA>             NA

This results is two columns for `plotID`: `plotID.x` is from the left
table and `plotID.y` is from the right table. Notice how there are two
rows with the same `bird_count` value (`30`). This result incorrectly
repeated measurements because two different `plotIDs` share the same
`siteID` and we only joined by `siteID`. Be careful about joins and
double check that you results are what you expect - do not assume it
worked correctly.
