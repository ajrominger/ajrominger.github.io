---
layout: post
title: "Notes from a meeting with Luis Bettencourt"
author: "A. J. Rominger"
date: "19 June 2017"
tags: equilibrium evo-ecology
---

Disconnect between dynamic processes and static theory
------------------------------------------------------

The argument is that processes relevant for the development of each theory occur at different scales of space and time. Thus from the ecological perspective, systems will equilibrate before the non-equilibrial effect of evolution can be detected. Thus it's not suprising that ecological theories have been built on equilibrial assumptions. However, the question remains, when should evolutionary forces be relevant for ecological theory and predictions about communities generally? Answering this question should be seen as a major goal for eco-evo theory. (Note: one thing we know from work by---and inspired by---Ricklefs is that the regional pool, which is an evolutionary outcome, has a strong influence on the potential configuration of local communities. Nonetheless, these local communities could be completely equilibrial w.r.t. their region)

The disconnect between equilibrial theory (e.g. ecological theory) and dynamic processes (e.g. evolution) is not unique to the ecology-evolution realm. Indeed, theories of urbanization, economic markets and ideal statistical mechanics are all based on equilibrium, despite complex dynamics being responsible for their production. When we try to make predictive theories for these systems when far from equilibrium we fail. Thus we're left with the realization that we "don't have good descriptions of dynamics that create complexity" (quote verbatim from Luis...it's a good one).

What constitutes success
------------------------

In ecology we have several theories that predict equilibrium patterns and derive from statistical mechanical type ways of thinking: neutral theory and MaxEnt theories. Statistical mechanical ways of thinking are probably warrented for equilibrial ecology because a pleathora of mechanisms are valid for the assembly and maintenance of ecological communities, yet generality is seen across these communities. One success will be in unifying these theories---i.e., assuming each theory is valid in some limit, what is the universal theory whoes limits we must take in order to arrive at each specific theory. The larger success will be in understanding what dynamics, which brought to stationarity, yield this unified equilibrial theory.

Two approaches to exploring dynamics
------------------------------------

### 1. Assume a given ecological theory is right.

Add dynamics and see what quantities are conserved and figure out how they are conserved---is it the absolute maximum that's conserved or the average; if the average, we're dealing with something like the canonical ensemble.

### 2. What is the simpliest physical systems that evolves?

The answer is effecitively Maxwell's demon. Luis has been working on this in a different context, but the logic applies. Take a device that collects energy but expends energy in the process.
$$
\\frac{\\Delta E}{\\Delta t} = y - c = rE
$$
 where *y* is the process by which energy is gained and *c* is the cost of gaining that energy. The big assumption is that this goes like a rate constant times energy. If that's true, then solving that difference equation leads to
$$
\\frac{1}{t} \\frac{\\text{ln}E(t)}{E\_0} = \\frac{1}{t}\\int\_0^t r(t')dt'
$$
 The right hand side of this equation can be thought of as fitness, and thus scaled into individuals.

Assuming the environment is variable, *r* will be variable, and because we integrate over *r* this leads to lognormal *E*. Thus if *E* is really what fitness is, we might expect lognormal population sizes. Something is most likely amiss here, as we don't often see lognormals, except in transient systems. Zipf behavior is also possible if *r* is presumed to be constant. Work by Felsenstein (1978) uses similar logic and might be worth exploring for parallels, and also to see what SAD is suggested by his approach.

One take-home message is that *E* (or perhaps ln*E* can be expected to be constant in probability, thus *N* can be expected by to be constant in probability and so MaxEnt type approaches may be warrented.

References
----------

Felsenstein, Joseph. 1978. “Macroevolution in a Model Ecosystem.” *The American Naturalist* 112 (983). University of Chicago Press: 177–95.
