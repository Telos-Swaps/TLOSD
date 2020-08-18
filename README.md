# TLOSD

## Overview

TLOSD is a Telos USD stable coin backed by a pool of tokens from successful USD stablecoin projects. This repository contains to result of work carried out under the [Telos USD Stable Token](https://chainspector.io/governance/telos-works/5a2g1zy5r3vq) Telos Works proposal.

## Design

The system comprises four contracts;
* EOS Bridge - A bridge contract running on the EOS blockchain (EOS account [telosd.io](https://bloks.io/account/telosd.io))
* Telos Bridge - A bridge contract running on the EOS blockchain (Telos account [telosd.io](https://telos.bloks.io/account/telosd.io))
* Token contract - The standard eosio.token contract, used for all wrapped and proxy tokens on the Telos blockchain (Telos account [tokens.swaps](https://telos.bloks.io/account/tokens.swaps))
* Swaps contract - A low price slippage token exchange contract with extensions to support the (USD stable) TLOSD and TLOSM (liquidity) tokens (Telos account [telosd.swaps](https://telos.bloks.io/account/telos.swaps))

## System setup

The system will initially be built around the following existing tokens;
* [USDT](https://bloks.io/tokens/USDT-eos-tethertether) - USD Tether on EOS
* [EOSDT](https://bloks.io/tokens/EOSDT-eos-eosdtsttoken)

### Telos tokens

| Description | Contract     | Precision | Issuer       | Max supply                  |
|:----------- | ------------:| ---------:| ------------:| ---------------------------:|
| TLOSD       | tokens.swaps | 4         | telosd.swaps |     1 000 000 000.0000      |
| TLOSM       | tokens.swaps | 8         | telosd.swaps |     1 000 000 000.00000000  |
| USDT        | tokens.swaps | 4         |    telosd.io | 1 000 000 000 000.0000      |
| EOSDT       | tokens.swaps | 9         |    telosd.io |       170 000 000.000000000 |

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

| Description          | Mainnet      | Testnet       | Local node   |
|:-------------------- | ------------:| -------------:|:------------:|
| **Telos**            |              |               |              |
| Namespace            |        swaps |         swaps |        swaps |
| Telos admin          |  admin.swaps |   admin.swaps |  admin.swaps |
| &nbsp;               |              |               |              |
| Bridge               |    telosd.io |     telosd.io |    telosd.io |
| TLOSD swaps          | telosd.swaps |  telosd.swaps | telosd.swaps |
| TLOSD issuer         | telosd.swaps |  telosd.swaps | telosd.swaps |
| TLOSM issuer         | telosd.swaps |  telosd.swaps | telosd.swaps |
| &nbsp;               |              |               |              |
| Wrapped tokens       | tokens.swaps |  tokens.swaps | tokens.swaps |
| Wrapped token issuer |    telosd.io |     telosd.io |    telosd.io |
| &nbsp;               |              |               |              |
| UI onchain config    | config.swaps |  config.swaps | config.swaps |
| &nbsp;               |              |               |              |
| **EOS**              |              |               |              |
| EOS admin            | telosdcpunet |   admin.swaps | telosdcpunet |
| &nbsp;               |              |               |              |
| Bridge               |    telosd.io |  tlosd.eos.io |    telosd.io |
| &nbsp;               |              |               |              |
| USDT                 | tethertether |  tethertether | tethertether |
| EOSDT                | eosdtsttoken |  eosdtsttoken | eosdtsttoken |

* "swaps" is the namespace owner. Using it as the swaps contract prevents the creation of unauthorised *.swaps accounts.

