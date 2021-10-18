# Community Verification Event for the Ethereum-compatible Version of PlatON and Alaya

[[中文]](https://github.com/AlayaNetwork/Developer-Events/blob/main/README-CN.md)

## Background

PlatON 1.1.1 and Alaya 0.16.1 will be fully compatible with Ethereum development tools to lower the cost of development and learning for developers who are participating in the network building of PlatON and Alaya. Relying on the many well-established tools and resources now available in the Ethereum ecosystem, developers can quickly expand their applications and engage in innovation on the PlatON and Alaya network that features privacy-preserving properties, high performance, and low service fees.

We will hold a one-month developer event for the adoption of Ethereum development tools and the verification of the migrated Ethereum applications to examine the compatibility, reliability, and user experience of Ethereum development tools on the PlatON and Alaya network. This event aims to improve the network’s compatibility with Ethereum tools and provide developers in our community with more development tools, guides, and samples.



## Overview

Hosted on the **Alaya development network** (updated to version 0.16.1), PlatON’s pilot network, the event will be open to all community developers or teams who are interested, without any threshold.

By migrating all kinds of DApps or smart contract samples in the Ethereum ecosystem onto the Alaya development network, developers can verify the compatibility and availability of the PlatON network in relation to the Ethereum development tools/resources during the entire process of migration and development (including the Alaya development tools used in this process). They can also submit and try to solve the problems they found and produce migration guides (document or video) for DApps, smart contracts, and development tools, including DApp migration codes/development tools that have already been adjusted for the Alaya network.

To get more inspirations, take a look at [Recommended Projects for the Event](https://github.com/AlayaNetwork/Developer-Events/blob/main/Recommended%20Projects%20for%20the%20Event.md).


## How to join the event

The whole process of the event will be conducted on GitHub in an open and transparent manner.

**[Sign up for the event]**

Before signing up for the event, please go to the [GitHub Repository](https://github.com/AlayaNetwork/Developer-Events) of the event and submit the relevant information through Issue.

Standard application format:

> **Title:**  GitHub username  - The name of the DApp/contract to be migrated or the name of the Ethereum development tool to be verified + migration verification
>
> **Content:**
>
> - Project Name:
> - Project repository
> - Development tools/resources used:
> - Scheduled completion date:
> - The expected content of delivery:
> - Contact info:

**Sample:**

> **Title:** Boney - Unis Migration verification
>
> **Content:**
>
> Project Name: Unis
>
> Project repository: https://github.com/Uniswap/swap-router-contracts
>
> Development tools/resources used: Remix，Truffle，MetaMask，Ethereum Web3.js
>
> Scheduled completion date: November 1, 2021
>
> The expected content of delivery: Document and video guides for Unis migration and the front-end code and contract code of Uniswap
>
> Contact info: https://t.me/Boneybi

*Note: Before signing up, please check the Issue of other participants, so that you will not be migrating or verifying the same DApp or development tool. Where the same DApp or development tool has been submitted, only the first two submissions (by valid delivery) will be approved and rewarded.*



**[DApp migration and verification of Ethereum development tools]**

You can start the migration/verification process by forking a personal repository of the DApp to be migrated or the development tool to be verified. During the migration/verification process, you should submit the incompatibility or bug of the PlayON/Alaya network and the development tools into the Issue used for signing up. We will provide bug bounty rewards for each valid feedback.

> Format of bug feedback:
>
> [Issue + sequence No.] Description of the bug

*Note: Valid feedback does not include known compatibility issues. For more information, please refer to the compatibility constraint specified in the below Notice.*



**[Submit the delivery]**

Participants will receive delivery rewards for the ultimate submission of the migration guide (document or video) regarding the corresponding case/tool. Please make sure that your delivery is complete and available. That is, the guide submitted should be complete and the DApp migrated and the Ethereum development tool involved should be adjusted for the Alaya development network. Each delivery will be assessed by a professional team of reviewers.

> Please submit your deliverables to this repository by PR, naming the file or folder with your `GitHub username - project name` and associating it with your sign-up Issue.



## Term of the event

Open for participation： **from October 19, 2021 to November 14, 2021**

Distribution of rewards: **Before November 18, 2021**



## Rewards

- **Bug bounty reward:** By submitting the problems of the PlatON/Alaya network or the development tools, the participant will receive rewards according to the problem’s level upon confirmation by our core team.

  | Reward (unit: USDT, to be converted into LAT upon distribution) | Level    |
  | ------------------------------------------------------------ | -------- |
  | 5,000                                                        | Blocker  |
  | 2,000                                                        | Critical |
  | 1,000                                                        | Normal   |
  | 500                                                          | Minor    |
  | 50                                                           | Trivial  |

- **Delivery reward:** Participants can receive LAT rewards of 500 USDT~5,000 USDT according to the delivery quality, subject to comprehensive assessments that will be primarily based on the difficulties involved in the migration and verification of applications, contracts, and tools, as well as the quality and reference value of the submitted migration guides.



## Notice

During this event, the migration of Ethereum DApps and the verification of Ethereum development tools need to be conducted on the Alaya development network. Please set up a local environment that complies with the requirements of the Alaya development network.

### Alaya development network

> Chain ID ：201030   
>
> RPC URL：http://47.241.91.2:6789  Or  ws://47.241.91.2:6790
>
> Currency Symbol：ATP

Resources related to the Alaya development network 

- Alaya document: https://alaya.network/alaya-devdocs/en/ 
- Join the Alaya development network: https://alaya.network/alaya-devdocs/en/Join_the_dev_network
- Alaya development network blockchain explorer: https://devnetscan.alaya.network/
- Alaya development network faucet: https://faucet.alaya.network/faucet/?id=f93426c0887f11eb83b900163e06151c
- Samurai (explorer plug-in wallet): https://alaya.network/alaya-devdocs/en/Samurai_user_manual
- PlatON Studio(IDE)：https://alaya.network/alaya-devdocs/en/IDE



### Compatibility constraint

**In terms of Solidity, the PlatON/Alaya network differs from Ethereum in the following aspects, which may require adjustments (as appropriate) when adopting Ethereum development tools in the network or when migrating Ethereum applications onto PlatON/Alaya.**

- Block timestamp: The unit of timestamps recorded by blocks in the Ethereum network is second, while PlatON and Alaya have adopted millisecond as the unit for block timestamp, and the return value obtained using now and block.timestamp also differs.
- Token unit: PlatON and Alaya have replaced Ethereum’s wei and ether with von and lat (atp).
- The format of account address: PlatON and Alaya support EIP55 as well as Bech32.
- The version of the Solidity compiler: The latest version of PlatON/Alaya now supports Solidity 0.8.6.



## Contact

For help or support, you can ask for support at [Discord](https://discord.gg/jAjFzJ3Cff) 、[Telegram](https://t.me/joinchat/LhO63AsZ_iozZGNl) or [Forum](https://forum.latticex.foundation/).

## License

This event is licensed under the [MIT license](https://github.com/AlayaNetwork/Developer-Events/blob/main/LICENSE.md)
