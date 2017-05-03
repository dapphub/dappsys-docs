
########
DS-Guard
########

.. image:: https://img.shields.io/badge/view%20source-github-blue.svg?style=flat-square
   :target: https://github.com/dapphub/ds-guard

DS-Guard is an implementation of the :ref:`DSAuthority <DSAuthority>` interface. It is a box contract (meaning it should be deployed to the blockchain without modification) that offers a simple implementation of a whitelist, with the ability to grant addresses the authorization to call individual functions on specific contracts. This is stored in a three-dimensional mapping data structure:

::

    mapping (bytes32 => mapping (bytes32 => mapping (bytes32 => bool))) acl

It is easy to understand the semantics of this mapping when comparing it against the definition of the ``canCall`` function that makes up the ``DSAuthority`` interface:

::

    function canCall(address src, address dst, bytes4 sig)

Thus, the key of the outermost mapping is the ``src`` (or calling) address, the key of the middle mapping is the ``dst`` (or destination) address, and the key of the innermost mapping is the ``sig`` function.

DS-Guard also has a public constant called ``ANY`` that allows one to create blanket authorizations. One can create specific ``(src, dst, sig)`` triples or blanket authorizations to grant access to groups of addresses or functions. These are all the different authorization statements that one can make:

* A specific address can call a specific function on a specific contract
* A specific address can call ANY function on a specific contract
* A specific address can call a specific function on ANY contract
* A specific address can call ANY function on ANY contract
* ANY address can call a specific function on a specific contract
* ANY address can call ANY function on a specific contract
* ANY address can call a specific function on ANY contract
* ANY address can call ANY function on ANY contract

These different statements are all combined with the OR operator, meaning that the most open permission will take precedence. As you can see, this list is divided between giving super-user access to an address and making functions public (which essentially removes the functionality of the ``auth`` modifier). Both of these types of authorization can be dangerous, so the developer is encouraged to think carefully before using the ``ANY`` permission. For example, suppose a ``DSGuard`` controlled access to the ``mint`` function of a specific contract. If a permission was then added that allowed ANY address to call the ``mint`` function of ANY contract, it would effectively invalidate the original access control.

The ``DSGuard`` type inherits from both the ``DSAuth`` and ``DSAuthority`` types. This means that it has its own ``owner``/``authority``, and it can be an authority for other ``DSAuth``-controlled contract systems. It is possible to control one ``DSGuard`` contract with another ``DSGuard`` contract!

.. _DSGuard:

DSGuard
=======

You should deploy a ``DSGuard`` contract if you want to control other ``DSAuth`` contracts with a simple whitelist that holds entries for each access-controlled function. This is the simplest implementation of the ``DSAuthority`` interface.

Import
------
``import ds-guard/guard.sol``

Parent Types
------------

:ref:`DSAuth <DSAuth>`, :ref:`DSAuthority <DSAuthority>`


API Reference
-------------

event LogOkay
^^^^^^^^^^^^^

This event is logged when modifying the contract's authorization data by calling the ``okay`` function. 

::

    event LogOkay(bytes32 src, bytes32 dst, bytes32 sig, bool yes)

function ANY
^^^^^^^^^^^^

This will return the contract's public ``ANY`` constant, which is an alias for the maximum possible ``uint256`` and is used in ``DSGuard`` to assign blanket permissions (see the introduction above).

::

    bytes32 constant public ANY = bytes32(uint(-1))

function canCall
^^^^^^^^^^^^^^^^

This function definition is inherited from :ref:`DSAuthority <DSAuthority>` and will return ``true`` if the ``DSGuard`` holds authorization data that allows the ``src`` address to call the ``sig`` function on the ``dst`` address. This will either be an explicit authorization entry for the specific ``(src, dst, sig)`` triple in question, or a blanket ANY permission (see the introduction above for the different types of ANY permissions).

::

    function canCall(
        address src, address dst, bytes4 sig
    ) constant returns (bool)

function okay
^^^^^^^^^^^^^

This function has three signatures and is controlled by the ``authorized`` modifier. It allows the caller to edit the whitelist that is consulted by the ``canCall`` function.

The first signature will write the value of ``yes`` to the ``(src, dst, sig)`` triple in the whitelist.

::

    function okay(bytes32 src, bytes32 dst, bytes32 sig, bool yes) authorized("okay")

This signature is an alias for ``okay(src, dst, sig, true)``

::

    function okay(address src, address dst, bytes32 sig)

This signature is an alias for ``okay(src, dst, ANY, true)``

::

    function okay(address src, address dst)

