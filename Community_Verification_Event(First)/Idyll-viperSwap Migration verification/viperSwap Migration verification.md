## Pancakeswap 适配迁移 —— viperSwap Migration verification

### 1. 迁移环境：

Truffle v5.1.5 (此版本以下编译0.6.2solidity可能会报错)
Node v16.13.0

### 2. Pancake 源码简要分析

Pancake 智能合约由三个github项目组成。core，periphery，farm。提供了简洁的x-y-k自动做市商实现。代码主要由两部分组成：Core实现某个交易的Pair的管理逻辑，Periphery实现路由，即一个或者多个交易对的兑换逻辑。理解增加/抽取流动性以及swap操作。farm则提供了单币，双币挖矿的逻辑，包括其治理代币Cake Token。

https://github.com/pancakeswap/pancake-swap-core

https://github.com/pancakeswap/pancake-swap-periphery

https://github.com/pancakeswap/pancake-farm

pancake-swap-core 是单个swap逻辑，两两交易对提供流动性，称之为pool。pancake 由于 fork Uniswap 参照其核心思想reserve0*reserve1的乘积不变。每个交易对有一些基本属性：reserve0/reserve1以及total supply。reserve0/reserve1 是交易对的两种代币的储存量。total supply是当前流动性代币的总量。

pancake-swap-periphery属于token之间的路由，代币之间的兑换，在一个个swap的基础上构建服务。核心逻辑实现在Pancake Router01.sol中。称为Router，因为Periphery实现了“路由”，支持各个swap之间的连接。基本上实现了三个功能：1/ add liquidity（增加流动性）2/remove liqudity (抽取流动性) 3/ swap（交换）

pancake-farm 

https://github.com/abdullah-ming/viperFarm

核心合约称为GovernorAlpha。它包含创建和执行建议的所有逻辑。

 timelock (Timelock.sol) 。它包含 *delay* 和 *grace period* 表示从一个提案被接受到它可以被执行，需要等待多少天。

Cake代币合约本身

MasterChef实现了铸造新的Cake代币。这里的逻辑Pancake主要fork 了 [Sushiswap](https://github.com/sushiswap/sushiswap/blob/master/contracts/MasterChefV2.sol) 。这是创建Cake代币的唯一途径。它是通过在MasterChef内质押LP代币来实现铸币。MasterChef是由所有者控制。MasterChef 合约里指定了每个区块可以挖出的 Cake 个数，代码里为：`sushiPerBlock`（好像是 40 个）,然后，不同的 LP 按设定的比例来分 sushiPerBlock。

### 3. 合约部署

官方提供两种部署方式，一种安装Truffle，自定义部署。另一种通过PlatON Studio。**个人体验觉得用VS Code + Truffle 保证合约能正常编译，再用PlatON Studio部署效果最佳**

1). 安装Docker --> 选择Alaya开发网络 --> 导入钱包账户

2). 编译部署

1.编译

在viperSwap和viperfarm下分别执行

```
truffle compile
```

2.部署则是通过PlatON Studio

![image-20211115163404280](https://github.com/abdullah-ming/viperSwap/blob/master/images/image-20211115163404280.png)

![image-20211115145553066](https://github.com/abdullah-ming/viperSwap/blob/master/images/image-20211115145553066.png)

![image-20211115144423876](https://github.com/abdullah-ming/viperSwap/blob/master/images/image-20211115144423876.png)

### 4. Pancake-frontend 部署

```
git clone https://github.com/pancakeswap/pancake-frontend.git
```

大致对frontend代码了解之后，需要修改的有以下几点：

pancakeswap/sdk，在这里主要修改Chain ID，FACTORY_ADDRESS即ViperFactory的合约地址，以及PlatON地址和以太坊地址兼容 

拿到上个步骤部署的合约地址，在路径/pancake-frontend/node_modules/@pancakeswap/sdk/dist/constants.d.ts修改：
chain-ID，FACTORY_ADDRESS，INIT_CODE_HASH

在/pancake-frontend/src/config/下是合约地址和网络的相关配置

在/pancake-frontend/src/config/constants/networks.ts 则是网络相关配置。

将部署后的合约地址和config中相应的合约做替换。

如Router合约 0x10ED43C718714eb63d5aA57B78B54704E256024E，在BSC浏览器找到之后，替换为自己部署的Router合约`atp1yau850cwcd4uzfy0e3sz3w3df643lk3p0gwelv`在代码中做一个全局替换。

将网络改为Alaya测试网

```
sudo npm install -g yarn

yarn install

yarn build

yarn start
```
### 源码

https://github.com/abdullah-ming/viperFarm

https://github.com/abdullah-ming/viperSwap
