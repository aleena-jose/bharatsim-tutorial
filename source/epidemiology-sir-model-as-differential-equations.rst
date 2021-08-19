SIR Model as Differential Equations
====================================

Epidemiological models are often compartmental models, where each individual in the model(or in our case, agents) move from one compartment to another. One of the simple but effective models include the **SIR Model**. The SIR Model consists of three compartments or states, namely, ``Susceptible``, ``Infected`` and ``Removed``. In the model, the movement of each individual from one compartment to another is governed by some rate, which can be expressed as differential equations, explained later in this document. But first, how does the SIR Model work?

The SIR Model
--------------
In the SIR Model, initially all individuals are Susceptible, while the Infection is induced into a small fraction of the total population. Contacts of the ``Infected`` will transition to the ``Infected``. Similarly individuals progress from the ``Infected`` to the ``Removed`` coompartment with another rate. It is important to note that ``Removed`` does not imply either of *Recovered* or *Dead*, rather a combination of both. The representation of the model looks as follows. 
.. figure
The SIR model that is explained below is on an isolated population. The other condition imposed on this population is that people who have been ``Removed`` will not be able to get Susceptible. 

