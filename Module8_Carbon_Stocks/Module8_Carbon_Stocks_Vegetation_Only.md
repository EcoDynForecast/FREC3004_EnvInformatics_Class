Module 9 Carbon Stocks
================
FREC 3004: Environmental Informatics

``` r
library(tidyverse)
library(lubridate)
library(neonUtilities)
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
  - creating simple functions

## R knowledge covered in module

  - NEON API (zipsByProduct() and stackByTable())

## Step 1: Background on carbon and climate

Estimating carbon storage is important for quantifying the climate
mitigation benefit provided by an ecosystem. Carbon that is stored in an
ecosystem is carbon that is not in the atmosphere and contributing to
climate change. Carbon storage is quantified by adding up the carbon in
different “stocks” of carbon within the ecosystem, with vegetation and
soils being the most important stocks in most terrestrial ecosystems.

Importantly, this is a different perspective than Module 6 where you
analyzed the flux of carbon into or out of an ecosystem (NEP). The
carbon flux is the *rate* that carbon is stored while the carbon stocks
are the amount of carbon that is already present. As an example, a young
forest may have low carbon stocks but high NEP while an older forest may
have high carbon stocks but low NEP.

## Step 2: Background on estimating carbon stocks

**Calculating total biomass of trees**

One common approach to estimating carbon in woody vegetation is by
measuring the diameter of each individual tree over a certain size in a
set area (called a plot). The diameter of each tree is converted to
biomass using allometric relationships between the diameter and biomass.
Allometric relationships are statistical relationships that are created
by measuring a tree’s diameter before cutting down and weighing it. A
general relationship for a species can be created by combining these
harvested trees into a single analysis. Allometric relationships
typically estimate aboveground biomass which needs to be converted to
carbon by multiplying by 0.5 (biomass is about 50% carbon).

Total biomass of a tree is the sum of aboveground and belowground
carbon. Since belowground carbon is less commonly measured (it requires
digging up the tree roots), we can use ratios of aboveground to
belowground carbon from a limited set of trees where the entire tree was
extracted to calculate total tree tree carbon.

Once total tree carbon is calculated for each tree (i.e., units of kg C
per tree) in a plot, the density of carbon for each plot (i.e., kg C per
m2) is calculated by summing across the individual trees and dividing
the plot sum by the plot area.

## Step 3: Your charge\!

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

Your charge is to:

1.  Write down and add your hypothesized answer to the following
    questions. Explore the descriptions of the NEON sites at
    neonscience.org, how the sites look on Google Maps, and your general
    knowledge about ecosystem science to develop your hypotheses. You
    will write your rank for each question.
    
      - What do you think are the patterns in vegetation carbon stocks
        between the four sites that I provide you? Based on information
        about location and ecosystem type, record what you think the
        order of the sites is - with a rank of 1 being the most carbon.

2.  Write the code to calculate the in living trees

3.  Create a knitted R markdown document that reports the amount of
    carbon in live trees. The document should include the following:
    
      - A figure showing the live tree stocks in each tower plot in each
        year with measurements for each site. Each site will be a
        different panel.
      - A bar chart showing the mean stocks among the four sites.
      - A table with summarized values for the site level vegetation
        carbon stocks.

4.  Evaluate whether the your data analysis supported your hypothesis
    from \#1

**Important note:**

## Step 4: Examine data products

NEON data is organized by data product ID in the NEON Date Portal:
<https://data.neonscience.org/static/browse.html>

You will be using the following data product:

  - DP1.10098.001 (Woody plant vegetation structure)

For each data product summarize the following based on the documents
available in “View Product Details” link associated with each data
product. Think about the answers to the following questions.

  - How is the plot sampling done for each data product?
  - What variables do you need from each data product to calculate the
    carbon stocks in vegetation at the site?
  - What measurements are done that will allow for your to calculate
    vegetation stocks - refer to Step 2 and the presentation.
  - How is the data in each product organized?

## Step 5: Plan analysis

Using the description about how to calculate the vegetation carbon
stocks above, create a bulleted list your plan for calculating tree
carbon stocks at the sites.

## Step 6: Download data

First, define the site ID

``` r
site <- "ORNL"
```

and the data product ID

``` r
data_product <- "DP1.10098.001"
```

Second, use the `zipsByProduct()` function to download files via NEON’s
API

``` r
zipsByProduct(dpID=data_product, site=site, 
                package="basic", check.size=F,
                savepath = NA)
