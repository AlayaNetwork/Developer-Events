# Prism迁移及验证过程

## 参考资料

- [以太坊DApp快速迁移教程](https://alaya.network/alaya-devdocs/zh-CN/DApp_migrate/)
- [迁移教程](https://devdocs.platon.network/docs/zh-CN/Solidity_Contract_Migrate/)

## 迁移思路
PlatON和Alaya网络升级后已高度兼容以太坊，大大减少了DAPP迁移的工作量。对Prism项目来说，只有涉及到时间戳的部分代码需要迁移。

- 合约代码
	-  AssetPrice.sol 该合约负责从预言机中获取各资产的价格，其中maxDelayTime记录着报价的最大延迟时间，该参数数值为外部设置，合约无需修改。

		```plain
		    function setMaxDelayTime(uint256 time) external onlyOwner {
		        emit MaxDelayTimeChanged(maxDelayTime, time);
		        maxDelayTime = time;
		    }
		```

	-  Setting.sol 该合约负责保存系统的公用参数，这些参数数值为外部设置，合约无需修改。

		```plain
		    function setMintPeriodDuration(uint256 time) external onlyOwner {
		        emit SettingChanged(MINT_PERIOD_DURATION, bytes32(0), Storage().getUint(MINT_PERIOD_DURATION), time);
		        Storage().setUint(MINT_PERIOD_DURATION, time);
		    }
		```

	-  SupplySchedule.sol 该合约负责计算代币发行量，在PlatON网络中，365 days 的计算结果单位是秒需要转换为毫米
		
		```plain
		    function _getYearlyPeriods() private view returns (uint256) {
		        uint256 year = 365 days * 1000;
		        return year.div(Setting().getMintPeriodDuration());
		    }
		```

- 部署代码
	-  4_deploy_asset_price.js 部署该合约时，需要将数值设置为毫秒

		```plain
		    await deployer.deploy(AssetPrice);
		    let assetPrice = await AssetPrice.deployed();
		    await assetPrice.setMaxDelayTime(3600 * 3 * 1000);
		```

	-  5_deploy_setting.js 部署该合约时，需要将数值设置为毫秒

		```plain
			await deployer.deploy(Setting);
			let setting = await Setting.deployed();
			await setting.setMintPeriodDuration(3600 * 24 * 7 * 1000);
		```

	-  6_deploy_supply_schedule.js 部署该合约时，需要将开始时间设置为毫秒

		```plain
			let startTime = 1635696000 * 1000;
			await deployer.deploy(SupplySchedule, resolver.address, startTime, 0);
		```

## 编译合约

可以直接使用原始版本的 truffle进行编译

```plain
truffle compile
```

## 部署合约

```plain
truffle migrate
```

```plain
   Deploying 'AssetPrice'
   ----------------------
   > transaction hash:    0x2392d5e93c44386169c78eed7bcedb508853cc0e59901872e7771f3a12630fe0
   > Blocks: 3            Seconds: 4
   > contract address:    0xfb680D29f84C64337bE810AA495fEb30E75C93F0
   > block number:        19071476
   > block timestamp:     1636346030492
   > account:             0x8e8289E2c74F1Bc35fD96F0F8A9DB9381ec32863
   > balance:             209.98225854
   > gas used:            2001301 (0x1e8995)
   > gas price:           1 gwei
   > value sent:          0 ETH
   > total cost:          0.002001301 ETH

      Deploying 'Setting'
   -------------------
   > transaction hash:    0x1314500a8aaac5b55df03c6a25d5630c2d2ef985d9541779e4ba01f103a3c6af
   > Blocks: 4            Seconds: 4
   > contract address:    0x243d6DefDDAc986b6a72a5DaE02A3Cf9E0Ee430A
   > block number:        19071735
   > block timestamp:     1636346317947
   > account:             0x8e8289E2c74F1Bc35fD96F0F8A9DB9381ec32863
   > balance:             209.980308617
   > gas used:            1846642 (0x1c2d72)
   > gas price:           1 gwei
   > value sent:          0 ETH
   > total cost:          0.001846642 ETH

   Deploying 'SupplySchedule'
   --------------------------
   > transaction hash:    0x761be97a2fd25cb7a2822e9d9a335860e61124c1060db27f86eca92b0103711e
   > Blocks: 5            Seconds: 4
   > contract address:    0x0E4D0E79a40965419658A23a88115d6162FAa059
   > block number:        19072855
   > block timestamp:     1636347817062
   > account:             0x8e8289E2c74F1Bc35fD96F0F8A9DB9381ec32863
   > balance:             209.975983876
   > gas used:            2649712 (0x286e70)
   > gas price:           1 gwei
   > value sent:          0 ETH
   > total cost:          0.002649712 ETH

```

## 验证

欢迎大家到[https://prisma.one/](http://prisma.one/)测试项目。
