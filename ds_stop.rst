#######
DS-Stop
#######

.. image:: https://img.shields.io/badge/view%20source-github-blue.svg?style=flat-square
   :target: https://github.com/dapphub/ds-stop

DS-Stop is a simple mixin type that allows an authorized account to disable and enable functions on deriving types via a ``stoppable`` modifier. It is useful in situations where one needs to halt a system for maintenance, in case of emergency, or simply to wind it down after a temporary lifespan.

.. _DSStop:

DSStop
======

Your contract should inherit from the ``DSStop`` type if you want an admin address to be able to disable/enable any of its functions.

Import
------
``import ds-stop/stop.sol``

Parent Types
------------

:ref:`DSAuth <DSAuth>`, DSNote


API Reference
-------------

function stopped
^^^^^^^^^^^^^^^^

Returns the value of the public ``stopped`` variable, which is set to ``true`` when the token's ``stoppable`` functions are disabled.

::
    
    bool public stopped

function stop
^^^^^^^^^^^^^

Sets ``stopped`` to ``true``, which disables normal token behavior.

::

    function stop() auth note

function start
^^^^^^^^^^^^^^

Sets ``stopped`` to ``false``, which enables normal token behavior.

::

    function start() auth note

modifier stoppable
^^^^^^^^^^^^^^^^^^

Asserts that ``stoppable`` is equal to ``false``, allowing an admin account to disable normal token operations.

::

    modifier stoppable