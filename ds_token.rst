########
DS-Token
########

.. image:: https://img.shields.io/badge/view%20source-github-blue.svg?style=flat-square
   :target: https://github.com/dapphub/ds-token

The DS-Token package is for creating useful `ERC20 <https://github.com/ethereum/EIPs/issues/20>`_ tokens. There are two types included in this package, ``DSTokenBase`` and ``DSToken``. If you just want a standard ERC20 template to extend with your business logic, inherit from the ``DSTokenBase`` type. 

If you already working with a :ref:`DSAuth <DSAuth>`-controlled system however, we highly recommend using the ``DSToken`` type. This type is meant to serve as a box contract (meaning it should be deployed to the blockchain without modification) and then controlled with other admin contracts. This is because ``DSToken`` introduces three new features that cover almost all `direct` usecases for tokens: ``mint``, ``burn``, and ``stop``/``start``. As one might guess, the ``mint`` function creates new tokens, the ``burn`` function destroys existing tokens, and the ``stop``/``start`` functions disable/enable the normal functionality of the token. All three of these features are ``auth`` controlled, which means `a separate contract can control the token and its supply according to unique business logic`. 

This is what makes ``DSToken`` so useful at its core. It is obvious that all the different ERC20 tokens of Ethereum will have special unique business logic, but at their core they almost always boil down to creating and destroying tokens according to some condition. The ``DSToken`` building block can be deployed just once and still fit into an upgradeable system thanks to the separation of concerns afforded by DS-Auth. A brief example:

::

    // An interface that your unique business logic implements
    contract SystemRules {

        function canCashOut(address user);

        function serviceFee() returns (uint128);
    }

    // A basic controller that handles cashing out 
    // from appTokens to some underlying collateral

    contract AppTokenController is DSAuth, DSMath {

        ERC20 deposit;
        DSToken appToken;
 
        SystemRules rules;

        function cashOut(uint128 wad) {
            assert(rules.canCashOut(msg.sender));

            // Basic idea here is that prize < wad
            // with the contract keeping the difference as a fee.
            // See DS-Math for wdiv docs.
            
            uint prize = wdiv(wad, rules.serviceFee());

            claimTokens.pull(msg.sender, wad);

            // only this contract is authorized to burn tokens
            claimTokens.burn(prize);

            deposit.transfer(msg.sender, prize);
        }

        function newRules(SystemRules rules_) auth {
            rules = rules_;
        }
    }

As you can see, the actual operations on the token are completely covered by the ``DSToken`` feature set, there is some app-specific logic around charging fees, and the system is completely updateable. This is what makes the ``DSToken`` type so useful.

.. _DSTokenBase:

DSTokenBase
===========

Your contract should inherit from the ``DSTokenBase`` type if you want it to have standard `ERC20 <https://github.com/ethereum/EIPs/issues/20>`_ functionality.

Import
------
``import ds-token/base.sol``

Parent Types
------------

`ERC20 <https://github.com/dapphub/erc20>`_, :ref:`DSMath <DSMath>`


API Reference
-------------

function totalSupply
^^^^^^^^^^^^^^^^^^^^

Returns the outstanding supply of all tokens.

::

    function totalSupply() constant returns (uint256)

function balanceOf
^^^^^^^^^^^^^^^^^^

Returns the balance of the ``src`` address.

::

    function balanceOf(address src) constant returns (uint256)

function allowance
^^^^^^^^^^^^^^^^^^

Returns the amount of tokens that ``guy`` can withdraw from the ``src`` address via the ``transferFrom`` function.

::

    function allowance(address src, address guy) constant returns (uint256)

function approve
^^^^^^^^^^^^^^^^

Approves ``guy`` to withdraw ``wad`` tokens from ``msg.sender`` via the ``transferFrom`` function. Throws on uint overflow.

::

    function approve(address guy, uint256 wad) returns (bool)

function transfer
^^^^^^^^^^^^^^^^^

Transfers ``wad`` tokens from ``msg.sender`` to the ``dst`` address. Throws on uint overflow.

::

    function transfer(address dst, uint wad) returns (bool)

function transferFrom
^^^^^^^^^^^^^^^^^^^^^

Assumes sufficient approval set by the ``approve`` function. Transfers ``wad`` tokens from the ``src`` address to the ``dst`` address and decrements ``wad`` from ``approvals[src][msg.sender]``. Throws on uint overflow.

::

    function transferFrom(address src, address dst, uint wad) returns (bool)


DSToken
=======

You should deploy a ``DSToken`` contract if you want standard `ERC20 <https://github.com/ethereum/EIPs/issues/20>`_ functionality, plus the ability to create and destroy tokens and disable the token's functionality from authorized addresses. This complete functionality covers almost all basic token usecases, allowing you to separate app-specific business logic into adminstrative controller contracts with elevated permissions on the token.

Import
------
``import ds-token/token.sol``

Parent Types
------------

:ref:`DSTokenBase(0) <DSTokenBase>`, :ref:`DSStop <DSStop>`, :ref:`DSAuth <DSAuth>`, DSNote


API Reference
-------------

function DSToken
^^^^^^^^^^^^^^^^

The constructor function only assumes you want a custom symbol. For custom ``name``, see the ``setName`` function. For custom ``decimals``, please think carefully about what you're doing and then override the type if you still need them.

::

    function DSToken(bytes32 symbol_)

function symbol
^^^^^^^^^^^^^^^

Returns the value of the public ``symbol`` variable. Used to identify the token.

::

    string public symbol

function decimals
^^^^^^^^^^^^^^^^^

Returns ``18``. For custom ``decimals``, please think carefully about what you're doing and then override the type if you still need them.

::

    uint256 public decimals = 18


function mint
^^^^^^^^^^^^^

Creates ``wad`` tokens and adds them to the balance of ``msg.sender``, increasing the total supply. Throws on uint overflow.

::

    function mint(uint128 wad) auth stoppable note

function burn
^^^^^^^^^^^^^

Removes ``wad`` tokens from the balance of ``msg.sender`` and destroys them, reducing the total supply. Throws on uint overflow.

::

    function burn(uint128 wad) auth stoppable note

function push
^^^^^^^^^^^^^

Alias for ``transfer(dst, wad)``.

::

    function push(address dst, uint128 wad) returns (bool)

function pull
^^^^^^^^^^^^^

Alias for ``transferFrom(src, msg.sender, wad)``.

::

    function pull(address src, uint128 wad) returns (bool)

function transfer
^^^^^^^^^^^^^^^^^

Identical functionality to its parent function in ``DSTokenBase``. Adds the ``stoppable`` and ``note`` modifiers.

::

    function transfer(address dst, uint wad) stoppable note returns (bool)

function transferFrom
^^^^^^^^^^^^^^^^^^^^^

Identical functionality to its parent function in ``DSTokenBase``. Adds the ``stoppable`` and ``note`` modifiers.

::

    function transferFrom(
        address src, address dst, uint wad
    ) stoppable note returns (bool)

function approve
^^^^^^^^^^^^^^^^

Identical functionality to its parent function in ``DSTokenBase``. Adds the ``stoppable`` and ``note`` modifiers.

::

    function approve(address guy, uint wad) stoppable note returns (bool)

function name
^^^^^^^^^^^^^

Returns the value of the public ``name`` variable. Used to identify the token. Defaults to empty string and can be set via ``setName``.

::

    bytes32 public name = ""

function setName
^^^^^^^^^^^^^^^^

Sets the token's ``name`` variable. Controlled by ``auth``.

::

    function setName(bytes32 name_) auth
