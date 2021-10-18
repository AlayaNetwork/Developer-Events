# PlatON|Alaya 以太坊兼容版本社区验证活动

[[English]](https://github.com/AlayaNetwork/Developer-Events)

## 背景

为了降低开发者参与PlatON及Alaya网络建设的开发成本和学习成本，PlatON 1.1.1版本和Alaya0.16.1版本将开始全面兼容以太坊生态工具。利用现有成熟丰富的以太坊生态工具和资源，开发者能够在具有隐私特性，高性能和低廉手续费的PlatON和Alaya网络上快速进行应用拓展和创新实践。

我们将举办了为期1个月的以太坊生态工具/应用移植验证开发者活动，以此验证PlatON和Alaya网络对以太坊生态工具的兼容性、可靠性和体验性。期待通过本次活动完善和提升对以太坊生态工具兼容性，并为社区开发者提供更多丰富可用的开发工具、教程和参考示例。



## 活动概述

本次活动将在PlatON的先行网 **Alaya开发网**（已升级到Alaya0.16.1版本）上开展，任何感兴趣的社区开发者或团队都可以参加，没有参与门槛限制。

开发者可以通过将以太坊生态内各种类型DApp或智能合约示例移植到Alaya开发网络上，以此验证整个移植开发过程中PlatON网络对各项以太坊生态工具/资源的兼容性与可用性（包含使用的Alaya生态开发工具），反馈发现的问题并尝试解决，并输出DApp、智能合约、开发工具的移植操作指南（文档或视频形式），包含已适配Alaya网络的DApp移植代码/开发工具。

想要获取更多灵感，请查看 [Recommended Projects for the Event](https://github.com/AlayaNetwork/Developer-Events/blob/main/Recommended%20Projects%20for%20the%20Event-CN.md)。



## 参与方式

本次活动所有过程将在GitHub上以公开透明的方式开展。

**【活动报名】**

参与报名的用户，请先进入本次活动的[GitHub仓库](https://github.com/AlayaNetwork/Developer-Events)，通过Issue方式提交您的报名信息。

报名信息规范：

> **标题**：GitHub用户名 - 移植的DApp/合约名称或要验证的以太坊开发工具名称 + 移植验证
>
> **内容**：
>
> - 项目名：
> - 项目仓库：
> - 使用到的开发工具或资源：
> - 计划完成时间：
> - 预计交付内容：
> - 联系方式：

**参考示例**：

> **标题**：Boney- Unis 移植验证
>
> **内容**：
>
> 项目名：Unis
>
> 项目仓库：https://github.com/Uniswap/swap-router-contracts
>
> 使用到的工具或资源：Remix，Truffle，MetaMask，Ethereum Web3.js
>
> 计划完成时间：2021/11/01
>
> 预计交付内容：Unis移植文档教程，视频指导教程，Uniswap移植前端代码和合约代码
>
> 联系方式：https://t.me/Boneybi

*注：提交报名前，请查阅所有已报名Issue，避免与其他用户移植或验证相同的DApp应用或工具，本活动仅采纳和奖励最早且有效交付的前2位用户。*



**【DApp移植与以太坊生态工具验证】**

将需要移植的DApp或待验证的开发工具 Fork进您的个人仓库以此开始您的移植和验证工作，移植验证过程中将发现的PlatON/Alaya网络和生态工具的不兼容性或bug问题提交至您的活动报名Issue内。每个有效反馈问题都将会获得本次活动的bug bounty奖励。

> 问题反馈格式：
>
> 【Issue+序号】问题描述

*注：有效反馈问题不包含已知的兼容性问题，见下方注意事项中的兼容约束说明。*



**【提交交付成果】**

用户最终输出对应案例/工具的移植使用操作教程（文档或视频形式）将会获得活动的交付奖励。请注意确保交付成果具备完整性和可用性（教程完整，移植的案例和使用到的以太坊生态工具已适配Alaya开发网），每个交付成果都将会有专业评审团队进行审核确认。

> 请将交付成果通过PR方式提交至本仓库，以`GitHub用户名-项目名` 命名交付文件或文件夹，并关联对应报名Issue。



## 活动时间

参与时间：**2021/10/19~2021/11/14**

奖励发放时间：**2021/11/28前**



## 活动奖励

- **Bug Bounty奖励**：提交PlatON/Alaya网络或生态工具存在的问题，经核心团队确认后，将按照问题等级进行奖励。

  | 问题等级     | 奖励（单位U，实际发放为折算后的LAT） |
  | ------------ | ------------------------------------ |
  | Blocker致命  | 5000                                 |
  | Critical严重 | 2000                                 |
  | Normal一般   | 1000                                 |
  | Minor轻微    | 500                                  |
  | Trivial建议  | 50                                   |

- **交付奖励**：按照交付质量可获得500U~5000U等值的LAT奖励，主要评价标准将基于应用，合约及工具的移植及验证难度，移植教程输出质量和参考价值进行综合评估。



## 注意事项

本次活动所有以太坊DApp移植和开发工具验证需要在Alaya开发网络上进行，请注意配置Alaya开发网的网络环境。

#### Alaya开发网

> Chain ID ：201030   
>
> RPC URL：http://47.241.91.2:6789  Or  ws://47.241.91.2:6790
>
> Currency Symbol：ATP

Alaya开发网资源

- Alaya文档: https://alaya.network/alaya-devdocs/en/ 
- Alaya开发网接入：https://alaya.network/alaya-devdocs/zh-CN/Join_the_dev_network
- Alaya开发网区块链浏览器: https://devnetscan.alaya.network/
- Alaya开发网水龙头：https://faucet.alaya.network/faucet/?id=f93426c0887f11eb83b900163e06151c
- Samurai（浏览器钱包插件）：https://alaya.network/alaya-devdocs/zh-CN/Samurai_user_manual
- PlatON Studio(IDE)：https://alaya.network/alaya-devdocs/zh-CN/IDE



#### 兼容性约束

**以太坊与PlatON/Alaya网络存在以下Solidity差异，在进行以太坊生态工具或应用移植到PlatON/Alaya过程中可能需要根据实际情况进行适配。**

- 区块时间戳：以太坊区块中记录的timestamp单位为秒，PlatON和Alaya区块记录的timestamp单位为毫秒，使用now、block.timestamp获得的返回值会有不同。
- token的单位：PlatON和Alaya使用von、lat(atp) 代替了以太坊的wei、ether。
- 账户地址格式：PlatON和Alaya同时支持EIP55和Bech32地址格式。
- Solidity编译器版本：当前PlatON/Alaya最新版本已支持到0.8.6。



## 联系

如需帮助或支持，请加入我们的开发者社区[Discord](https://discord.gg/jAjFzJ3Cff)、[Telegram](https://t.me/joinchat/LhO63AsZ_iozZGNl)或[Forum](https://forum.latticex.foundation/)。



## License

This event is licensed under the [MIT license](https://github.com/AlayaNetwork/Developer-Events/blob/main/LICENSE.md)
