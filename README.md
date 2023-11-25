# MEV-Boost Winning Bid Data

This repository is a collection of [public domain](https://creativecommons.org/public-domain/cc0/) Ethereum [MEV-Boost](https://boost.flashbots.net/) winning bid data.

## Coverage

Block data is extracted using [cryo](https://github.com/paradigmxyz/cryo) leveraging the [Merkle RPC](https://freerpc.merkle.io/), and merged with data from the following relays: 
- [Flashbots](https://boost-relay.flashbots.net/) 
- [ultrasound](https://relay.ultrasound.money/)
- [bloXroute regulated](https://bloxroute.regulated.blxrbdn.com/)
- [bloXroute max profit](https://bloxroute.max-profit.blxrbdn.com)
- [Agnostic](https://agnostic-relay.net/)

Data coverage begins on October 10, 2023 at block [18,320,000](https://beaconcha.in/block/18320000). We may backfill more data in time.

The data is delivered in Parquet chunks of 10,000 blocks, allowing for decreased bandwidth for users who choose to keep their datasets updated.

## Limitations

Currently, the dataset only compares builder bid hashes against the block hash written to the chain. It contains false positives where multiple relays received the winning block but did not all deliver it to the block proposer.

This has implications for relative relay market share but not for total MEV-Boost statistics. We will backfill this data in time, and present an empty `payload_delivered` column in the data schema in the meantime.

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
- `payload_delivered`: `<NA>`
