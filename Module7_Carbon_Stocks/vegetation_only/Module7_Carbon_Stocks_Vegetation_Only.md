Module 7 Carbon Stocks
================
FREC 3004: Environmental Informatics

``` r
library(tidyverse)
library(lubridate)
library(neonUtilities)
library(knitr)
```

## Science question

How much carbon is stored in a forest ecosystem and where in the
ecosystem is it stored?

## Environmental Learning objectives

  - Describe how vegetation carbon stocks vary between different
    ecosystem types
  - Calculate carbon in trees from individual tree measurements of size
  - Scale carbon estimates from the individual tree to the plot (i.e., a
    set area)

## Data Science Learning objectives

  - Developing a data science analysis plan
  - Understands unfamiliar data tables and how they are linked.
  - Applying prior data science skills to execute an analysis plan
  - Generating an analysis that uses multiple tables of data
  - Downloading and processing data from the National Ecological
    Observatory Network

## Assumes knowledge of the following in R:

  - mutate()
  - group\_by()
  - ggplot()
  - geom\_line()
  - geom\_point()
  - select()
  - summarize()
  - mean()
  - filter()
  - rename()
  - read\_csv()
  - as.numeric()
  - lm()
  - rbind()
  - geom\_abline()
  - geom\_smooth()
  - geom\_histogram()
  - geom\_bar()
  - head()
  - read\_delim()
  - rename()
  - slice()
  - as.numeric()
  - paste0()
  - for loop
  - if-else statement
  - ifelse()
  - vector()
  - geom\_hline()
  - joins
  - str\_sub()
  - add\_predictions()
  - add\_residuals()
  - make\_datetime()
  - separate()
  - Creating simple functions

## R knowledge covered in module

  - NEON specific functions (stackByTable())
  - distinct()
  - kable()

## Background on carbon and climate

Estimating carbon storage is important for quantifying the climate
mitigation benefit provided by an ecosystem. Carbon that is stored in an
ecosystem is carbon that is not in the atmosphere and thus not
contributing to climate change. Carbon storage is quantified by adding
up the carbon in different “stocks” of carbon within the ecosystem, with
vegetation and soils being the most important stocks in most terrestrial
ecosystems.

Importantly, this is a different perspective than Module 6 where you
analyzed the flux of carbon into or out of an ecosystem (NEP). The
carbon flux is the *rate* that carbon is stored while the carbon stocks
are the amount of carbon that is already present. As an example, a young
forest may have low carbon stocks but high NEP (Net Ecosystem Exchange
from Module 6) while an older forest may have high carbon stocks but low
NEP.

## Background on estimating carbon stocks

One common approach to estimating carbon in woody vegetation is by
measuring the diameter of each individual tree over a certain size in a
set area (called a plot). The diameter of each tree is converted to mass
(called biomass) using equations that use diameter to predict biomass
(called allometric relationships). Allometric relationships are
statistical relationships that are created by measuring a tree’s
diameter before cutting down and weighing it. A general relationship for
a species can be created by combining these harvested trees into a
single analysis. Allometric relationships typically estimate aboveground
biomass which needs to be converted to carbon by multiplying by 0.5
(biomass is about 50% carbon).

Total biomass of a tree is the sum of aboveground and belowground
carbon. Since belowground carbon is less commonly measured (it requires
digging up the tree roots), we can use ratios of aboveground to
belowground carbon from a limited set of trees where the entire tree was
extracted to calculate total tree tree carbon.

Once total tree carbon is calculated for each tree (i.e., units of kg C
per tree) in a plot, the density of carbon for each plot (i.e., kg C per
m2) is calculated by summing across the individual trees and dividing
the plot sum by the plot area.

## Your charge\!

I am a client interested in the carbon storage in vegetation of
different ecosystems across the continental U.S. to guide my investment
in the California Carbon Market.

I need carbon stocks using measurements for the following sites within
the National Ecological Observatory Network. In particular, I am
interested in the following sites:

  - Blandy Experimental Farm (BLAN)
  - Bartlett Experimental Forest (BART)
  - Mountain Lake Biological Station (MLBS)
  - Ordway-Swisher Biological Station (OSBS)

### Part 1: Develop hypotheses

Explore the descriptions of the NEON sites at
<https://www.neonscience.org/field-sites/field-sites-map/list>, how the
sites look on the satellite image in the site description, and your
general knowledge about ecosystem science to develop your hypothesized
for the following question:

  - Based on information about location and ecosystem type, rank the
    sites in order of 1 = most vegetation carbon, 4 = least vegetation
    carbon

  - Provide justification for your ranking.

### Part 2: Download and Merge data products

Examine the NEON data product

NEON data is organized by data product ID in the NEON Data Portal:
<https://data.neonscience.org/data-products/explore>

You will be using the following data product:

  - DP1.10098.001 (Woody plant vegetation structure)

To download the data from NEON, use the following steps

  - Go to the NEON data portal: <https://data.neonscience.org/home>
  - In “Get Started..” type: Woody plant vegetation structure
  - Scroll to “Woody plant vegetation structure”. It has the Data
    Product number: DP1.10098.001
  - Click Download Data
  - Select the four sites above
  - Select “All years” then click “NEXT”
  - Click “Include” for “Do you want to include documentation?” “NEXT”

