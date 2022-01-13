# Swap Interface

website：https://devnet-swap.ddex.cc/

code： https://github.com/platon-uniswap/interface



## To Start Development

#### Git clone

~~~bash
$ git clone https://github.com/platon-uniswap/info.git
~~~

#### Installing dependencies

```bash
$ yarn
```

#### Running locally

```bash
$ yarn start
```



## 前端代码修改与打包

### 安装nodejs

通过[node官网](https://nodejs.org/)下载安装最新的node稳定版本

### 修改代码

#### 依赖包修改并发布

* 包名：uniswap-sdk

* 源码地址: [https://github.com/fujianlian/uniswap-sdk/tree/platon](https://github.com/fujianlian/uniswap-sdk/tree/platon)

* 修改位置：

  - src/constants.ts (28行处)

  ```javascript
  export const FACTORY_ADDRESS = '0x154b2f6202a5033Cb150931177B86a2B03E485dF'
  export const INIT_CODE_HASH = '0x1892ee6b3b8f653471529d0b06a772bdc5588bb0b15607cb427c8148f70004a9'
  ```

  将上面的FACTORY_ADDRESS和INIT_CODE_HASH替换成新部署生成的对应的数据

  - src/entities/token.ts(95行)

  ```javascript
  [ChainId.PLATON_TESTNET]: new Token(
      ChainId.PLATON_TESTNET,
      '0xA838575b75a8b7C4A086a656F2d589eFb03109D6',
      18,
      'WLAT',
      'Wrapped LAT'
   ),
  ```
  

将上面的lat地址替换成新部署生成的WLAT代币地址

* 直接替换包

  运行npm i 安装sdk依赖，再运行npm run build编译包，编译成功后将生成的diast文件替换 PlatON-Swap 项目 node_nodules/@platon-swap/uniswap-sdk 下的 dist 文件
  
* 重新发布该包

  修改package.json中的版本号, 然后运行npm i 安装sdk依赖，再运行npm run build编译包，编译成功后运行npm publish --access public发布
  
  

#### 前端代码修改

##### 针对流动性挖矿，如果已经部署了staking合约，则代码需要添加对应流动性相关的代码，这里以ETH-PUSDT交易对为例

+ src/constants/index.ts(24行)

```javascript
export const PUSDT = new Token(ChainId.PLATON_TESTNET, '0x6A04AcbD67f6878A1Cc3DdDf7ACc45afA97057b1', 6, 'USDT', 'Tether USD')
```

   修改或增加对应代币的相关信息

+ src/state/stake/hooks.ts

```javascript
export const STAKING_GENESIS = 1600744959（11行）

export const REWARDS_DURATION_DAYS = 60(13行)
```

增加对应的交易对信息以及交易对流动性挖矿对应的staking合约地址

同时需要给Staking合约转对应的激励代币到合约账户，然后运行notifyRewardAmount接口将合约账户的激励代币生效



##### 其他和流动性挖矿无关的必要修改点

- src/constants/index.ts(第7/44/51/58行)

```javascript
export const ROUTER_ADDRESS = '0x2d51fd37f175984603A7d6C4758b27D09A1B2227'

export function getUniAddress(chainId: ChainId): string {
  if (isPlatOnChains(chainId)) {
    return '0xDFA36286675c8a03050b63F23D79786A067E0d24'
  }
  return '0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984'
}

export function getGovernAddress(chainId: ChainId): string {
  if (isPlatOnChains(chainId)) {
    return '0x7157e63BcAcfDa09Eb1C33Da88A0D0Bdc2880823'
  }
  return '0x5e4be8Bc9637f0EAA1A755019e06A68ce081D58F'
}

export function getTimelock(chainId: ChainId): string {
  if (isPlatOnChains(chainId)) {
    return '0xb90a71E184454F0eb544BD5e135cD093037649b4'
  }
  return '0x1a9C8182C09F50C8318d769245beA52c32BE35BC'
}
```

将上面的ROUTER_ADDRESS替换成新部署生成的router, 治理, timelock, 治理代币UNI合约地址

- src/constants/multicall/index.ts(第10行)

```javascript
[ChainId.PLATON_TESTNET]: '0xb80D5C1DE16301e57a13F19e2545E9813A4C2681',
```

将上面的lat地址替换成新部署生成的multicall合约地址

- src/state/stake/hooks.ts(第40行)

```javascript
[ChainId.PLATON_TESTNET]: [
    {
      tokens: [WETH[ChainId.PLATON_TESTNET], PUSDT],
      stakingRewardAddress: '0x4d7ec635A8047A2B90206264220DB2a655BDCEd8'
    }
]
```

将上面的stakingRewardAddress地址替换成新部署生成的stakingReward合约地址

- src/constants/list.ts(第15行)

```javascript
const GEMINI_LIST = 'https://raw.githubusercontent.com/fujianlian/interface/main/public/main.json'
```

这个默认的json列表所在的URL地址需要替换成可以访问到token-list.json文件的URL地址

- public/token-list.json

```
{
  ...
  "tokens":[
     {
            "name": "Platon Wrapped ETH",
            "chainId": 210309,
            "symbol": "WETH",
            "decimals": 18,
            "address": "0xA838575b75a8b7C4A086a656F2d589eFb03109D6",
            "logoURI": "https://raw.githubusercontent.com/Uniswap/assets/master/blockchains/ethereum/assets/0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2/logo.png"
        },
        {
            "name": "USDT",
            "chainId": 210309,
            "symbol": "USDT",
            "decimals": 6,
            "address": "0x6A04AcbD67f6878A1Cc3DdDf7ACc45afA97057b1",
            "logoURI": "https://ethereum-optimism.github.io/logos/USDT.png"
        },
        {
            "name": "UNI-V2",
            "chainId": 210309,
            "symbol": "UNI-V2",
            "decimals": 18,
            "address": "0xE5177055644DF9e2FA47246EA0973dE2c11AE730",
            "logoURI": "https://gemini.com/images/currencies/icons/default/uni.svg"
        },
        {
            "name": "UNI",
            "chainId": 210309,
            "symbol": "UNI",
            "decimals": 18,
            "address": "0xDFA36286675c8a03050b63F23D79786A067E0d24",
            "logoURI": "https://gemini.com/images/currencies/icons/default/uni.svg"
        }
  ],
  ...
}
```

将token-list.json文件修改，__WETH地址替换成新部署的代币合约地址，其他代币地址可以自己增加或者删除即可__，logoURI替换成可以访问的代币符号图片链接

### 编译打包

1. 运行`npm i`安装依赖包，请检查@platon-swap/uniswap-sdk的依赖包版本是否正确。

2. 运行`npm run build`命令编译项目。

3. 使用下面的命令把build目录下面的文件打包：

```shell
$ cd build
$ tar -zcvf dist.tar.gz *
```



## 前端包部署

### Github.io部署

~~~bash
$ yarn depoly
~~~

成功后访问 https://username.github.io/interface 即可，username为GitHub的用户名

### Nginx部署

* 在nginx目标机器，新建web目录，假设全路径为/home/web，通过下面的命令解压dist.tar.gz 到web目录：

  ```
  tar -zxvf dist.tar.gz -C ./web
  ```

* 前端包需要部署到nginx，nginx参考如下配置：

  ```
  server {
          listen 8080; ##监听端口
          server_name  10.1.1.50; ##服务器ip 根据实际情况替换
          client_max_body_size  20m;
          charset utf-8;
          
          #access_log  logs/host.access.log  main;
          
          location / {
              add_header 'Access-Control-Allow-Origin' '*';
              add_header 'Access-Control-Allow-Credentials' 'true';
              add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
              add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
              root   /home/web; ##假设目标机器存放项目文件位置 根据实际情况替换
              index  index.html index.htm;
              try_files $uri $uri/ /index.html;
          }   
          
          location /api { ##/api是项目文件中.env.production文件下的REACT_APP_NETWORK_URL 作为请求转发的标记，根据实际情况替换，请确保与项目中一致
              proxy_pass http://10.1.1.50:6789; ##“节点搭建”步骤目标节点的地址
              #proxy_set_^_header Host $host:$server_port;
             #proxy_set_header Host $host;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection "upgrade";
              proxy_send_timeout 12s;
              proxy_read_timeout 60s;
              proxy_connect_timeout 10s;
          }
          error_page   500 502 503 504  /50x.html;
          location = /50x.html {
              root   html;
          }   
      }
  ```

* nginx重新reload：

  ```shell
  $ nginx -s reload
  ```

