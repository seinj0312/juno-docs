---
description: Instructions for joining Juno networks with statesync
---

# Sync with state-sync

{% hint style="danger" %}
**THIS CONFIGURATION IS NOT CURRENTLY WORKING. SUPPORT IS IN DEVELOPMENT.**
{% endhint %}

## Introduction

State-sync is a module built into the Cosmos SDK to allow validators to rapidly join the network by syncing your node with a snapshot enabled RPC from a trusted block height.&#x20;

This greatly reduces the time required for a validator or sentry to sync with the network from days to minutes. The limitations of this are that there is not a full transaction history, just the most recent state that the state-sync RPC has stored. An advantage of state-sync is that the database is very small in comparison to a fully synced node, therefore using state-sync to resync your node to the network can help keep running costs lower by minimising storage usage.

By syncing to the network with state-sync, a node can avoid having to go through all the upgrade procedures and can sync with the most recent binary only.

{% hint style="info" %}
For nodes that are intended to serve data for dapps, explorers or any other RPC requiring full history, state-syncing to the network would not be appropriate.&#x20;
{% endhint %}

## Uni testnet state-sync

Kingnodes operate and maintain a snapshot RPC for the `uni` testnet network.&#x20;

{% hint style="info" %}
This documentation assumes you have followed the instructions for [Joining Testnets](joining-the-testnets.md) and [Setting up Cosmovisor](setting-up-cosmovisor.md).
{% endhint %}

The state-sync configuration is as follows:

```bash
# snapshot-interval specifies the block interval at which local state sync snapshots are
# taken (0 to disable). Must be a multiple of pruning-keep-every.
snapshot-interval = 2000

# snapshot-keep-recent specifies the number of recent snapshots to keep and serve (0 to keep all).
snapshot-keep-recent = 10
```

Set `SNAP_RPC` variable to the kingnodes snapshot RPC

```bash
SNAP_RPC="https://rpc.uni.kingnodes.com:443"
```

Fetch the `LATEST_HEIGHT` from the snapshot RPC, set the state-sync `BLOCK_HEIGHT` and fetch the `TRUST_HASH` from the snapshot RPC. The `BLOCK_HEIGHT` to sync is determined by subtracting the snapshot-interval from the `LATEST_HEIGHT`.&#x20;

```bash
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```

Check variables to ensure they have been set

```bash
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

# output should be something similar to:
# 1002969 1000969 0B532538F74C946B82D1697704B25F2D8E12D989766B30AF5F8730A7A7A94CDB
```

If you have not already, [set some persistent peers](joining-the-testnets.md#set-persistent-peers-1).

Set the required variables in `~/.juno/config/config.toml`

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.juno/config/config.toml
```

Stop the node and reset the node database

{% hint style="danger" %}
WARNING: This will erase your node database. If you are already running validator, be sure you backed up your `config/priv_validator_key.json` and `config/node_key.json` prior to running `unsafe-reset-all`.

It is recommended to copy `data/priv_validator_state.json` to a backup and restore it after `unsafe-reset-all` to avoid potential double signing.
{% endhint %}

```bash
sudo systemctl stop cosmovisor && cosmovisor unsafe-reset-all
```

Restart node and check logs

```bash
sudo systemctl restart junod && journalctl -u cosmovisor -f
```
