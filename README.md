# TLOSD

## Overview

TLOSD is a Telos USD stable coin backed by a pool of tokens from successful USD stablecoin projects. This repository contains to result of work carried out under the [Telos USD Stable Token](https://chainspector.io/governance/telos-works/5a2g1zy5r3vq) Telos Works proposal.

## Design

The system comprises four contracts;
* EOS Bridge - A bridge contract running on the EOS blockchain
* Telos Bridge - A bridge contract running on the EOS blockchain
* Token contract - The standard eosio.token contract, used for all wrapped and proxy tokens on the Telos blockchain.
* Swaps contract - A low price slippage token exchange contract with extensions to support the (USD stable) TLOSD and TLOSM (liquidity) tokens.

## System setup

The system will initially be built around the following existing tokens;
* [USDT](https://bloks.io/tokens/USDT-eos-tethertether) - USD Tether on EOS
* [EOSDT](https://bloks.io/tokens/EOSDT-eos-eosdtsttoken)

### Telos tokens

| Description | Contract     | Precision | Issuer       | Max supply                  |
|:----------- | ------------:| ---------:| ------------:| ---------------------------:|
| TLOSD       | tokens.swaps | 4         |   tlos.swaps |     1 000 000 000.0000      |
| TLOSM       | tokens.swaps | 8         |   tlos.swaps |     1 000 000 000.00000000  |
| USDT        | tokens.swaps | 4         | ???????????? | 1 000 000 000 000.0000      |
| EOSDT       | tokens.swaps | 9         | ???????????? |       170 000 000.000000000 |

### EOS tokens

| Description | Contract     | Precision | Issuer       | Max supply                  |
|:----------- | ------------:| ---------:| ------------:| ---------------------------:|
| USDT        | tethertether | 4         | - - - - - -  | 1 000 000 000 000.0000      |
| EOSDT       | eosdtsttoken | 9         | - - - - - -  |       170 000 000.000000000 |
| VIGOR       | vigortoken11 | 4         | - - - - - -  |   100 000 000 000.0000      |

### Conversion paths

| From            | USDT@telos | EOSDT@telos | TLOSD@telos | TLOSM@telos | USDT@eos  | EOSDT@eos |
| --------------- |:----------:|:-----------:|:-----------:|:-----------:|:---------:|:---------:|
| **USDT@telos**  |            | X           | X           | X           | X         |  X        |
| **EOSDT@telos** | X          |             | X           | X           | X         |  X        |
| **TLOSD@telos** | X          | X           |             | X           |           |           |
| **TLOSM@telos** | X          | X           | X           |             |           |           |
| **USDT@eos**    | X          | X           | X           | X           |           |           |
| **EOSDT@eos**   | X          | X           | X           | X           |           |           |

* (X) - Supported

### Memo formats

| From            | To          | Contract       | Memo                                             |
| --------------- | ----------- | -------------- | ------------------------------------------------ |
| **USDT@eos**    |             |                |                                                  |
| USDT@eos        | USDT@telos  | "EOS bridge"   | < telos account >@telos                          |
|                 |             |                | < telos account >@telos &#124; < memo >          |
| USDT@eos        | EOSDT@telos | "EOS bridge"   | tlosd.swaps@telos &#124; EOSDT@< telos account > |
| USDT@eos        | TLOSD@telos | "EOS bridge"   | tlosd.swaps@telos &#124; TLOSD@< telos account > |
| USDT@eos        | TLOSM@telos | "EOS bridge"   | tlosd.swaps@telos &#124; TLOSM@< telos account > |
| &nbsp;          |             |                |                                                  |
| **EOSDT@eos**   |             |                |                                                  |
| EOSDT@eos       | EOSDT@telos | "Telos bridge" | < telos account >@telos                          |
|                 |             |                | < telos account >@telos &#124; < memo >          |
| EOSDT@eos       | USDT@telos  | "Telos bridge" | tlosd.swaps@telos &#124; USDT@< telos account >  |
| EOSDT@eos       | TLOSD@telos | "Telos bridge" | tlosd.swaps@telos &#124; TLOSD@< telos account > |
| EOSDT@eos       | TLOSM@telos | "Telos bridge" | tlosd.swaps@telos &#124; TLOSM@< telos account > |
| &nbsp;          |             |                |                                                  |
| **USDT@telos**  |             |                |                                                  |
| USDT@telos      | EOSDT@telos | tlosd.swaps    | EOSDT                                            |
| USDT@telos      | TLOSD@telos | tlosd.swaps    | TLOSD                                            |
| USDT@telos      | TLOSM@telos | tlosd.swaps    | TLOSM                                            |
| USDT@telos      | USDT@eos    | "Telos bridge" | < eos account >@eos                              |
|                 |             |                | < eos account >@eos &#124; < memo >              |
| &nbsp;          |             |                |                                                  |
| **EOSDT@telos** |             |                |                                                  |
| EOSDT@telos     | USDT@telos  | tlosd.swaps    | USDT                                             |
| EOSDT@telos     | TLOSD@telos | tlosd.swaps    | TLOSD                                            |
| EOSDT@telos     | TLOSM@telos | tlosd.swaps    | TLOSM                                            |
| EOSDT@telos     | EOSDT@eos   | "Telos bridge" | < eos account >@eos                              |
|                 |             |                | < eos account >@eos &#124; < memo >              |
|                 |             |                |                                                  |

### Summary of accounts

| Description         | Mainnet      | Testnet      | Local node   |
|:------------------- | ------------:| ------------:|:------------:|
| Namespace           |        swaps |        swaps |        swaps |
| Admin               |  admin.swaps |  admin.swaps |  admin.swaps |
| &nbsp;              |              |              |              |
| **Telos**           |              |              |              |
| Bridge              | ???????????? | issuer.swaps | ???????????? |
| TLOSD Swaps         |  tlosd.swaps |        swaps |              |
| TLOSD issuer        |  tlosd.swaps |        swaps |              |
| TLOSM issuer        |  tlosd.swaps |        swaps |              |
| &nbsp;              |              |              |              |
| Pegged tokens       | tokens.swaps |    usd.swaps |              |
| Pegged token issuer | ???????????? | issuer.swaps | ???????????? |
| &nbsp;              |              |              |              |
| **EOS**             |              |              |              |
| Bridge              | ???????????? |    eos.swaps |    eos.swaps |
| USDT                | tethertether |   usdt.swaps |   usdt.swaps |
| EOSDT               | eosdtsttoken |  eosdt.swaps |  eosdt.swaps |

* "swaps" is the namespace owner. Using it as the swaps contract prevents the creation of new *.swaps accounts.

