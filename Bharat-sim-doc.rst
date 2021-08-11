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

Overview
==================

BharatSim is a framework for scientific researchers to build an `agent based model <https://en.wikipedia.org/wiki/Agent-based_model>`_ to help envision the disease spread of COVID-19, devise, visualize and compare different interventions strategies (like vaccinations or lockdowns) and incorporate the effects of different levels of compliance. It allows the user to create hypothetical scenarios and simulate them. The agents in this model are normal people, going about their daily lives.

BharatSim uses Scala, a language that uses the JVM or Java Virtual Machine to run. To easily view all the code, one can install IntelliJ, an IDE to view, modify and run the code.

Installation
=============

Prerequisites
--------------
1. Setup scala and sbt - https://docs.scala-lang.org/getting-started/index.html#install-scala
2. Configure IntelliJ - https://docs.scala-lang.org/getting-started/intellij-track/getting-started-with-scala-in-intellij.html#installation

Code setup
-----------
1. Clone the repository git clone - https://github.com/debayanLab/BharatSim
2. Open the BharatSim-master folder in Intellij. By default, IntelliJ shows a screen which is divided into two parts. The smaller panel on the left shows you the directory and all the files in the main folder while the bigger panel in the middle is where all the code is displayed. This is where you will write all your Scala code and make changes to the existing code.
3. On the left panel, click on BharatSim-master > src > main > scala > com.bharatsim > examples.epidemiology. Here you will see two directories named **sir** and **tenCompartmentalModel**.
4. Open the **sir** directory.
5. It contains a few files, all of which are Scala Classes. The class that is used to run the model is the Main class. In order to run the main class, click on the green triangle.

.. image:: D://Soumil/Project/Docs/Im1.png




