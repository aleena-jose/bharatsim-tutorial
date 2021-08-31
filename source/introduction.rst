Introduction
============

To analyse and predict behaviour of complex phenomena such as the spread of COVID-19 through a network of individuals, one is often greatly helped by the ability to create hypothetical scenarios and simulate them. For example, an infectious disease outbreak is heavily influenced by many factors both pertaining to the disease (such as vaccination or intrinsic immunity levels), as well as pertaining to the network on which it spreads (such as the age structure and density of the underlying population). Such heterogeneity often leads to outbreaks of different sizes and severities in two locations that might appear similar on paper. Thus, it is necessary to implement a model that is able to incorporate heterogeneity into the behaviours and interactions at the level of individual agents.

For this reason simply importing models developed for other countries which do not take into account many complexities of the Indian situation -- its unique demographics, the state (or absence) of health care, stratified social structures and complex ecological gradients -- may not be able to achieve the desirable level of predictive ability. The inputs that are required for such modelling must be collated from multiple sources, including imperfect and not easily usable government data, requiring both local knowledge and considerable experience in data analysis and interpretation. The result is a pressing need for an epidemic modelling framework tailored to Indian needs, with potential applications beyond its immediate use for COVID-19 modelling.

Given the flexibility that it aims to achieve, it should not be surprising that building and implementing such agent-based models not an easy task. Good models should ideally enable us to predict the timing of potential surges in health care requirements, making it easier to allocate resources in a timely manner. They should also allow us to compare the merits of different non-pharmaceutical interventions as the epidemic progresses, while helping us understand, say, the role that asymptomatic infections play in the disease spread.

BharatSim is such a framework, helping model developers to build large-scale agent-based models to address the above challenges in an Indian context. BharatSim's vision is to build an India scale agent based framework that would enable modellers from various fields of study -- ranging from epidemiology, and disaster management to economics -- to advise policy makers and other institutions in their decisions. No other framework in India is as generic or scalable in this context.


BharatSim has been developed with three goals in mind:

1. **Flexibility:** Given that it is a social simulation framework, a researcher can develop models across disciplines such as epidemiology, economics, climate science, and so on,

2. **Scalability:** It can keep track of to millions of agents in an efficient manner,

3. **Customisation:** It has been developed to suit India's needs, via its support for synthetic population.


Synthetic Population
====================

A synthetic population is a simplified individual-level representation of the actual population. This means that while every person is represented individually in it, not all of their attributes are included (for example, hair colour or shoe-size are deemed to be irrelevant for modelling epidemic spread, and are thus ignored, while the presence of commodities like diabetes would be included). As such, a synthetic population does not aim to be identical to the actual population, but instead attempts to match its various statistical distributions and correlations, thereby being sufficiently close to the true population to be used in modelling.
