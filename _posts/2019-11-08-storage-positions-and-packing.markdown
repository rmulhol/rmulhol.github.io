---
layout: post
title:  "Querying Ethereum Storage: Understanding Positions and Packing"
date:   2019-11-08
categories: blockchain
tags: ethereum rpc
---

One advantage of Ethereum's decentralized applications is their transparency: you don't have to trust anyone because you can verify everything for yourself.
Unfortunately, it's not often clear _how_ to perform that verification.

Ethereum nodes assist in this effort by verifying the validity of state transitions, but a "valid" state transition might still yield unexpected results.
In order to verify that the state of the network matches your expectations, you need to be able to perform queries that access that state.

How do you do that?

**Querying Storage**

The obvious query for accessing contract state is [eth_getStorageAt](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getstorageat).
This enables you to lookup the value assigned to any variable on a deployed contract at any block.

Any time you use this query, the first and third arguments are obvious: the contract address and the block number.
You want to access the value of a variable on a given contract at a given block, so plug in that contract's address and the desired block number (or "latest" for the most recent value).

The tricky part comes in constructing the second argument: the storage key.
[The Solidity docs](https://solidity.readthedocs.io/en/latest/miscellaneous.html#layout-of-state-variables-in-storage) cover how to build this key for Solidity contracts, but the devil is in the details.

The first topic to understand in building storage keys for Solidity contracts is variable positioning.

**Variable Positions**

For contract variables that are neither a mapping nor a dynamic array, the storage key is the zero-indexed position of the variable on the contract.
This means that the first variable usually has a storage key of `0`, the second `1`, the third `2`, and so forth.

The main quirk that can get in the way here is packing.
Variables that do not occupy an entire storage slot (32 bytes) will be packed together if they are ordered contiguously (and both fit in a single slot).

Let's take a look at an example contract:

{% highlight text %}
pragma solidity 0.5.12;

contract PackingExample {
    uint256 callCount;
    uint128 numberOne;
    uint128 numberTwo;

    function setNumberOne(uint128 n) public {
        callCount++;
        numberOne = n;
    }

    function setNumberTwo(uint128 n) public {
        callCount++;
        numberTwo = n;
    }
}
{% endhighlight %}

There are three variables on this contract, but only two storage slot positions.
Since a `uint128` only occupies 16 bytes and we have two of them arranged contiguously following a completely occupied slot, they are packed into the same position: `1`.

Suppose that we execute two transactions against this contract:
1. call `setNumberOne(1)`
1. call `setNumberTwo(2)`

In that case, we would expect a call to get the latest storage at position `1` on this contract to return `0000000000000000000000000000000200000000000000000000000000000001`.

Since items in a storage slot are lower-order aligned, the 16 bytes to the right represent the value for `numberOne` and the 16 bytes to the left represent `numberTwo`.

Let's take a look at one more example:

{% highlight text %}
pragma solidity 0.5.12;

contract PackingExampleTwo {
    uint64 callCount;
    uint128 numberOne;
    uint128 numberTwo;
    uint64 numberThree;
    uint64 numberFour;

    function setNumberOne(uint128 n) public {
        callCount++;
        numberOne = n;
    }

    function setNumberTwo(uint128 n) public {
        callCount++;
        numberTwo = n;
    }

    function setNumberThree(uint64 n) public {
        callCount++;
        numberThree = n;
    }

    function setNumberFour(uint64 n) public {
        callCount++;
        numberFour = n;
    }
}
{% endhighlight %}

Can you guess how many storage slots this contract uses?
Which variables will go into which position?

This contract also requires only two storage slot positions, but the contiguously arranged `uint128` variables do not occupy the same slot.
Instead, position `0` contains `callCount` and `numberOne`, and position `1` contains `numberTwo`, `numberThree`, and `numberFour`.

A `uint64` only requires 8 bytes, which means space remains for `numberOne` to be packed into the same slot as `callCount`.
At that point, the slot at position `0` has utilized 24 of the 32 available bytes, and cannot accommodate the 16 bytes required for `numberTwo`.

Instead, `numberTwo` occupies 16 bytes in the next position, which also has space to accommodate the two following `uint64` values.

Suppose we execute four transactions against this contract:
1. call `setNumberOne(1)`
1. call `setNumberTwo(2)`
1. call `setNumberThree(3)`
1. call `setNumberFour(4)`

In that case, we would expect a call to get the latest storage at position `0` to return `0000000000000000000000000000000000000000000000010000000000000004`, indicating that `callCount` is `4` and `numberOne` is `1`.
Note that the first 8 bytes in this storage slot are not used - the first 8 bytes can be discarded when parsing this payload.

Similarly, the latest storage at position `1` would be `0000000000000004000000000000000300000000000000000000000000000002`, indicating that `numberTwo` is `2`, `numberThree` is `3`, and `numberFour` is `4`.

**Further Considerations**

Being able to correctly identify the positions of variables on a contract is the first step to correctly building storage keys for desired state.
Hopefully, this post has provided some insight into how to correctly identify the positions of variables given storage packing.

Additional knowledge is required to identify the values for more complex types - entries in mappings and dynamic arrays, structs, etc.
Look forward to future posts on those topics!
