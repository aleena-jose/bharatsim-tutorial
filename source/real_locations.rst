Checking the people in a location (SIR)
=======================================

What we were doing
------------------

In our earlier example of the SIR model, we decided if a person would be infected or not by:

* retrieving the type of location that the person in question was supposed to be in from their schedule
* computing the number of people who could potentially infect them

We use the following function to accomplish the second part of the algorithm:

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

returns the ``EMPLOYER_OF`` string. Therefore, ``totalNeighbourCount`` counts the total number of nodes liked to this particular ``Office`` node with the ``EMPLOYER_OF`` relation.

The problem arises with different methods of scheduling. Someone who's infected may follow a different schedule, and stay at home. However, ``totalNeighbourCount`` *doesn't care* about the location a person has at a particular tick: all it does is count the number of people with the appropriate relations. As a consequence of this, the results would not be what the modeller intended.

.. note:: The same thing happens while calculating ``infectedNeighbourCount``. This has effects not just on the workplace, but on homes, hospitals, and other locations in your model too.

Dealing with the problem
------------------------

There are two currently proposed methods:

Using an attribute of the ``Person`` class
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We can solve the problem by adding an attribute called ``currentLocation`` to the ``Person`` class.

.. code-block:: scala

  case class Person(id: Long, age: Int, infectionState: InfectionStatus, infectionTick: Int,
                  RecoveryTick: Double, currentLocation: String = "HOUSE") extends StatefulAgent {}


.. tip:: We've set ``"House"`` as the `default value <https://docs.scala-lang.org/tour/default-parameter-values.html>`_ of the attribute, and so it's no longer necessary to initialize it when creating an instance of the ``Person`` class in the user-defined ``csvDataExtractor`` function.

After doing so, we need to add a behaviour which changes the ``currentLocation`` at every tick. First, we define what we want the behaviour to do with the following block of code in the ``Person`` class:

.. code-block:: scala

  private val checkCurrentLocation: Context => Unit = (context: Context) => {
    val schedule = context.fetchScheduleFor(this).get
    val locationNextTick: String = schedule.getForStep(context.getCurrentStep + 1)
    if (currentLocation != locationNextTick) {
      this.updateParam("currentLocation", locationNextTick)
    }
  }

Next, we need to register the behaviour so that it's executed every tick:

.. code-block:: scala

  addBehaviour(checkCurrentLocation)

.. hint:: ``updateParam`` only updates the value of the attribute at the **end** of the tick. Thus, for all practical purposes, it's useful to view the function as one that changes the value of the attribute on the *subsequent tick*. As such, we store the place the person is expected to be on the next tick, and hence use ``context.getCurrentStep+1`` as an argument to ``schedule.getForStep``.

Now, we need use this attribute when we compute ``totalNeighbourCount`` and ``infectedNeighbourCount``. The basic structure of the function remains the same:

.. code-block:: scala

  def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {}

``node.getConnectionCount`` has another (optional) argument besides the relation, which is ``matchPattern``. Using it, we can get counts of the people with a specific relation who also satisy some other condition based on their attributes: in this case, we'll look for the people who have the ``currentLocation`` attribute equal to the ``placeType`` of the node.

..code-block:: scala
    val totalNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      "currentLocation" equ placeType)

As we did before, we return ``0`` if there are no neighbours (as otherwise we'd be dividing by 0):

.. code-block:: scala

    if (totalNeighbourCount == 0) return 0d

Next, we need the total count of infected people. We can do that by checking that the person's ``infectionState`` is ``Infected``, in addition to what we did before:

.. code-block:: scala

    val infectedNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      ("infectionState" equ Infected) and ("currentLocation" equ placeType))

.. note:: You need to use ``equ``, ``and`` and other pattern-matching relations instead of the scala versions ``==``, ``&&``, etc. They're defined in ``com.bharatsim.engine.graph.patternMatcher.MatchCondition``. Remember to import them!

Finally, we return the infected fraction,

.. code-block:: scala

    infectedNeighbourCount.toDouble / totalNeighbourCount.toDouble

Putting it all together, our function is

.. code-block:: scala

  def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {
    val totalNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      "currentLocation" equ placeType)

    if (totalNeighbourCount == 0) return 0d

    val infectedNeighbourCount = node.getConnectionCount(node.getRelation[Person]().get,
      ("infectionState" equ Infected) and ("currentLocation" equ placeType))

    infectedNeighbourCount.toDouble / totalNeighbourCount.toDouble
  }


Checking the locations without a ``currentLocation`` attribute
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``updateParam`` updates a node on the graph, and is called once per person per tick. That can potentially slow the program down, and another possibility is to avoid using it entirely. We'll still do the same thing - get the schedule for the agent, check if they're actually at the place you're looking at, and then get the total and infected counts.

.. note:: We can't use ``getConnectionCount`` anymore, cause there's no attribute to match to. As such, the calculation of the total and infecteded neighbour counts is done by iterating over every person with the relation, and adding them in.

Let's break it up: the structure of the function remains identical

.. code-block:: scala

  def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {}

First, we assign two variables to count the number of total and infected neighbors. These will be incremented later.

.. code-block:: scala

    var totalNeighbourCount: Int = 0
    var infectedNeighbourCount: Int = 0

We now find everyone with the appropriate relation:

.. code-block:: scala

    val peopleWithRelation: Iterator[GraphNode] = node.getConnections(node.getRelation[Person]().get)

.. note:: ``peopleWithRelation`` is a convenient data structure called an `iterator <https://docs.scala-lang.org/overviews/collections/iterators.html>`_. It's very useful if you want to loop through a container, as we do here.

Now, we want to check the ``currentLocation`` and ``infectionState`` for every one of these people. We iterate over the iterator using the ``foreach`` method:

.. code-block:: scala

    peopleWithRelation.foreach (relatedPerson => {}

.. hint:: The function inside the curly brackets is executed for every ``GraphNode`` in the iterator. We can easily reference that particular node with ``relatedPerson``.

The first thing we want to do for each ``relatedPerson`` is to get the location they're expected to be at this tick

.. code-block:: scala

      val schedule = context.fetchScheduleFor(relatedPerson.as[Person]).get
      val locationThisTick: String = schedule.getForStep(context.getCurrentStep)

First we check if the ``relatedPerson`` is actually in the place we're looking at, and if so we increment ``totalNeighbourCount``. If they're also infected, we increment ``infectedNeighbourCount``.

.. code-block:: scala

      if (locationThisTick == placeType) {
        totalNeighbourCount += 1
        if (relatedPerson.as[Person].isInfected) {
          infectedNeighbourCount += 1
        }
      }

That's all we need to do for each ``relatedPerson``: outside the loop, we now have to check for the edge case where ``totalNeighbourCount = 0``, and return the infected fraction

.. code-block:: scala

    if (totalNeighbourCount == 0) return 0d

    infectedNeighbourCount.toDouble / totalNeighbourCount.toDouble

All in all, the function we use is

.. code-block:: scala

  def computeInfectedFraction(node: Node, placeType: String, context: Context): Double = {
    var totalNeighbourCount: Int = 0
    var infectedNeighbourCount: Int = 0
    val peopleWithRelation: Iterator[GraphNode] = node.getConnections(node.getRelation[Person]().get)
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

At the moment, we cannot say which method is preferable as there hasn't been much testing to see how they scale up with the size of the population.
