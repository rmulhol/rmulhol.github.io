---
layout: post
title:  "Caching Ethereum Data: Events vs Storage"
date:   2019-11-06
categories: blockchain
tags: ethereum rpc
---

One core challenge in the Ethereum ecosystem is accessing historical data.
Want to know what your token balance was last week?
Hopefully you've allocated [over 3TB of disk](https://etherscan.io/chartsync/chainarchive) to running an archive node, or else that data's likely been pruned.

Historical state pruning is no accident.
A full node's goal is to validate the current state of the network, which only requires applying transactions to the most recent state.
If your objective is to enable performant queries against previous states, it makes sense to use a tool that's specifically devoted to that task.

But how would one build such a tool?

**Events**

A common technique for caching historical data is track [events](https://solidity.readthedocs.io/en/latest/contracts.html#events).
During execution, contracts can emit events that provide a window into the contract's internal state at that point in time.
Full nodes currently retain all events indefinitely, and the [JSON RPC API](https://github.com/ethereum/wiki/wiki/JSON-RPC) enables subscribing to desired events in real time - so building a cache derived from events minimizes resource usage.

Unfortunately, this approach has several drawbacks.

For one, you're depending on contract authors to include all the data you desire in the events they emit.
If they decide that some data points are not worthy of events (and the gas costs they incur), then you're out of luck - your cache will have to exclude that data.

Additionally, there's no guarantee that the data in an event is accurate.
If you haven't inspected the contract's source code to verify that an event's payload is correctly derived from the expected storage slot(s), it could be coming from anywhere.

In many cases, an event doesn't even capture a contract's state per se, but rather the delta between old and new values.
In that case, your cache depends on capturing _every_ relevant event (with handling for events pruned by reorgs) and then correctly applying arithmetic to derive the product of the deltas.

Oh yeah, and node developers have [signaled](https://gist.github.com/karalabe/60be7bef184c8ec286fc7ee2b35b0b5b) that retaining all historical events may not remain viable - events may eventually be pruned along with previous states.

This is clearly not a very good situation.
Events can alert you to the fact that something happened, but they should not be considered a reliable proxy for reconstructing historical state.

So where does that leave us?

**Storage**

The place to go for accessing historical contract storage values is, wait for it... storage!
The aforementioned JSON RPC API includes a [function for accessing storage slots directly](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getstorageat).
If you query for data at a given storage slot, then you know that you're getting an accurate representation of the data you desire.
You can even [get proof](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getproof).

There are a couple of hurdles.
Accessing storage at previous blocks means running an archive node, and building up keys for storage slots in mappings and dynamic arrays is no picnic.

But, on the whole, if you want to build a reliable cache - this is currently the cost of doing business.
Looking forward to covering how you can minimize those costs and get your queries right in upcoming posts!
