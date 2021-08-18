.. Bharat-Sim documentation master file, created by
   sphinx-quickstart on Mon Jul 12 19:47:20 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Bharat-Sim's documentation!
======================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:


* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`




Step-by-step Construction of the SIR Model
===========================================

Bharatsim is an open source large-scale agent-based simulation framework which allows you to build and simulate sophisticated agent-based models. Agents form the main component of such a simulation. They are entities which are defined with a set of parameters and obey certain specified conditions as the simulation runs.

In the purview of the SIR Model, the agents are people, which go about their daily lives and follow regular schedules. Here, the parameters defining an agent are a unique agent id, their age, their infection status, the time for which they remain infected and so on. The framework allows you to introduce new parameters as and when required.

The rules governing the behaviour of all agents include the particular schedules which they follow and the functions which allow the agents to transit from one compartment to another as described before. 







Schedules
---------

The current schedules of all the agents are stored in the **create12HourSchedules** function. The default function has three schedules:-

1. **employeeSchedule** - This schedule is followed by all agents above the age of 25.
2. **studentSchedule** - This is followed by all the agents below the age of 25.
3. **quarantinedSchedule** - This is an intervention which 'tells' all the infected agents to stay home until they recover.

The *myDay* and *myTick* values defined at the start of the code are used to define all schedules. For example, if *myDay* has 2 ticks (each tick is 12 hours long), the **employeeSchedule** says that all employees remain at home during tick 0, and then they spend tick 1 in the office. The infected agents following the **quarantinedSchedule** spend both ticks at home.

Thus, as more and more classes of agents are introduced, they can be made to follow different schedules. These schedules can also be made more complex by changing the number of ticks in a day.

Behaviours
----------

Behaviours are a set of conditions which are checked for all agents during each tick. They govern the transitions of all agents between the different compartments of the model. The **Person** file currently has 3 behaviours:-

1. **incrementInfectionDay** - This function counts the number of days for which an agent is infected.
2. **checkForInfection** - This function is checked for all the susceptible agents in the population and it governs the transition from the susceptible to the infected compartment.
3. **checkForRecovery** - This function is checked for all the infected agents in the population and governs the transitions from the infected to the recovered compartment.

Different behaviours can be added to this file, which impose different conditions on the agents.

*checkForInfection*
---------------------

1. The **isSusceptible** function ensures that this behaviour is only checked for the susceptible agents.
2. Next, the function fetches the schedule for each agent, which determines the location of that particular agent at the current tick. The location of an agent has two components-

    * **placeType** gives the type of location, that is House/Office/School
    * **place** gives the unique id of that location.

   For example, House 203, Office 102 are valid locations.

3. The **fetchInfectedFraction** function calculates the fraction of agents which are infected in that particular location at the current tick.
4. The transition from the susceptible to the infected compartment is inherently probabilistic and this probability (*P*) is a product of three components:-

    * The transmission rate (:math:`{\beta}`) is the rate at which the disease is transmitted from an infected person to a susceptible person (per day).
    * The time spent in that particular location (in days).
    * The fraction of infected people in that location at the current tick.

5. The **biasedCoinToss** function picks a random number between 0 and 1. If this number is less than *P*, it updates the infection status of a person from Susceptible to Infected. Otherwise, the agent remains susceptible.

*checkForRecovery*
------------------

1. The **isInfected** function ensures that this behaviour is only checked for the infected agents.
2. The probability to transition to the Recovered compartment is a product of two components:-

    * The transmission rate (:math:`{\lambda}`) (per day).
    * The length of each tick (in days).







