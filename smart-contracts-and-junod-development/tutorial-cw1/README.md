---
cover: ../../.gitbook/assets/Gitbook Banner large 6 (1) (1) (1) (1) (1) (22).png
coverY: 0
---

# CW1 Tutorial

This tutorial will take you through compiling, storing and interacting with the CW1 subkeys contract in the[ cosmwasm-plus](https://github.com/CosmWasm/cosmwasm-plus) repo.

`cosmwasm-plus` contains production-grade contracts, whereas `cosmwasm-examples`, which we used in the previous tutorial, is optimised for readability and for those new to CosmWasm and/or smart contracts in general.

As before, this will take four steps. Unlike before, we have more options for executing commands once instantiated:

1. Write the contract (already done)
2. Store the contract on-chain
3. Instantiate the contract
4. Execute commands using the contract:
   1. As the admin key (A), set an allowance for a key (B)
   2. As the key with an allowance, send tokens to key (C)
   3. See tokens arrive at key (C)
   4. See allowance decrease for key (B)

There is a video available that walks through the steps in this tutorial. The versions have changed since the video, but the main difference is that you should run Juno in Docker on your laptop rather than building youself:

{% content-ref url="../junod-local-dev-setup.md" %}
[junod-local-dev-setup.md](../junod-local-dev-setup.md)
{% endcontent-ref %}

{% embed url="https://youtu.be/9yDaCklWlU0" %}
