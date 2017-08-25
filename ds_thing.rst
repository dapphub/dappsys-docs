
########
DS-Thing
########

.. image:: https://img.shields.io/badge/view%20source-github-blue.svg?style=flat-square
   :target: https://github.com/dapphub/ds-thing

The DS-Thing mixin type serves as a generalized base class for standalone contracts. It simply inherits from the :ref:`DSAuth <DSAuth>`, :ref:`DSNote <DSNote>`, and :ref:`DSMath <DSMath>` types, making it an alias type for blockchain "things" that can be called, use math, log events, and have a concept of ownership.

.. _DSThing:

DSThing
=======

Your contract should inherit from the ``DSThing`` type if you know it will be doing math on user input data, logging events for function calls, and will have access-controlled functions.

Import
------
``import ds-thing/thing.sol``

Parent Types
------------

:ref:`DSAuth <DSAuth>`, :ref:`DSNote <DSNote>`, :ref:`DSMath <DSMath>`


API Reference
-------------

none