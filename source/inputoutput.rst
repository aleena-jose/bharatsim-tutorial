Inputs and Outputs in BharatSim
===============================

Inputs
------
**to be completed**

* function should feed in the csv file 1 row at a time
* data gets 'ingested' and graphNodes are created

Outputs
-------

A convenient way to store the output is by using a `CSV <https://en.wikipedia.org/wiki/Comma-separated_values>`_ file, which is discussed below.

.. note:: In case the quantities you'd like to output are fairly simple, you could use Scala's ``println`` function to directly output what you need.

Saving your output to a CSV file
~~~~~~~~~~~~~~~~~~~~~~~~

The BharatSim engine contains a class called ``CsvOutputGenerator``, which has two attributes:
* ``path``, the desired path for the output file to be stored
* ``csvSpecs``, a user-defined class that outputs the headers and the rows required. Note that this user-defined class should extend the ``CSVSpecs`` trait and override the ``getHeaders`` and ``getRows`` methods.

Once the class is complete, we now have to **register** it in the simulation (defined in the main function), similar to how we registered `agents <#>`_. First, we must import ``CsvOutputGenerator`` and ``SimulationListenerRegistry``

.. code-block:: scala
   
  import com.bharatsim.engine.listeners.{CsvOutputGenerator, SimulationListenerRegistry}


Next, we register it using the ``register`` method of ``SimulationListenerRegistry``

.. code-block:: scala

  SimulationListenerRegistry.register(
    new CsvOutputGenerator("src/main/resources/output.csv", new myCsvSpecs(context))
    )

where ``myCsvSpecs`` is the user-defined class which requires the context as an attribute.

An example: The SIR Model
~~~~~~~~~~~~~~~~~~~~~~~~~

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

Now we simply have to register it in the simulation. Note that the following code snippet should be located in the main function:

.. code-block:: scala

    val simulation = Simulation()

    simulation.ingestData(implicit context => {
      ingestCSVData("inputData.csv", csvDataExtractor)
    })

    simulation.defineSimulation(implicit context => {

      create12HourSchedules()

      registerAction(
        StopSimulation,
        (c: Context) => {
          getInfectedCount(c) == 0
        }
      )

      beforeCount = getInfectedCount(context)

      registerAgent[Person]

      SimulationListenerRegistry.register(
        new CsvOutputGenerator("src/main/resources/output.csv", new SIROutputSpec(context))
      )
    })

    simulation.onCompleteSimulation { implicit context =>
      printStats(beforeCount)
      teardown()
    }

.. hint:: Running the above block of code once will cause a file called ``output`` to be created at ``src/main/resources/``. However, running it again will rewrite the contents of the file with the new output. You can get around this by adding the current time to the output as a string. For example,

  .. code-block:: scala

    val currentTime = new Date().getTime

    SimulationListenerRegistry.register(
        new CsvOutputGenerator("src/main/resources/output_" + currentTime + ".csv", new SIROutputSpec(context))
      )
  Note that ``Date().getTime`` returns the time as a `UNIX timestamp <https://en.wikipedia.org/wiki/Unix_time>`_, and so your output will contain a long integer after the underscore.