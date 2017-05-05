
#######
DS-Note
#######

.. image:: https://img.shields.io/badge/view%20source-github-blue.svg?style=flat-square
   :target: https://github.com/dapphub/ds-note

The ``DSNote`` type is a way to generically log function calls as events. To do this, it provides a ``LogNote`` event and a ``note`` modifier. Functions that are decorated with the ``note`` modifier, will log a ``LogNote`` event that contains this list of data:

* ``msg.sig``
* ``msg.sender``
* The first parameter that the function is taking
* The second parameter that the function is taking
* ``msg.value``
* ``msg.data``

The first four items are indexed, making them queryable by blockchain clients. This covers `most` of the usecases for events, making this package useful to quickly add event logging functionality to your dapp.

.. _DSNote:

DSNote
======

Your contract should inherit from the ``DSNote`` type if you want a simple repeatable way to log function calls as events.

Import
------
``import ds-note/note.sol``

Parent Types
------------

None


API Reference
-------------

event LogNote
^^^^^^^^^^^^^

This event will log information about functions that are decorated with the ``note`` modifier. The parameters correspond to these data fields:

* ``sig`` is ``msg.sig``
* ``guy`` is ``msg.sender``
* ``foo`` is the first parameter that the function is taking
* ``bar`` is the second parameter that the function is taking
* ``wad`` is ``msg.value``
* ``fax`` is ``msg.data``

::

    event LogNote(
        bytes4   indexed  sig,
        address  indexed  guy,
        bytes32  indexed  foo,
        bytes32  indexed  bar,
        uint              wad,
        bytes             fax
    ) anonymous

modifier note
^^^^^^^^^^^^^

Decorating functions with the ``note`` modifier will cause useful information to be logged when they are called. See ``LogNote`` above for the specific information that gets logged.

::

    modifier note