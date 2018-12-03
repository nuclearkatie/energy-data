Nuclear generated what percent of clean energy in 2017?
================
Katie Mummah
12/2/2018

# Data from U.S. EIA

<https://www.eia.gov/electricity/data.php>

Choose “Generation and Thermal Output”, then under “Detailed preliminary
EIA-923 monthly and annual survey data (back to 1990)”, and “State-level
generation and fuel consumption data”, download “Annual (back to 1990)”

### libraries

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(readxl)
```

# Calculation

## Read data

``` r
annual_state_electricity <- read_excel("electricity_data.xls", skip = 2)
#make as tibble, clean for readability
```

### Clean data

``` r
annual_state_electricity <- as.tbl(annual_state_electricity)
annual_state_electricity <- annual_state_electricity %>% mutate(Source = recode(Source, 'Wood and Wood Derived Fuels'='Wood Derived Fuels')) %>% mutate(Source = recode(Source, 'Hydroelectric Conventional'='Hydroelectric')) %>% mutate(Source = recode(Source, 'Solar Thermal and Photovoltaic'='Solar Thermal and PV')) 
```

### Only using total electric power industry, in 2017

``` r
total_industry_2017 <- annual_state_electricity %>% filter(Type == "Total Electric Power Industry") %>% filter(Year == 2017) %>%
  filter(Source != "Total")
```

### Calculate clean energy generation summed over all states in 2017

``` r
clean_all_states_2017 <- total_industry_2017 %>% filter(Source %in% c("Geothermal", "Hydroelectric", "Nuclear", "Solar Thermal and PV", "Other Biomass", "Wind", "Wood Derived Fuels" )) %>% summarise(total_clean = sum(Gen))
```

### Calculate nuclear energy summed over all states in 2017

``` r
nuclear_all_states_2017 <- total_industry_2017 %>% filter(Source == "Nuclear") %>% summarise(total_nuclear = sum(Gen))
#percent
nuclear_percent_of_clean_energy <- (nuclear_all_states_2017$total_nuclear / clean_all_states_2017$total_clean) * 100
```

## Answer: Nuclear generated what percent of clean energy in 2017?

``` r
paste0("In 2017, nuclear power generated ", round(nuclear_percent_of_clean_energy,2), "% of the U.S. clean energy")
```

    ## [1] "In 2017, nuclear power generated 53.97% of the U.S. clean energy"
