Scripted simulation runs with `mrgsolve`
========================================

``` r
library(mrgsim)
library(mrgsolve)
library(dplyr)
```

Load a model
------------

``` r
mod <- mread("popex", modlib())

param(mod)
```

    . 
    .  Model parameters (N=4):
    .  name value . name value
    .  TVCL 1     | TVV  24   
    .  TVKA 0.5   | WT   70

Load a scripted simulation run
------------------------------

``` r
run <- load_run("foo.yaml")
```

Simulate
--------

``` r
out <- sim_run(mod,run)
```

``` r
dim(out)
```

    . [1] 109850      5

``` r
out %>%
  group_by(arm) %>% 
  filter(time==4032) %>%
  summarise(Mean = mean(DV), Min = min(DV), Max = max(DV), N=n())
```

    . # A tibble: 3 x 5
    .     arm      Mean        Min      Max     N
    .   <chr>     <dbl>      <dbl>    <dbl> <int>
    . 1  arm1  7.535748 0.02442406 53.22159   250
    . 2  arm2  3.374540 0.10521101 23.11008   250
    . 3  arm3 17.732016 0.68618571 94.66082   150

The simulation run specification
--------------------------------

      name: Simulation 1
      
      endpoints: [DV,CP,RESP]
      
      output_file: saved.RDS
      
      model: irm1
      
      project: /Users/kyleb/Rlibs/mrgsolve/models
      
      covariate:
        WT:   {distribution: rlnorm,  meanlog: 4.3 , sdlog: 0.5, by: ID, lb: 40, ub: 140}
        SEX:  {distribution: rbinomial, p: 0.5}
        AGE1: {distribution: runif, min: 21, max: 42, as: AGE}
        AGE2: {distribution: runif, min: 12, max: 27, as: AGE}
      
      covset:
        cov1: [WT,SEX,AGE1]
        cov2: [WT,SEX,AGE2]
      
      period:
        a: {amt: 100, ii: 24, addl: 83}
        b: {amt: 200, ii: 24, addl: 83}
        c: {amt: 500, ii: 24, addl: 168}
      
      sequence:
        s1: [a, b]
        s2: [a, a]
        s3: [c]
      
      sample:
        des1: {end: 4032, delta: 24, add: [0]}
      
      arm:
        arm1: {nid: 250, sequence: s1, covset: cov1}
        arm2: {nid: 250, sequence: s2, covset: cov2}
        arm3: {nid: 150, sequence: c,  covset: cov1}
      
      objects:
        - "Sigma <- diag(c(1,1))"
        - "mu <- log(c(2,20))"

Run specification for pediatric / adult pk
------------------------------------------

      name: Pediatrics and adults, same dose
      
      endpoints: [DV]
      
      output_file: saved.RDS
      
      model: popex
      
      covariate:
        WT1:   {distribution: rlnorm,  meanlog: 4.3 , sdlog: 0.5, by: ID, lb: 50, ub: 120, as: WT}
        WT2:   {distribution: rlnorm,  meanlog: 3.6 , sdlog: 0.5, by: ID, lb: 25, ub: 80,  as: WT}
      
      covset:
        cov1: [WT1]
        cov2: [WT2]
      
      period:
        dose100: {amt: 100, ii: 24, addl: 168}
      
      sample:
        des1: {end: 4032, delta: 24, add: [0]}
      
      arm:
        adult:     {nid: 250, sequence: dose100, covset: cov1}
        pediatric: {nid: 250, sequence: dose100, covset: cov2}
