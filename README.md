
<!-- README.md is generated from README.Rmd. Please edit that file 
You'll still need to render `README.Rmd` regularly, to keep `README.md` up-to-date. `devtools::build_readme()` is handy for this. 
-->

# assertHE

<!-- badges: start -->

[![R-CMD-check](https://github.com/dark-peak-analytics/assertHE/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/dark-peak-analytics/assertHE/actions/workflows/R-CMD-check.yaml)

<!-- badges: end -->

The goal of assertHE is to identify errors and bugs in health economic
evaluation models early in the development process.

## Installation

You can install the development version of assertHE from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("dark-peak-analytics/assertHE")

library(assertHE)
```

## Example

This is a basic example which shows you how to solve a common problem:

``` r
library(assertHE)

# create a transition probability array
n_t <- 1000 # number of cycles
v_hs_names <- c("H", "S", "D") # health states
n_hs <- length(v_hs_names)

# create array of transition probabilities
a_P <- array(
 data = 0,
 dim = c(n_hs, n_hs, n_t),
 dimnames = list(v_hs_names, v_hs_names, 1:n_t)
)

# fill in transition probabilities for main transitions.
a_P["H", "S",] <- 0.3
a_P["H", "D",] <- 0.01
a_P["S", "D",] <- 0.1
a_P["S", "H",] <- 0.5

# Fill in the proportion remaining in health state in each slice.
# This is the remainder after all other transitions are accounted for.
for(x in 1:n_t){
 diag(a_P[,,x]) <- 1 - rowSums(a_P[,,x])
}

# Use the function from the package.
# This check should return no error, the array is symmetric, numeric, values 
# are between 0 and 1 and all rows sum to 1.
# Note: stop_if_not = F returns warnings, stop_if_not = T returns errors.
check_trans_prob_array(a_P = a_P, 
                       stop_if_not = T)

# We can introduce an error to see the output
# In this case, we set the first 10 cycles of transition from H to S to 0.
# This means that the rows don't sum to 1 for the H row for 1:10 cycle.
a_P["H", "S", 1:10] <- 0

check_trans_prob_array(a_P = a_P, 
                       stop_if_not = F)

# The output looks like this:

# Warning message:
# In check_array_rows_balanced(a_P, stop_if_not = stop_if_not) :
#   Not valid transition probabilities
#    Transition probabilities not valid from Health States:
# 1                                           H; at cycle 1
# 2                                           H; at cycle 2
# 3                                           H; at cycle 3
# 4                                           H; at cycle 4
# 5                                           H; at cycle 5
# 6                                           H; at cycle 6
# 7                                           H; at cycle 7
# 8                                           H; at cycle 8
# 9                                           H; at cycle 9
# 10                                         H; at cycle 10
```

[Robert Smith](https://www.linkedin.com/in/robert-smith-53b28438) <sup>
1,2 </sup>

<sup> 1 </sup> *Public Health Economics and Decision Science, University
of Sheffield, UK* <br> <sup> 2 </sup> *Dark Peak Analytics, Sheffield,
UK*

Contact: <rsmith@darkpeakanalytics.com>

Website: [Dark Peak Analytics](https://www.darkpeakanalytics.com)
