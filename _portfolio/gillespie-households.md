---
title: "Gillespie Algorithm for Household Epidemics"
excerpt: "Master thesis project: Simulating epidemics with household structure and gamma-distributed infection times."
collection: portfolio
---

This is my **master thesis project** at the University of Turin (M.Sc. Mathematics, Stochastic and Data Science).

The project implements the Gillespie algorithm for simulating epidemics within a household-structured population, using gamma-distributed infection and exposure times instead of the standard exponential assumption. This allows for more realistic modeling of disease progression.

## Overview

The classical Gillespie algorithm assumes exponentially distributed waiting times between events. By introducing gamma-distributed times for infection and exposure durations, the model better captures the observed variability in real-world epidemics.

The simulation includes:

- **Household structure**: Population divided into households with sizes following a binomial distribution.
- **Two transmission rates**: Global transmission (Beta\_1) and within-household transmission (BetaH), reflecting the higher contact rates inside households.
- **Lockdown policy**: Automatic switching between normal (Beta\_1) and lockdown (Beta\_2) transmission rates based on infection thresholds.
- **Gamma-distributed compartments**: Both the exposed (E) and infected (I) stages are subdivided into multiple sub-compartments, converting exponential to gamma-distributed durations.

## Configuration

The simulation is configured through an input file with parameters including:

- Number of households and household size distribution
- Global and household transmission rates (Beta\_1, Beta\_2, BetaH)
- Lockdown activation/relaxation thresholds
- Number of exposed and infected sub-compartments (controlling the shape of the gamma distribution)

## Output

The simulation produces time-series data tracking compartment populations (S, E, I, R) over time, along with lockdown activation timestamps when policies are enabled.

<a href="https://github.com/Claudio-Me/Gillespie_for_Households_gamma_distribution" target="_blank" rel="noopener" class="btn">
  <i class="fab fa-github"></i> View on GitHub
</a>