**Answer the following question:** Are using the data in a way that
meets NEON Data Usage and Citation Policies?

  - If so, Check Yes and then NEXT
  - Click “DOWNLOAD DATA”

The files that you downloaded have the data spread across multiple files
for different months at each site. To combine all the sites and months
into a single file use the following code:

``` r
stackByTable(filepath= "data_raw/NEON_struct-woody-plant/",
             folder=T)
```

Confirm that a set of files are in a new folder:

`data_raw/NEON_struct-woody-plant/stackedFiles`

There will be multiple csv files and a README files. You will need to
open the csvs and consult the NEON documentation to understand what
variables are in what tables.

After you have confirmed that the files are in the folder, set the `eval
= FALSE` in the chunk’

## Part 3: Examine data

Based on the important on the data portal for the data product and the
`variables.csv` file in your
`data_raw/NEON_struct-woody-plant/stackedFiles`:

**Answer the following questions:**

1)  How are the following columns in the data product related?

<!-- end list -->

  - siteID:
  - plotID:
  - individualID:
  - eventID:
  - date:

<!-- end list -->

2)  What information to you need to calculate live vegetation stocks at
    the individual tree, plot, and site level - refer to **Background on
    estimating carbon stocks** and the Science Introduction
    presentation.

<!-- end list -->

  - individual tree:
  - plot:
  - site:

<!-- end list -->

3)  Based you answer to \#2, what are the variable names in the data
    product have the information that you need?

4)  Based you answer to \#3, what tables are those variables names
    found?

## Part 4: Plan analysis

Using the description about how to calculate the vegetation carbon
stocks above, create a bullet list that generally outlines your plan for
calculating tree carbon stocks for each of the four sites.

  - Step 1:
  - Step 2:
  - Step …

## Part 5: Calculate carbon in live trees

This step will challenge you to develop a workflow using the data
science skills to calculate the carbon stocks in live trees at each of
the four sites. For each site, you should have a **site-level** mean
carbon stock in **live trees** for **each year** with measurements. Your
estimate will be from the plots that are sampling the ecosystem under
the flux tower - called **tower** plots. See
<https://www.neonscience.org/field-sites/field-sites-map/BART> for an
example map of a plot with the tower plots labeled (Tower Base Plot)

Hints for calculating carbon in live trees:

  - The `plantStatus` column has whether the tree was live at the time
    of measurement, but there are multiple types of live trees. Be sure
    your analysis includes all the types of live trees. You may need to
    use string manipulation and filter functions in the `stringr`
    package.
  - You will need to use function `distinct(plotID, .keep_all = TRUE)`
    on the plot table to only use each plotID once. `distinct()` is
    similar to `unique()` and will find the unique plotIDs. .keep\_all =
    TRUE will keep the other data associated with each plotID.
  - Remember that only 50% of biomass is carbon so you will need to
    convert from biomass to carbon
  - Assume that belowground biomass (i.e., roots) is 30% of aboveground
    biomass
  - Be very careful with the units at each step: the final units should
    be kgC m^-2 (Kilogram of carbon per meter squared). The order of
    magnitude should be 1 - 100.  
  - The Climate Action Reserve project (an official carbon accounting
    organization for the California Carbon Exchange) provides allometric
    relationships to use. The allometric equations for each species can
    be found in the file “Allometrics.csv” on modules. Places this file
    in you `data_raw` directory. You will need to **join** the
    parameters (B0 and B1) in “Allometrics.csv” to your data frame with
    the diameter measurements. If a species is not in the list of
    allometric relationships it should have an NA when you join, just
    pick one set of allometric parameters to to use and assign it to all
    species with NAs (these are very uncommon species to it won’t
    influence your answer much). The parameters we use come from Table 4
    in the Jenkins et al. 2003 paper on Canvas.
  - The equation for converting diameter to biomass is at the bottom of
    Table 4 in Jenkins et al. 2003. The equation uses B0 and B1 from
    “Allometrics.csv”.
  - The **site level** value in each year is the mean of the plots at
    the site. Don’t forget that we are only interested in the “Tower”
    plots.

Remember to review the description in **Background on estimating carbon
stocks** about how to calculate carbon stocks.

``` r
#INSERT CODE
```

## Part 6: Create report

I am looking for the following plots, tables, and text in the Rmarkdown
document:

  - A figure showing the mean live tree carbon stocks in each site and
    year

<!-- end list -->

``` r
#INSERT CODE
```

  - Summarized table of values for the site level carbon stocks averaged
    across year (use the `kable()` function to generate a clean looking
    table).

<!-- end list -->

``` r
#INSERT CODE
```

  - Text describing issues that you had with your analysis, concerns you
    have with your analysis, and concerns you have with the data.

  - Text describing whether your data analysis supported your hypothesis
    from Part \#1 and, if not, possible reasons why it did not.

Be sure your figures are complete with units, labels, and a title. Be
sure your figures and tables are legible when knitted.
