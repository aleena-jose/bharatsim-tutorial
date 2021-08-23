Checking the people in a location (SIR)
=======================================

What we were doing
------------------

While calculating the number of people who could potentially infect a person, we did the following:

.. code-block:: scala

    def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {
    val totalNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get)
    if (totalNeighbourCount == 0)
      return 0d
    val infectedNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      "infectionState" equ Infected).toDouble
    infectedNeighbourCount / totalNeighbourCount.toDouble
  }

Let's take a closer look at the first line, how we calculate ``totalNeighbourCount``.

.. code-block:: scala

  val totalNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get)

Assume that the node was an ``Office``. In that case,

.. code-block:: scala

  node.getRelation[Person]().get

returns the ``EMPLOYER_OF`` string. Now, ``totalNeighbourCount`` is counting the total number of nodes which the workplace node is the ``EMPLOYER_OF``.

The problem arises when we introduce quarantining to our model. Someone who's infected may follow a different schedule, and stay at home. However, ``totalNeighbourCount`` *doesn't care* about the location a person has at a particular tick: all it does it count the number of people with the appropriate relations.

As a result of this, quarantining individuals is far less effective than it should be. It still could have a minor effect (depending on your model), as someone who's infected does not go to their workplace. Regardless, that person still infects others currently at the workplace.

.. note:: The same thing happens while calculating ``infectedNeighbourCount``. This has effects not just on the workplace, but on homes, hospitals, and other locations in your model too.

Dealing with the problem
------------------------

There are two currently proposed methods:

Using an attribute of the ``Person`` class
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We can solve the problem by adding an attribute called ``currentLocation`` to the ``Person`` class.

.. tip:: Remember to also initialize the attribute when created an instance of the class while extracting data from the CSV!

After doing so, we need to add a behaviour which changes the ``currentLocation`` at every tick. We could do this by adding the following line of code in the ``Person`` class:

.. code-block:: scala

  private val checkCurrentLocation: Context => Unit = (context: Context) => {
    val schedule = context.fetchScheduleFor(this).get
    val locationNextTick: String = schedule.getForStep(context.getCurrentStep + 1)
    if (currentLocation != locationNextTick) {
      this.updateParam("currentLocation", locationNextTick)
    }
  }
  addBehaviour(checkCurrentLocation)

.. hint:: ``updateParam`` only updates the value of the attribute at the **end** of the tick. Thus, for all practical purposes, it's useful to view the function as one that changes the value of the attribute on the *subsequent tick*. As such, we store the place the person is expected to be on the next tick, and hencee use ``context.getCurrentStep+1`` as an argument to ``schedule.getForStep``.

Now, we need use this attribute when we compute ``totalNeighbourCount`` and ``infectedNeighbourCount``. ``node.getConnectionCount`` has another (optional) argument besides the relation, which is ``matchPattern``. Using it, we can get counts of the people with the relationship who's attributes follow a pattern: in this case, we'll look for the people who have the ``currentLocation`` attribute equal to the ``placeType`` of the node.


.. code-block:: scala

  def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {
    val totalNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      "currentLocation" equ placeType)

    if (totalNeighbourCount == 0) return 0d

    val infectedNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      ("infectionState" equ Infected) and ("currentLocation" equ placeType))

    infectedNeighbourCount.toDouble / totalNeighbourCount.toDouble
  }

.. note:: ``equ``, ``and`` and other pattern-matching relations are defined in ``import com.bharatsim.engine.graph.patternMatcher.MatchCondition._``

Checking the locations without a parameter
------------------------------------------

``updateParam`` updates a node on the graph, and is called once per person per tick. That can potentially slow the program down, and another possibility is to avoid using it entirely. We'll still do the same thing - get the schedule for the agent, check if they're actually at the place you're looking at, and then get the total and infected counts.

The following function accomplishes this:

.. code-block:: scala

  def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {
    var totalNeighbourCount: Int = 0
    var infectedNeighbourCount: Int = 0
    val peopleWithRelation = node.getConnections(node.getRelation[Person]().get)
    peopleWithRelation.foreach (relatedPerson => {
      val schedule = context.fetchScheduleFor(relatedPerson.as[Person]).get
      val locationThisTick: String = schedule.getForStep(context.getCurrentStep)
      if (locationThisTick == placeType) {
        totalNeighbourCount += 1
        if (relatedPerson.as[Person].isInfected) {
          infectedNeighbourCount += 1
        }
      }
    })
    if (totalNeighbourCount == 0) return 0d

    infectedNeighbourCount.toDouble / totalNeighbourCount.toDouble
  }

.. note:: We can't use ``getConnectionCount`` anymore, cause there's no attribute to match to. As such, the calculation of the total and infecteded neighbour counts is done by iterating over every person with the relation, and adding them in.

At the moment, we cannot say which method is preferable as there hasn't been much testing to see how they scale up with the size of the population.
