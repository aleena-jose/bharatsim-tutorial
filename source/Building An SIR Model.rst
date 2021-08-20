


Introduction
------------

Bharatsim is an open-source large-scale agent-based simulation framework that allows you to build and simulate sophisticated agent-based models. Agents form the main component of such a simulation. They are entities that are defined with a set of parameters and obey certain specified conditions as the simulation runs.

In the purview of the SIR model, the agents are people, which go about their daily lives and follow regular schedules. Here, the parameters defining an agent are a unique agent id, their age, their infection status, the time for which they remain infected and so on. The framework allows you to introduce new parameters as and when required.

The rules governing the behaviour of all agents include the particular schedules which they follow and the functions which allow the agents to transit from one compartment to another as described before.

Model Components
----------------

The model consists of different objects and classes and can be separated into individual ``.scala`` files.

1. Nodes of the Network: These include ``House``, ``School``, ``Office``, etc, which essentially correspond to the different geographical locations in which the agents spend their time according to some schedule. These classes have some attributes associated with them. For eg. ``getContactProbability`` is one such function that gives the transmission probability of the disease in that particular location. In order to add new Nodes to the Network (for eg. Hospital), one has to create a new scala class inside the same package corresponding to the new Node.

.. image:: House.png

  

2. Disease: ``Disease`` is a class where all epidemiological parameters are conveniently defined. For example, the transmission rate :math:`\beta` is defined in this class. This class allows for a convenient way to store and modify variables as required.

3. Infection Status: The ``InfectionStatus`` is the class where all the compartments of the model are defined. The ``InfectionStatus`` object in the class, is a serialisable object which includes all the compartments. Inside this class, there is an encoder function which converts the compartment into a corresponding string and a decoder function, which decodes a string into a particular compartment. To add a new compartment like ‘Exposed’ refer to the figure below:-

.. image:: InfectionStatus.png

4. Output Specification: ``CSVSpecs`` is a framework defined trait which helps in generating a .csv file for the simulation output. The ``SEIROutputSpec`` is a class that extends this trait and defines how the output ``.csv`` file should look. Inside this class, the ``getHeaders`` basically specifies the list of column headers in the ``.csv`` file, while the ``getRows`` fetches the values of these quantities from context. For eg. one can use the ``fetchcount`` function in the framework defined trait ``graphProvider`` to display the number of people in each compartment at every tick.


Saving your Output
~~~~~~~~~~~~~~~~~~

Suppose you wanted your output to give you the numbers of susceptible, infected and recovered people at every time step. You can then write the following:

.. code-block:: scala

  import com.bharatsim.engine.Context
  import com.bharatsim.engine.graph.patternMatcher.MatchCondition._
  import com.bharatsim.engine.listeners.CSVSpecs
  import com.bharatsim.examples.epidemiology.SIR.InfectionStatus.{Infected, Removed, Susceptible}
  
  class SIROutputSpec(context: Context) extends CSVSpecs {
    override def getHeaders: List[String] =
      List(
        "Step",
        "Susceptible",
        "Infected",
        "Removed"
      )
  
    override def getRows(): List[List[Any]] = {
      val graphProvider = context.graphProvider
      val label = "Person"
      val row = List(
        context.getCurrentStep,
        graphProvider.fetchCount(label, "infectionState" equ Susceptible),
        graphProvider.fetchCount(label, "infectionState" equ Infected),
        graphProvider.fetchCount(label, "infectionState" equ Removed)
      )
      List(row)
    }
  }
 
* The first column (Step) stores the current time step, obtained using the ``context.getCurrentStep`` function
* The next 3 columns store the number of Susceptible, Infected and Removed people respectively, by fetching the total number of ``Person`` nodes on the graph with the appropriate `infection status <#>`_.

Now we simply have to register it in the simulation. Note that the following code snippet should be located inside ``simulation.defineSimulation`` in the main function:

.. code-block:: scala

  SimulationListenerRegistry.register(
    new CsvOutputGenerator("src/main/resources/output.csv", new SIROutputSpec(context))
      )