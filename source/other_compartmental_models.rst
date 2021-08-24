.. BharathSim-IISERPune documentation master file, created by
   sphinx-quickstart on Sun Aug 15 12:05:07 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Other Compartmental Models
================================================

SEIR
====

This is a generalisation made on the basic SIR model to include the Exposed state of the individuals. The exposed compartment (E) represents incubation period for the disease, that is a latent phase where the individual is infected but not yet infectious. The infected people can infect the susceptible (S) people who will be moved to the exposed (E) compartment before they are moved to the infected (I) compartment. From the infected compartment they will be removed (R) eventually.  The diagram below shows how the individuals move through each compartment in this model.

.. image:: _static/images/seir_compartments.png

The rate of transmission of the disease from an infected to a susceptible is represented by 
:math:`{\beta}`
. The incubation rate, 
:math:`{\lambda_E}`
, is the rate of latent individuals becoming infectious. The average time an individual spends in the exposed compartment, the incubation period of the disease is thus given by 1/
:math:`{\lambda_E}`
. At last 
:math:`{\lambda_I}`
represents the rate of removal of infected individuals from Infected compartment.

In a closed population with no births or deaths, the SEIR model can be defined using a set of coupled non-linear differential equations described below:

.. math::

   \frac{dS}{dt} = \frac{ -\beta SI }{N}


   \frac{dE}{dt} = \frac{ \beta SI }{N} - \lambda_E E

                                         
   \frac{dI}{dt} = \lambda_E E - \lambda_I I

                               
   \frac{dR}{dt} = \lambda_I I

                                               
where the total population,

.. math::

 N = S + E + I + R

Introducing the incubation period does not change the total number of infections. The incubation period prolongs the duration of the epidemic, but with a short incubation period the peak in the number of infected becomes tall and sharp compared to another model with a longer incubation period. The graphs below show simple SEIR models with incubation periods 5 and 10 days respectively.

.. image:: _static/images/seir2.png 
.. image:: _static/images/seir.png

The above equations can be solved numerically to get deterministic results but, as explained in `Link text <#>` , we can also solve it stochastically using a similar algorithm. 

In the algorithm, if the agent is susceptible, we compute the number of infected individuals they come in contact with who could potentially infect them (I). Then, during each time step δt, they are transferred to the Exposed compartment, with some probability, 

.. math::

 P_{SE} = \frac{\beta I \delta t}{N}

Individuals from the Exposed compartment are transferred to the Infected compartment with the probability,

.. math::

 P_{EI} = \lambda_E \delta t

If the agent is already infected, we transition them to the recovered compartment with a probability

.. math::

 P_{IR} = \lambda_I \delta t.


