Getting Started
===============

BharatSim is written in Scala 2, so once the source code is obtained, a development environment needs to be set up. 


Setting up Scala
----------------

Scala requires setting up

* JDK (Java Development Kit)
* Scala Compiler and SBT (Scala Build Tool) and a few tools

Installing JDK
~~~~~~~~~~~~~~

Java 8 or 11 can be installed from Oracle or through OpenJDK.

`Download OpenJDK <https://adoptopenjdk.net/>`_. OpenJDK 8 or 11 with Hotspot JVM is fine.

Installing Scala with Coursier
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Coursier** can be used to setup Scala in an easy way. 


Follow the installation instructions at the `Coursier Site <https://get-coursier.io/docs/cli-installation>`_. 


Once ``cs`` is downloaded, run:

.. code-block:: console

    $ ./cs setup

This will install and setup Scala and related tools.


Setting up an IDE and First Run
-------------------------------

The recommended IDE is **IntelliJ Idea**. It provides a lot of features helpful for newcomers to a language. It also handles large projects well. 

Running IntelliJ Idea is a bit resource intensive, so if you really have no choice [due to hardware limitations, old systems, etc.], then any text editor works with command line. We talk about IntelliJ Idea and VSCode here.

IntelliJ Idea
~~~~~~~~~~~~~

* Download the `IntelliJ Idea Community Edition <https://www.jetbrains.com/idea/download/>`_. 


.. note:: On Linux, a ``.tar.gz`` file is downloaded. 

  * Unzip the archive: ``tar xvf archivename.tar.gz``. 
  * Go the unarchived folder. Run the ``bin/idea.sh`` file to open IntelliJ Idea.

* Go to File --> Settings [Or press ``CTRL+ALT+S``] to open the Settings Menu. Go to **Plugins** and search and install the **Scala** Plugin.
  
  .. image:: _static/images/idea-scala-plugin.png

The IDE is now setup.

Let's open the Project. The following steps are assuming the source code directory is named ``BharatSim-master``.

* Go to File --> Open, and select the ``build.sbt`` file in the BharatSim source code directory. Now, select **Open as Project**. IntelliJ Idea will load the project.
* We need some dummy data to run any examples.
  
  * Inside IDEA, on the left side there is the Project Directory Tree. Navigate to ``BharatSim-master/src/main/scala/com/bharatsim/model/`` and open the ``DummyDataGenerator.scala`` file.
  * The line where the ``object DummyDataGenerator`` starts, there should be a Green triangle, which looks like a Run/Play button. Click on that and **Run 'DummyDataGenerator'**. 

    .. image:: _static/images/dummy-data-generator.png

  * A window will pop up from below, showing the status of the run. Once it is completed, it should look like:

    .. image:: _static/images/dummy-data-generator-run-finished.png

  * In the Project Directory structure on the left, scroll down a bit. There will be a ``dummy10k.csv`` file now present. 

    .. image:: _static/images/dummy10k-file.png

  * Open your system's file explorer. This dummy data file should be present as ``BharatSim-master/dummy10k.csv``. Rename it to ``citizen10k.csv``.

* Now, we **run the SIR Model**. SIR is a simple compartmental model to analyze epidemics, where a person can be either Susceptible (S), Infected (I) or Recovered (R). We will see SIR Model in detail in the Epidemiology section.
  
  * In IntelliJ Idea, on the left is the project directory structure and tree. Navigate to ``BharatSim-master/src/main/scala/com/bharatsim/examples/epidemiology/sir`` and open the ``Main.scala`` file. 
  * There will again be a Green arrow/triangle besides the line containing ``object Main``. Click on it, and ``Run 'Main'``.
  
    .. error:: If it gives an error like ``Ingestion Failed : java.nio.file.NoSuchFileException: citizen10k.csv``, make sure you properly follow the previous steps to generate dummy data and rename ``dummydata10k.csv`` to ``citizen10k.csv``.

  * Wait till the program finishes running. At the end, it should look like this:

    .. image:: _static/images/sir-run.png

  * The output CSV file is present at ``BharatSim-master/src/main/resources/output_unixtimestamp.csv``. This contains the output as per the specification in the program. This can be used to further analyze the results of the SIR Model run.


If you reached till here, Congratulations! IntelliJ Idea is setup and working correctly.

.. tip:: If the green "Run" arrows do not appear, or some other issues occur and the program does not start to run due to failed dependencies or Scala versions, then there is a simple trick to try.

  IntelliJ Idea stores its own configuration for the project inside a ``.idea`` directory in the project folder. Delete this folder, and start from scratch, by opening the ``build.sbt`` file again and then importing the project.