---
layout: post
title: "Notes on an interesting morphospace evolution paper"
author: "A. J. Rominger"
date: "10 August 2017"
tags: equilibrium diversification evo-ecology
---

In a recent paper (Cooney et al. 2017), the authors argue that evolution of bill shape in birds confirms the hypothesis that adaptive radiation happens early in a clade's diversification, filling niche space, and eventually leading to a slow down in diversification once the niche space becomes crowded. Their primary result supporting this claim is a re-constructed rapid increase in disparity followed by a slow-down in dispartiy across the tree of birds. This result they compare to null models based on Brownian motion along the given phylogeny, finding that disparity increases monotonically under the null model, in comparison to the sigmoidal pattern observed in the real data.

My question is: could the observed pattern nonetheless result from a process that does not depend on the hypothesis that niche space filling drives diversification slow-downs? One could imagine that this is possible becuase extinct lineages leave no trace of thier morpology and contribution to disparity. So even if the disparity through time plot is different from Brownian motion, could the niche space occupancy still be largely constant through time?

A simulatin proof of concept:

``` r
library(ape)
library(geiger)
library(paleotree)

treFull <- rphylo(200, 1, 0.8, fossils = TRUE)
tre <- drop.fossil(treFull)

ntrait <- 20
s <- as.matrix(exp(-dist(matrix(runif(ntrait*2), ncol = 2))))
diag(s) <- runif(ntrait, 1, 1.5)
traits <- sim.char(tre, s, 1)[, , 1]
traitsPCA <- prcomp(traits)$x[, 1:8]
```

Here's the idea for the simulation:

-   simulate many correlated traits through time
-   look at how making a PCA for only extant taxa vs. extant and extinct taxa influences disparity through time

Cooney, Christopher R, Jen A Bright, Elliot JR Capp, Angela M Chira, Emma C Hughes, Christopher JA Moody, Lara O Nouri, Zoë K Varley, and Gavin H Thomas. 2017. “Mega-Evolutionary Dynamics of the Adaptive Radiation of Birds.” *Nature* 542 (7641). Nature Research: 344–47.
