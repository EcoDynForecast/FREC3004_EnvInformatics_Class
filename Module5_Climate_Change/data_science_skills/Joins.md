Joins
================
FREC 3004: Environmental Informatics
3/20/2020

First create two data frames: `ecosystem_type_table` and `bird_count`.  
Notice how they share two of the same column names (`siteID`) but the
values in the two columns are not exactly similar. In this example,
`ecosystem_type_table` is a table of ecosystem types from one dataset
and `bird_count` is the number of bird species in a set of plots. Not
all sites with bird species counts were in the set of sites where the
ecosystem type was evaluated.

``` r
ecosystem_type_table <- tibble(siteID = c("MLBS","BART","TOOK"),
            ecosystem_type = c("forest","grassland","grassland"))
ecosystem_type_table
```

    ## # A tibble: 3 x 2
    ##   siteID ecosystem_type
    ##   <chr>  <chr>         
    ## 1 MLBS   forest        
    ## 2 BART   grassland     
    ## 3 TOOK   grassland

``` r
bird_count_table <- tibble(siteID = c("MLBS","MLBS", "BART","TALL"),
             plotID = c("A1", "A2", "A1", "B30"),
            bird_count = c(30,10,20,5))
bird_count_table
```

    ## # A tibble: 4 x 3
    ##   siteID plotID bird_count
    ##   <chr>  <chr>       <dbl>
    ## 1 MLBS   A1             30
    ## 2 MLBS   A2             10
    ## 3 BART   A1             20
    ## 4 TALL   B30             5

The goal of joins is to combine these two table. However, there are
different ways to combined tables based on the desired outcome.

## Left join

Now, you want to join the two columns so that you merge the
`bird_count_table` table into the `ecosystem_type_table` table. The key
is that you want to keep all rows from `ecosystem_type_table` but merge
in rows from `bird_count_table` that match rows from
`ecosystem_type_table` (i.e., the same `siteID`). To do this we use an
left join. The reason “left” is used is becausewe want to keep all rows
in the left table of the function call

``` r
left_join(ecosystem_type_table, bird_count_table, by = c("siteID"))
```

    ## # A tibble: 4 x 4
    ##   siteID ecosystem_type plotID bird_count
    ##   <chr>  <chr>          <chr>       <dbl>
    ## 1 MLBS   forest         A1             30
    ## 2 MLBS   forest         A2             10
    ## 3 BART   grassland      A1             20
    ## 4 TOOK   grassland      <NA>           NA

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

    ## Joining, by = "siteID"

    ## # A tibble: 4 x 4
    ##   siteID ecosystem_type plotID bird_count
    ##   <chr>  <chr>          <chr>       <dbl>
    ## 1 MLBS   forest         A1             30
    ## 2 MLBS   forest         A2             10
    ## 3 BART   grassland      A1             20
    ## 4 TOOK   grassland      <NA>           NA

which is the same as

``` r
left_join(ecosystem_type_table, bird_count_table)
```

    ## Joining, by = "siteID"

    ## # A tibble: 4 x 4
    ##   siteID ecosystem_type plotID bird_count
    ##   <chr>  <chr>          <chr>       <dbl>
    ## 1 MLBS   forest         A1             30
    ## 2 MLBS   forest         A2             10
    ## 3 BART   grassland      A1             20
    ## 4 TOOK   grassland      <NA>           NA

## Right join

The right join is the exact opposite of the left join. Where you want to
keep all rows from the table that is on the right side of the function
call

``` r
right_join(ecosystem_type_table, bird_count_table, by = c("siteID"))
```

    ## # A tibble: 4 x 4
    ##   siteID ecosystem_type plotID bird_count
    ##   <chr>  <chr>          <chr>       <dbl>
    ## 1 MLBS   forest         A1             30
    ## 2 MLBS   forest         A2             10
    ## 3 BART   grassland      A1             20
    ## 4 TALL   <NA>           B30             5

Notice how this has the `siteID = TALL` which is only in the
`bird_count_table` table. If you switch the order of the table you can
get the same answer with a left join.

``` r
left_join(bird_count_table, ecosystem_type_table, by = c("siteID"))
```

    ## # A tibble: 4 x 4
    ##   siteID plotID bird_count ecosystem_type
    ##   <chr>  <chr>       <dbl> <chr>         
    ## 1 MLBS   A1             30 forest        
    ## 2 MLBS   A2             10 forest        
    ## 3 BART   A1             20 grassland     
    ## 4 TALL   B30             5 <NA>

## Inner join

If you want to only include rows that are in both tables, you would use
an inner join. For example, we would use an inner join if we only want
the `SiteID` that are in both the `bird_count_table` and
`ecosystem_type_table` tables. You will see inner joins referred to as
“filtering” joins because the resulting table is a subset of the
original tables.

``` r
inner_join(ecosystem_type_table, bird_count_table, by = c("siteID"))
```

    ## # A tibble: 3 x 4
    ##   siteID ecosystem_type plotID bird_count
    ##   <chr>  <chr>          <chr>       <dbl>
    ## 1 MLBS   forest         A1             30
    ## 2 MLBS   forest         A2             10
    ## 3 BART   grassland      A1             20

## Full join

If you combine all rows from both tables, you would use a full join.
This join will result in the most `NA` values

``` r
full_join(ecosystem_type_table, bird_count_table, by = c("siteID"))
```

    ## # A tibble: 5 x 4
    ##   siteID ecosystem_type plotID bird_count
    ##   <chr>  <chr>          <chr>       <dbl>
    ## 1 MLBS   forest         A1             30
    ## 2 MLBS   forest         A2             10
    ## 3 BART   grassland      A1             20
    ## 4 TOOK   grassland      <NA>           NA
    ## 5 TALL   <NA>           B30             5

## Issues with Joins

There can be issues when joining if you have two tables and they have
columns with the same names but not all columns with the same names are
in the `by =`. The result of the join will add a `.x` and `.y` to the
names of the shared columns that are not in the `by =` argument.
Sometimes this can incorrectly filter or replicate your data. If you see
`.x` and `.y` in your column names, be sure to comfirm that your join
worked correctly.
