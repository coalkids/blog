Running nosetests on a dotcloud python instance
###############################################

:date: 2013-03-11 12:26
:tags: python, dotcloud, nose, nosetests
:category: python
:slug: nosetests-dotcloud
:author: Julien Seiler
:lang: en

A few month ago, we have chosen to rely on dotcloud to host our whole Bulleval_ stack. This includes a Python service hosting our web API.

<<<<<<< HEAD
I recently faced the need to run unit and functionnal tests for our Python service in order to check for any integration issue within the dotcloud infrastructure. So I basically, open a ssh connection to a sandbox instance of our Python service. ::
=======
We reccently faced the need to run unit and functionnal tests for our Python service in order to check for any integration issue within the dotcloud infrastructure. So we basically, open a ssh connection to a sandbox instance of our Python service. ::
>>>>>>> a1dd09598a3916c67598be7b40aabaf099d52e9b

    localhost$ dotcloud run python_instance_name.0
    dotcloud$

We use nose_ for our Python tests. Because, nose is not a strong dependency of our application, it is not listed in our ``requirements.txt`` file, so we pip-install it manually on the dotcloud instance. ::

    dotcloud$ pip install nose

Now, we can basically run the tests suite from the application sources. For some reasons, when pushing sources to dotcloud, we appear that all ``.py`` files are set to be executable. However, by default, nose exclude all executable files from the tests suite. ::

    dotcloud$ cd current
    dotcloud$ nosetests
    ----------------------------------------------------------------------
    Ran 0 tests in 0.300s

    OK

To be able to run nose tests on the dotcloud, we basically use the ``--exe`` option that enable tests search in python modules that are executable. ::

    dotcloud$ nosetests --exe
    ..............................................................................
    ..............................................................................
    ..............................................................................
    ..............................................................................
    ...................................................................
    ----------------------------------------------------------------------
    Ran 379 tests in 4.360s

    OK

.. _nose: https://nose.readthedocs.org
.. _Bulleval: http://www.bulleval.fr