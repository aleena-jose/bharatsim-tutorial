Assembling an Executable Jar file
===================================

Introduction
------------

This page highlights how you can make a ``jar`` file from any of the models that you create. A ``jar`` file is an executable java file that can be used to run your code

Steps for creating and executing a new jdk jar file
----------------------------------------------------

1.  Go to the sbt shell in IntelliJ, and type ``assembly``

.. figure:: _static/images/jar-doc-1.png
    :width: 200px
    :align: center
    :height: 100px
    :alt: alternate text
    :figclass: align-center

        sbt shell

2. The shell will display all the ``Main.scala`` files which are present in your directory. Select the one you want to run by typing it's index in the shell.
3. The shell will display all the ``Main.scala`` files which are present in your directory. Select the one you want to run by typing it's index in the shell.
4. Alternatively, you can specify which ``Main.scala`` file you want to run in the ``build.sbt`` file by typing the following command :-

.. figure:: _static/images/jar-doc-4.png
    :width: 200px
    :align: center
    :height: 100px
    :alt: alternate text
    :figclass: align-center