```

Third, since NEON data is organized by month and each monthly data
package as multiple data tables, NEON has created a function to combined
tables across months. Use this function `stackByTable()` to create
tables with the full time series

``` r
stackByTable(filepath= "filesToStack10098/",
             folder=T)
```

Look in the `filesToStack10098` folder for the files. There may be
multiple csv files, xml, and README files. You will need to open the
csvs and consult the NEON documentation to understand what variables are
in what tables.

## Step 7: Calculate carbon in live trees

This step will challenge you to develop a workflow using the data
science skills to calculate the carbon stocks in live trees at your
site. In the end you should have a *site-level* mean carbon stock in
*live trees* for each year that was sampled. Your estimate will be from
the plots that are sampling the ecosystem under the flux tower - called
“tower plots”. See
<https://www.neonscience.org/field-sites/field-sites-map/HARV> for an
example map of a plot with the tower plots labeled.

Hints for calculating carbon in live trees:

  - Remember that individual trees are within plots that have a set
    area.  
    Individual trees and plots are remeasured through time.
  - Use `filter(str_detect(plantStatus,"Live")` to select for the live
    trees.  
    The `str_detect()` function will find the rows with “Live” anywhere
    in variable character string. This is necessary because there are
    multiple types of live trees.
  - Remember that only 50% of biomass is carbon so you will need to
    convert from biomass to carbon
  - Assume that belowground biomass (i.e., roots) is 30% of aboveground
    biomass
  - Be very careful with the units at each step: the final units should
    be kgC m^-2 (Kilogram of carbon per meter squared). The order of
    magnitude should be 1 - 100.  
  - The information that you need is found across multiple tables.
    Joining tables can be tricky because the plot table has repeating
    plotIDs, resulting in duplicated values when you join. The function
    `distinct(plotID, .keep_all = TRUE)` can be used to select each
    plotID only once.
  - The Climate Action Reserve project (an official carbon accounting
    organization for the California Carbon Exchange) provides allometric
    relationships to use. The allometric equations for each species can
    be found in the file “Allometrics.csv” on modules. Places this file
    in you data sub-directory. You will need to join the parameters in
    “Allometrics.csv”" to your data frame with the diameter
    measurements. If a species is not in the list of allometric
    relationships it should have an NA when you join, just pick one set
    of allometric parameters to to use and assign it to all species with
    NAs (these are very uncommon species to it won’t influence your
    answer much). The parameters we use come from Table 4 in the Jenkins
    et al. 2003 paper on Canvas.
  - The site level value is the average across plots. Don’t forget that
    we are only interested in the “Tower” plots.
  - Remember that each plot represents an area of forest where all the
    trees are measured.  
  - All values of carbon stocks should be in kg C per m2. You final
    vegetation stock numbers for the site should be an average across
    all ‘tower’ plots.

Finally, remember to review the description in Step 2 about how to
calculate carbon stocks.

## Step 8: Create report

Again, I am looking for:

  - A figure showing the live tree stocks in each tower plot in each
    year with measurements
  - A figure showing the dead tree stocks in each tower plot in each
    year with measurements
  - A bar chart showing the carbon in the three carbon components
  - Summarized table of values for the site level carbon stocks in the
    three carbon components.
  - Text describing whether your data analysis supported your hypothesis
    from \#1 and, if not, possible reasons why it did not.

Be sure your figures are complete with units, labels, and a title. Be
sure your figures are legible when knitted.