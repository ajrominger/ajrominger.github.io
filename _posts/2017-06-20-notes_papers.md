---
layout: post
title: "Paper notes for today"
author: "A. J. Rominger"
date: "20 June 2017"
tags: hawaii evo-ecology equilibrium diversification
---

### Lim and Marshall (2017)

Using Marshall's earlier model they try to estimate diversity-dependent diversification tracking a changing carrying capacity across the Hawaiian archipelago as the islands change size following their ontogeny. They fit their deterministic model using least squares and compare it to alternatives (logistic growth) using AIC assuming Gaussian residuals. A few questions about this approach: 1. obviously first, what about anthropogenic extinction? This could be especially investigated with birds so figuring out the richness used by Lim and Marshall (2017) (and comparing it to fossil data) would be good 2. the mean of a non-linear stochastic process won't be the same as its deterministic expression, so their curve shouldn't actually be what we observe even if the model is right 3. Gaussian residuals may not be right given that the true process should be a birth-death process with non-linear birth and death terms 4. Should immigration really be rolled into "birth" (i.e. speciation)? Should the same species then be counted independently on each island? Or should immigration be kept separate? 5. How bad is linear birth-death with no carrying capacity? Especially when considering the true process is stochastic? 6. They claim all their models share the same number of params, but how could a model with one carrying capacity, a model with a carrying capacity for each island, and a model with a carrying capacity that changes with area all have the same number of params? 7. If we simulate processes with no carrying capacity or other mechanism, would we still get good fits from their model? In particular, what if immigration (or immigration + priority effects) was primarily responsible for the pattern and their good fit?

### Moen and Wiens (2017)

Estimate diversification in frogs and show that whether or not a clade is arboreal accounts for ≈75% of explained variance. Big questions here: 1. If that's true, what's so special about arboreal habitat? Does escape into novel habitats drive most radiations? Arboreal occupancy evolved repeatedly, did it consistently lead to increased diversification? From Fig. 1 it seems like not. So is their inference more an artifact of their modeling choices? 2. Are diversification estimates robust? Or like other studies (e.g. Getz's bird work) are they just a correlate of clade age? Fig. 1 seems to show that they are. Should look into this "method of moments" estimator (Magallon and Sanderson 2001).

### Cserg et al. (2017)

Find that range models for plants don't predict their growth rates but do correlate with drivers of population persistence. Plants in climates predicted to be less favorable get smaller and show more variable demographic rates

References
==========

Cserg, Anna M, Roberto Salguero-Gómez, Olivier Broennimann, Shaun R Coutts, Antoine Guisan, Amy L Angert, Erik Welk, et al. 2017. “Less Favourable Climates Constrain Demographic Strategies in Plants.” *Ecology Letters*. Wiley Online Library.

Lim, Jun Y, and Charles R Marshall. 2017. “The True Tempo of Evolutionary Radiation and Decline Revealed on the Hawaiian Archipelago.” *Nature* 543 (7647). Nature Research: 710–13.

Magallon, Susana, and Michael J Sanderson. 2001. “Absolute Diversification Rates in Angiosperm Clades.” *Evolution* 55 (9). BioOne: 1762–80.

Moen, Daniel S, and John J Wiens. 2017. “Microhabitat and Climatic Niche Change Explain Patterns of Diversification Among Frog Families.” *The American Naturalist* 190 (1). University of Chicago Press Chicago, IL: 000–000.
