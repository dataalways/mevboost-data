# MEV-Boost Winning Bid Data

This repository is a collection of [public domain](https://creativecommons.org/public-domain/cc0/) Ethereum [MEV-Boost](https://boost.flashbots.net/) winning bid data.

## Coverage

Block data is extracted using [cryo](https://github.com/paradigmxyz/cryo) leveraging an [Infura RPC](https://www.infura.io/), and merged with data from the following relays: 
- [Flashbots](https://boost-relay.flashbots.net/) 
- [ultrasound](https://relay.ultrasound.money/)
- [bloXroute regulated](https://bloxroute.regulated.blxrbdn.com/)
- [bloXroute max profit](https://bloxroute.max-profit.blxrbdn.com)
- [Agnostic](https://agnostic-relay.net/)
- [Aestus](https://mainnet.aestus.live/)
- [Manifold](https://mainnet-relay.securerpc.com/)
- [Titan](https://titanrelay.xyz/)

We then enrich the data with Flashbots's [public builder mapping](https://datasets.flashbots.net/) and with the mevboost.pics [proposer mapping](https://mevboost.pics/data.html). 

Data coverage begins on October 11, 2023. We may backfill more data in time.

The data is delivered in daily Parquet chunks, allowing for incremental bandwidth for users who choose to keep their datasets updated.

## Pandas import example

```python 
# Validated with python 3.13.1

import os
import pandas as pd  # pandas==2.2.1

base_path = './data/'
file_paths = os.listdir(base_path)
# this assumes that the data directory is in the working directory

dfs = []
for file in file_paths:
    df_tmp = pd.read_parquet(os.path.join(base_path, file))
    dfs.append(df_tmp)

df = pd.concat(dfs)
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
- `value_eth`: `float64`
- `base_fee_per_gas`: `float64`
- `gas_limit`: `float64`
- `gas_used`: `int64`
- `builder_pubkey`: `str`
- `relay`: `str`
- `bid_timestamp_ms`: `float64`
- `slot_time_ms`: `float64`
- `proposer_pubkey`: `str`
- `block_timestamp`: `float64`
- `extra_data`: `str`
- `builder_label`: `str`
- `proposer_label`: `str`
- `lido_node_operator`: `str`
