
```yaml
name: Simulation 1

endpoints: [DV,CP,RESP]

output_file: saved.RDS

model: irm1

project: /Users/kyleb/Rlibs/mrgsolve/models

covariate:
  WT:   {distribution: rlnorm,  meanlog: 4.3 , sdlog: 0.5, by: ID, lb: 40, ub: 140}
  wt:   {formula: "wt[40,140]~rlnorm(4.3,0.5)|ID"}
  SEX:  {distribution: rbinomial, p: 0.5}
  AGE:  {distribution: runif, min: 21, max: 42}

covset:
  cov1: [WT,SEX,AGE]
  cov2: [SEX,AGE,WT]

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
  a1: {nid: 250, sequence: s1, covset: cov1}
  a2: {nid: 250, sequence: s2, covset: cov2}
  a3: {nid: 150, sequence: c,  covset: cov1}

objects:
  - "Sigma <- diag(c(1,1))"
  - "mu <- log(c(2,20))"
```
