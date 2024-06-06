# MEV-Boost Winning Bid Data

This repository is a collection of [public domain](https://creativecommons.org/public-domain/cc0/) Ethereum [MEV-Boost](https://boost.flashbots.net/) winning bid data.

## Coverage

Block data is extracted using [cryo](https://github.com/paradigmxyz/cryo) leveraging the [Infura RPC](https://www.infura.io/), and merged with data from the following relays: 
- [Flashbots](https://boost-relay.flashbots.net/) 
- [ultrasound](https://relay.ultrasound.money/)
- [bloXroute regulated](https://bloxroute.regulated.blxrbdn.com/)
- [bloXroute max profit](https://bloxroute.max-profit.blxrbdn.com)
- [Agnostic](https://agnostic-relay.net/)
- [Aestus](https://mainnet.aestus.live/) (Data prior to block 19,530,000 available in backfill directory)
- [Manifold](https://mainnet-relay.securerpc.com/) (Data prior to block 19,530,000 available in backfill directory)
- [Eden](https://relay.edennetwork.io/info) (Data prior to block 19,530,000 available in backfill directory)
- [Titan](https://titanrelay.xyz/) (Data prior to block 19,530,000 available in backfill directory)

Data coverage begins on October 10, 2023 at block [18,320,000](https://beaconcha.in/block/18320000). We may backfill more data in time.

The data is delivered in Parquet chunks of 10,000 blocks, allowing for incremental bandwidth for users who choose to keep their datasets updated.

## Pandas import example

```python 
# Validated with python 3.11.6

import os
import pandas as pd  # pandas==2.1.2

base_path = './data/'
file_paths = os.listdir(base_path)
# this assumes that the data directory is in the working directory

dfs = []
for file in file_paths:
    df_tmp = pd.read_parquet(os.path.join(base_path, file))
    dfs.append(df_tmp)

dfs.append(os.path.join(base_path, 'minority-relay-backfill/backfill__minority__relays__blocks__18320000_to_19530000.parquet'))
# add in the backfill data from other relays.

df = pd.concat(dfs)

df = df[df['payload_delivered'] == True]
# drop undelivered payloads

df.sort_values(by=['block_number', 'bid_timestamp_ms'], ascending=True, inplace=True)
# double sorting by block_number and bid_timestamp_ms allows the data to stay 
# ordered inclusive of non-mev blocks missing bid_timestamp_ms data.

df.reset_index(inplace=True, drop=True)
```

## Data Schema

- `block_datetime`: `datetime64[ns]`
- `slot`: `int64`
- `block_number`: `int64`
- `block_hash`: `str`
- `value`: `str`
- `base_fee_per_gas`: `uint64`
- `gas_limit`: `float64`
- `gas_used`: `int64`
- `num_tx`: `int64`
- `builder_label`: `str`
- `builder_pubkey`: `str`
- `relay`: `str`
- `bid_timestamp_ms`: `float64`
- `slot_time_ms`: `float64`
- `proposer_pubkey`: `str`
- `proposer_mev_recipient`: `str`
- `optimistic_submission`: `bool`
- `builder_fee_recipient`: `str`
- `block_timestamp`: `uint32`
- `extra_data`: `str`
- `payload_delivered`: `bool`
