## 合约

环境
ubuntu 1804


## 准备条件

需要创建一个钱包，导出私钥，在每个合约仓库根目录有一个.env配置文件。修改私钥环境变量。去水龙头领测试的LAT

### 部署uniswap核心合约

```
$ git clone https://github.com/platon-uniswap/uniswap-v2-core.git
$ cd uniswap-v2-core
$ npm i
$ npx hardhat --network alaya_dev run scripts/01-deploy-factory.js // 部署工厂合约并得到地址
$ npx hardhat --network alaya_dev run scripts/02-init-code-hash.js // 得到initcodehash，用户计算pair的地址
$ npx hardhat --network alaya_dev run scripts/03-deploy-test-coin.js // 部署USDT测试合约地址
```

### 部署uniswap周边合约

```
$ git clone https://github.com/platon-uniswap/uniswap-v2-periphery.git
$ cd uniswap-v2-periphery
$ npm i
$ npx hardhat --network alaya_dev run scripts/01-deploy-weth.js // 部署WETH合约
$ npx hardhat --network alaya_dev run scripts/02-deploy-router.js // 修改factory和weth合约地址，部署router02合约
```

### 部署UNI和治理合约

```
$ git clone https://github.com/platon-uniswap/governance.git
$ cd governance
$ npm i
$ npx hardhat --network alaya_dev run scripts/01-deploy-uni.js
$ npx hardhat --network alaya_dev run scripts/02-deploy-timelock.js // 部署时间锁合约，预计算治理合约地址
$ npx hardhat --network alaya_dev run scripts/03-deploy-gov.js // 修改文件，替换前一步timelock合约地址，部署治理合约地址，若部署失败，需要从上一步重新开始
$ npx hardhat --network alaya_dev run scripts/06-propose.js // 修改发起提案，如增发UNI，需要将uni的owner设置为timelock先
```

### 部署流动性挖矿合约

```
$ git clone https://github.com/platon-uniswap/liquidity-staker.git
$ cd liquidity-staker
$ npm i
$ npx hardhat --network alaya_dev run scripts/01-deploy-factory.js // 编辑文件，修改奖励地址为上述UNI的地址
$ npx hardhat --network alaya_dev run scripts/02-deploy-staking.js // 编辑文件，替换factory合约地址为前一步生成的合约地址，替换stakingToken为WETHUSDT的pair地址。
$ npx hardhat --network alaya_dev run scripts/03-notify-reward.js // 给池子转UNI奖励代币。开始流动性挖矿


```