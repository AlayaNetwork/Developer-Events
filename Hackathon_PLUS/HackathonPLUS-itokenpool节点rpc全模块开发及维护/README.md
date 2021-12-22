# PlatON-Go-SDK开发报告

本项目完成的相关信息如下:

## 节点rpc模块: 使用节点rpc进行链上信息获取 

### HTTPProvider: https://rpc.alayascan.com
### WebsocketProvider: wss://ws.alayascan.com

项目关联的Issue: [HackathonPLUS-itokenpool节点rpc全模块开发及维护](https://github.com/AlayaNetwork/Developer-Events/issues/16).

## 使用说明及文档

API文档参考:

[JSON](http://json.org/) 是一种轻量级的数据交换格式。它可以表示数字，字符串，值的有序序列以及名称/值对的集合。

[JSON-RPC](http://www.jsonrpc.org/specification) 是一种无状态的轻量级远程过程调用(RPC)协议。首先，本规范定义了几种数据结构及其处理规则。它与传输无关，因为可以在同一过程中，通过套接字，通过HTTP或在许多各种消息传递环境中使用这些概念。它使用JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) 作为数据格式。

## JavaScript API

要从JavaScript应用程序内部与PlatON节点通信，请使用 [web3.js](https://github.com/AlayaNetwork/client-sdk-js) 库，该库为RPC方法提供了方便的接口。

## 注意

PlatON在升级到1.1.1版本之后，通过[兼容以太坊](https://github.com/PlatONnetwork/PIPs/blob/master/PIPs/PIP-2.md)扩展了 JSON-RPC 2.0，对 request 请求对象增加 bech32 字段，Booleans 类型。bech32 为 true 表示此次 rpc 调用中地址部分的编解码格式为 bech32，默认为 EIP55。并且支持了以太坊的RPC调用，[参考](https://geth.ethereum.org/docs/rpc/ns-eth).

下面仅显示带有curl过程的RPC调用过程。实际上，您需要根据服务器的具体情况进行一些调整。例如，PlatON的可能调用过程是 `curl -X POST -H 'content-type: application/json' --data '{"jsonrpc":"2.0","bech32":true,"method":"web3_clientVersion","params":[],"id":67}' -H 'content-type: application/json'  https://rpc.alayascan.com`.

## JSON RPC API参考

#### admin_startWS

startWS 启动一个基于 WebSocket 的 JSON RPC API 网络服务来处理客户端请求. 

##### 参数
所有参数是可选的:
1. host: 监听的网络地址 (默认值是 "localhost")
2. port: 监听的端口号 (默认值是 8546)
3. cors: 要使用的跨源资源共享标头 (默认值是 "")
4. apis: 通过此接口提供的 API 模块 (默认值是 "platon,net,web3")

##### 返回

`Boolean` - 该方法返回一个布尔标志，指定是否打开了 WebSocket RPC 服务器。请注意，任何时候都只允许一个 WebSocket 端点处于活动状态。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_startWS","params":[host, port, cors, apis],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### admin_stopWS

stopWS 方法关闭当前打开的 WebSocket RPC 服务。

##### 参数
none

##### 返回

`Boolean` -  服务是否关闭.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_stopWS","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***


#### admin_startRPC

startRPC 启动一个基于 WebSocket 的 HTTP RPC API 网络服务来处理客户端请求. 

##### 参数
所有参数是可选的:
1. host: 监听的网络地址 (默认值是 "localhost")
2. port: 监听的端口号 (默认值是 8546)
3. cors: 要使用的跨源资源共享标头 (默认值是 "")
4. apis: 通过此接口提供的 API 模块 (默认值是 "platon,net,web3")

##### 返回

`Boolean` - 该方法返回一个布尔标志，指定是否打开了 HTTP RPC 服务器。请注意，任何时候都只允许一个 HTTP 服务处于活动状态.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_startRPC","params":[host, port, cors, apis],"id":1}
// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### admin_stopRPC

stopRPC 方法关闭当前打开的 HTTP RPC 服务。

##### 参数
none

##### 返回

`Boolean` -  服务是否关闭.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_stopRPC","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### admin_removePeer

如果连接存在，则与远程节点断开连接.

##### 参数
`string` -  要删除的对等节点的 enode URL.

##### 返回

`boolean` -  远程节点是否被移除.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_removePeer","params":["enode://f59c0ab603377b6ec88b89d5bb41b98fc385030ab1e4b03752db6f7dab364559d92c757c13116ae6408d2d33f0138e7812eb8b696b2a22fe3332c4b5127b22a3@127.0.0.1:30304"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### admin_exportChain

将当前区块链导出到本地文件.

##### 参数
`string` -  文件需要导出的位置.

##### 返回

`boolean` -  是否导出成功.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_exportChain","params":["/home/develop/blockchain.gz"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### admin_importChain

从本地文件导入区块链.

##### 参数
`string` -  需要导入文件的位置.

##### 返回

`boolean` -  是否导入成功.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_importChain","params":["/home/develop/blockchain.gz"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### web3_clientVersion

返回当前客户端版本。

##### 参数
none

##### 返回

`String` - 当前的客户端版本

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"web3_clientVersion","params":[],"id":67}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "Mist/v0.9.3/darwin/go1.4.1"
}
```

***

#### web3_sha3

返回给定数据的Keccak-256(*不是*标准化的SHA3-256)。

##### 参数

1. `String` - 要转换为SHA3哈希的数据。

```js
params: [
  '0x68656c6c6f20776f726c64'
]
```

##### 返回

`DATA` - 给定字符串的SHA3结果。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":64}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

***

#### net_version

返回当前的网络协议版本。

##### 参数
none

##### 返回

`String` - 当前的网络协议版本。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"net_version","params":[],"id":67}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "59"
}
```

***

#### net_listening

如果客户端正在积极侦听网络连接，则返回`true`。

##### 参数
none

##### 返回

`Boolean` - 侦听时为`true`, 否则为 `false`.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"net_listening","params":[],"id":67}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result":true
}
```

***

#### net_peerCount

返回当前连接到客户端的节点数量。

##### 参数
none

##### 返回

`QUANTITY` - 连接的节点数。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"net_peerCount","params":[],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": "0x2" // 2
}
```

***

#### platon_protocolVersion

返回当前的PlatON协议版本

##### 参数
none

##### 返回

`String` - 当前的PlatON协议版本。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_protocolVersion","params":[],"id":67}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "54"
}
```

***

#### platon_syncing

这个属性是只读的。如果正在同步，返回同步对象。否则返回`false`。


##### 参数
none

##### 返回

`Object|Boolean`, 如果节点还没有与网络同步，返回false，否则返回具有以下属性的同步对象：

  - `startingBlock`: `Number` - 同步开始区块号。
  - `currentBlock`: `Number`- 节点当前正在同步的区块号。
  - `highestBlock`: `Number`- 预估要同步到的区块。
  - `knownStates`: `Number` - 预计要下载的状态数据。
  - `pulledStates`: `Number` - 已经下载的状态数据。

##### 例子
```js
// Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"platon_syncing","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": {
    startingBlock: '0x384',
    currentBlock: '0x386',
    highestBlock: '0x454',
    knownStates: "0x0",
    pulledStates: "0x0"
  }
}
// Or when not syncing
{
  "id":1,
  "jsonrpc": "2.0",
  "result": false
}
```

***

#### platon_gasPrice

查询gasPrice。

##### 参数
none

##### 返回

`QUANTITY` - 返回以von为单位的gasPrice。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_gasPrice","params":[],"id":73}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":73,
  "jsonrpc": "2.0",
  "result": "0x09184e72a000" // 10000000000000
}
```

***

#### platon_accounts

返回客户端拥有的地址列表。


##### 参数
none

##### 返回

`Array of DATA`, string - 客户端拥有的bech32格式的地址字符串。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_accounts","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": ["lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3"]
}
```

***

#### platon_blockNumber

返回节点的块高

##### 参数
none

##### 返回

`QUANTITY` - 客户端所在的当前块高。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_blockNumber","params":[],"id":83}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":83,
  "jsonrpc": "2.0",
  "result": "0x4b7" // 1207
}
```

***

#### platon_getBalance

返回给定地址的帐户余额。

##### 参数

1. `DATA`, string - 字符串-bech32格式的地址字符串，用于检查余额。
2. `QUANTITY|TAG` - 块高, 或字符串: `"latest"`, `"earliest"` 或 `"pending"`.

```js
params: [
   'lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3',
   'latest'
]
```

##### 返回

`QUANTITY` - 当前余额(以von为单位)的整数。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getBalance","params":["lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3", "latest"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58" // 158972490234375000
}
```

***

#### platon_getStorageAt

从给定地址的存储位置返回值。

##### 参数

1. `DATA`, string - 存储格式为bech32的地址字符串。
2. `QUANTITY` - 存储器中位置的整数。
3. `QUANTITY|TAG` - 块高, 或字符串:  `"latest"`, `"earliest"` 或 `"pending"`.


```js
params: [
   'lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3',
   '0x0', // storage position at 0
   '0x2' // state at block number 2
]
```

##### 返回

`DATA` - 该存储位置的值。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getStorageAt","params":["lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3", "0x0", "0x2"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x03"
}
```

***

#### platon_getTransactionCount

返回从地址*sent*的交易数量。

##### 参数

1. `DATA`, string - bech32格式的地址字符串。
2. `QUANTITY|TAG` - 块高, 或字符串:  `"latest"`, `"earliest"` 或 `"pending"`.

```js
params: [
   'lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3',
   'latest' // state at the latest block
]
```

##### 返回

`QUANTITY` - 从该地址发送的交易次数的整数。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getTransactionCount","params":["lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3","latest"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### platon_getBlockTransactionCountByHash

从与给定区块哈希匹配的区块中返回区块中的交易数量。


##### 参数

1. `DATA`, 32 Bytes - 块哈希。

```js
params: [
   '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238'
]
```

##### 返回

`QUANTITY` - 此区块中交易数量的整数。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getBlockTransactionCountByHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xb" // 11
}
```

***

#### platon_getBlockTransactionCountByNumber

从与给定区块编号匹配的区块中返回区块中的交易数量。


##### 参数

1. `QUANTITY|TAG` - 块高, 或字符串:  `"earliest"`, `"latest"` 或 `"pending"`.

```js
params: [
   '0xe8', // 232
]
```

##### 返回

`QUANTITY` - 此区块中交易数量的整数。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getBlockTransactionCountByNumber","params":["0xe8"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

***

#### platon_getCode

返回给定地址的code。


##### 参数

1. `DATA`, string - bech32格式的地址字符串。
2. `QUANTITY|TAG` - 块高, 或字符串:  `"latest"`, `"earliest"` 或 `"pending"`.

```js
params: [
   'lax14984xa8uuhkmer32s6tuz5e3valxa0ct68a0c5',
   '0x2'  // 2
]
```

##### 返回

`DATA` - 返回给定地址的code。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getCode","params":["lax14984xa8uuhkmer32s6tuz5e3valxa0ct68a0c5", "0x2"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```

***

#### platon_sign

用给定的地址签名数据。

**注意** 签名地址必须解锁。

##### 参数

1. `DATA`, string - bech32格式的地址字符串。
2. `DATA`, 要签名的数据。

##### 返回

`DATA`: 签名后的数据。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_sign","params":["lax16xk7yhxd842s5l44x2k8t89v00sfcfcej8gsug", "Schoolbus"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x2ac19db245478a06032e69cdbd2b54e648b78431d0a47bd1fbab18f79f820ba407466e37adbe9e84541cab97ab7d290f4a64a5825c876d22109f3bf813254e8601"
}
```

***

#### platon_sendTransaction

如果数据字段包含代码，则创建新的消息呼叫交易或合同创建。

##### 参数

1. `Object` - 交易对象。
  - `from`: `DATA`，string - 发送事务的bech32格式的地址字符串。
  - `to`: `DATA`，string - bech32格式的地址字符串-(在创建新合约时是可选的)交易指向的地址。
  - `gas`: `QUANTITY` - (可选，默认值: 90000)为交易执行提供的gas的整数。它将返回未使用的气体。
  - `gasPrice`: `QUANTITY` - (可选，默认值: 待定)gasPrice。
  - `value`: `QUANTITY` - (可选)此交易发送的值的整数。
  - `data`: `DATA` - (可选)合同的编译代码。
  - `nonce`: `QUANTITY` - (可选)随机数的整数。这样可以覆盖使用相同随机数的未决事务。

```js
params: [{
  "from": "lax1kc8gm4sut5etaqzchw8tjuy8purjxv245450s0",
  "to": "lax163hgm4nut5etaqzchw8tjuy8purjg3t87dtrgq",
  "gas": "0x76c0", // 30400,
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

##### 返回

`DATA`, 32字节-交易哈希，如果交易不可用，则为零字节哈希。

在创建交易后，使用platon_getTransactionReceipt获取交易后的合约地址。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_sendTransaction","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

#### platon_sendRawTransaction

发送已签名交易数据

##### 参数

1. `Object` - 交易对象。
  - `data`: `DATA`, 已签名的交易数据。

```js
params: [{
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

##### 返回

`DATA`, 32字节 - 交易哈希，如果交易尚不可用，则为零哈希。

在创建交易后，使用platon_getTransactionReceipt获取交易后的合约地址。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_sendRawTransaction","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

#### platon_call

执行新的消息调用，而无需在区块链上创建事务。

##### 参数

1. `Object` - 事务调用对象
  - `from`: `DATA`，字符串 - bech32格式的地址字符串-(可选)发送交易的地址。
  - `to`: `DATA`，字符串 - bech32格式的地址字符串-事务指向的地址。
  - `gas`: `QUANTITY` - (可选)为交易执行提供的gas的整数。 platon_call不消耗gas，但是某些执行可能需要此参数。
  - `gasPrice`: `QUANTITY` - (可选)gasPrice
  - `value`: `QUANTITY` - (可选)此交易发送的值的整数。
  - `data`: `DATA` - (可选)合同的编译代码。
2. `QUANTITY|TAG` - 块高, 或字符串:  `"latest"`, `"earliest"` 或 `"pending"`.

##### 返回

`DATA` - 执行合约的返回值。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_call","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0"
}
```

***

#### platon_chainId

返回用于在当前最佳区块进行交易签名的链 ID。 如果不可用，则返回 Null。


##### 参数

None

##### 返回

`QUANTITY` - 链 ID，如果不可用，则为 null。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_chainId","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1"
}
```

***

#### platon_getAddressHrp

返回当前链的账户地址前缀。


##### 参数

None

##### 返回

`DATA` - 帐户地址的前缀。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getAddressHrp","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "lat"
}
```

***

#### platon_getProof

返回给定帐户的 Merkle-proof 和可选的一些存储密钥。


##### 参数

1. `Address`, 20 Bytes - 合约账户地址.
2. `StorageKeys` - StorageTrie中的key
3. `BlockNumber` - 区块高度

```js
params: [
   'lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq93t3hkm',
   ["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],
   '0x1'
]
```

##### 返回

`Object` - 账户相关数据和证明.

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getProof","params":[see above],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": {
      "address": "lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq93t3hkm",
      "accountProof": ["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],
      "balance": "0x99",
      "codeHash": "0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238",
      "nonce": "0x1",
      "storageHash": "0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238",
      "storageProof":[
          {
            "key": "0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238",
            "value": "0x9",
            "proof": ["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"]
          }
      ]
  }
}
```

***

#### platon_resend

重新发送接受现有交易和新的天然气价格和limit。 它将从池中删除给定的交易，并使用新的 gas 价格和limit重新插入它。

##### 参数

1. `Object` - 交易对象 - 参照 `platon_sendTransaction`

2. `QUANTITY` - `gasPrice` - 用于每个付费gas 的gasPrice 的整数。
3. `QUANTITY` - `gasLimit` - 为交易执行提供的gas整数。

```js
params: [{
  "from": "lat1lfxu0c2s4g2z872hgutpytlyekclw7272sj8dy",
  "to": "lat1wgs4njks2wm4s596prdktrvsnfayh0kzv5ntru",
  "gas": "0x76c0", // 30400,
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
},
"0x1",
"0x1"
]
```

##### 返回

`DATA`, 32 Bytes - 交易Hash，如果交易尚不可用则为空Hash。

当你创建合约时，在交易被打包后，使用 platon_getTransactionReceipt 获取合约地址。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_resend","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

***

#### platon_pendingTransactionsLength

返回交易池中待处理交易的数量。


##### 参数

None

##### 返回

`QUANTITY` - 待处理交易的数量。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_pendingTransactionsLength","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": 1
}
```

***

#### platon_getPoolNonce

返回给定帐户的最新 Nonce。


##### 参数

1. `Address`, 20 Bytes - 账户地址.

```js
params: ['lat1wgs4njks2wm4s596prdktrvsnfayh0kzv5ntru']
```

##### 返回

`QUANTITY` - 账户的Nonce.

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getPoolNonce","params":[see above],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": '0x1'
}
```

***

#### platon_pendingTransactions

返回交易池中的交易，并且具有作为该节点管理的帐户之一的发起人地址。


##### 参数

None

##### 返回

`Array` - 交易集合:

  - `blockHash`: `DATA`, 32 Bytes - 区块的哈希值。 等待被打包时为`null` 。
  - `blockNumber`: `QUANTITY` - 区块高度。等待被打包时为`null` 。
  - `from`: `DATA`, string - bech32 格式的地址字符串。交易的发送方。
  - `gas`: `QUANTITY` - 交易消耗的gas。
  - `gasPrice`: `QUANTITY` - GasPrice 为交易执行提供price。
  - `hash`: `DATA`, 32 Bytes - 交易的Hash。
  - `input`: `DATA` - 随交易一起发送的数据。
  - `nonce`: `QUANTITY` - 发件人在此之前进行的交易数量。
  - `to`: `DATA`, string - 接收方bech32格式的地址字符串。 当是合约创建的交易时为`null` 。
  - `transactionIndex`: `QUANTITY` - 区块中交易索引的位置，未打包时为`null`。
  - `value`: `QUANTITY` - 以 von 为单位转移的价值。
  - `r`: `Quantity` - 签名的 R 字段。
  - `s`: `Quantity` - 签名的 S 字段。
  - `v`: `Quantity` - 签名的 V 字段。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_pendingTransactions","params":[see above],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": [
      {
          blockHash: "",
          blockNumber: 26774895,
          from: "lat1lfxu0c2s4g2z872hgutpytlyekclw7272sj8dy",
          gas: 49220,
          gasPrice: 1000000000,
          hash: "0x926694537d833a406cb3b321f79966c7cab24e461ac419d4366e94dccc5f2e6e",
          input: "0xf855838203ec8180b842b8402d35a84c4fc677fe2a19c43407d4cd387b0bbf90a5a3511794d7f752012e4090d8e7a0931ed540be41b73badd3c767c5de28195f3062c7aefba951bfd7a5c49e8a896c6b935b8bbd400000",
          nonce: 1787,
          r: "0x61b8e974d37dfbe221be5267753e1717f573b6fcd9a0ca0b223aba1c2f8283af",
          s: "0xc0ef7e651bafbd396536b20250fde46199ed6fcf356d08b390e36a3ba039fca",
          to: "lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq93t3hkm",
          transactionIndex: 0,
          v: "0x62297",
          value: 0
        }
  ]
}
```

***

#### platon_getRawTransactionByBlockHashAndIndex

返回给定区块哈希和索引的交易字节数。

##### 参数

1. `Hash` - 32 Bytes - 区块Hash。
2. `Quantity` - 交易在区块中的索引。

```js
params: [
    '0x926694537d833a406cb3b321f79966c7cab24e461ac419d4366e94dccc5f2e6e', 
    '0x1'
]
```

##### 返回

- `Data` - 交易的原始字节数据（在 RLP 之后）。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getRawTransactionByBlockHashAndIndex","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xf8c08206fb843b9aca0082c04494100000000000000000000000000000000000000280b857f855838203ec8180b842b8402d35a84c4fc677fe2a19c43407d4cd387b0bbf90a5a3511794d7f752012e4090d8e7a0931ed540be41b73badd3c767c5de28195f3062c7aefba951bfd7a5c49e8a896c6b935b8bbd40000083062297a061b8e974d37dfbe221be5267753e1717f573b6fcd9a0ca0b223aba1c2f8283afa00c0ef7e651bafbd396536b20250fde46199ed6fcf356d08b390e36a3ba039fca"
}
```

***

#### platon_getRawTransactionByBlockNumberAndIndex

返回给定块号和索引的交易字节。

##### 参数

1. `Quantity` - 区块高度。
2. `Quantity` - 交易在区块中的索引。

```js
params: [
    '0x1b4', 
    '0x1'
]
```

##### 返回

- `Data` - 交易的原始字节数据（在 RLP 之后）。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getRawTransactionByBlockNumberAndIndex","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xf8c08206fb843b9aca0082c04494100000000000000000000000000000000000000280b857f855838203ec8180b842b8402d35a84c4fc677fe2a19c43407d4cd387b0bbf90a5a3511794d7f752012e4090d8e7a0931ed540be41b73badd3c767c5de28195f3062c7aefba951bfd7a5c49e8a896c6b935b8bbd40000083062297a061b8e974d37dfbe221be5267753e1717f573b6fcd9a0ca0b223aba1c2f8283afa00c0ef7e651bafbd396536b20250fde46199ed6fcf356d08b390e36a3ba039fca"
}
```

***

#### platon_signTransaction

签名交易而不将其发送到网络，可以使用 platon_sendRawTransaction 提交。

##### 参数

1. `Object` - 交易对象. 参考 platon_sendTransaction。

##### 返回

- `Object` - 签名的交易及其详细信息：
  - `raw`: `Data` - 已签名的 RLP 编码交易。
  - `tx`: `Object` - 交易对象。

##### 例子

```js
// Request
curl -X POST localhost:6789 --data '{"jsonrpc":"2.0","method":"platon_signTransaction","params":[{see above}],"id":1}' -H "Content-Type: application/json" 
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": {
    "raw": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
    "tx": {
      "hash": "0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
      "nonce": "0x0", // 0
      "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
      "blockNumber": "0x15df", // 5599
      "transactionIndex": "0x1", // 1
      "from": "lat1lfxu0c2s4g2z872hgutpytlyekclw7272sj8dy",
      "to": "lat1zqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq93t3hkm",
      "value": "0x7f110", // 520464
      "gas": "0x7f110", // 520464
      "gasPrice": "0x09184e72a000",
      "input": "0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360"
    }
  }
}
```

***

#### platon_getRawTransactionByHash

返回给定哈希的交易字节。

##### 参数

1. `Hash` - 32 Bytes - 交易Hash。

```js
params: [
    '0x926694537d833a406cb3b321f79966c7cab24e461ac419d4366e94dccc5f2e6e'
]
```

##### 返回

- `Data` - 交易的原始字节数据（在 RLP 之后）。

##### 例子

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getRawTransactionByHash","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xf8c08206fb843b9aca0082c04494100000000000000000000000000000000000000280b857f855838203ec8180b842b8402d35a84c4fc677fe2a19c43407d4cd387b0bbf90a5a3511794d7f752012e4090d8e7a0931ed540be41b73badd3c767c5de28195f3062c7aefba951bfd7a5c49e8a896c6b935b8bbd40000083062297a061b8e974d37dfbe221be5267753e1717f573b6fcd9a0ca0b223aba1c2f8283afa00c0ef7e651bafbd396536b20250fde46199ed6fcf356d08b390e36a3ba039fca"
}
```

***

#### platon_estimateGas

预估发送交易所需要的gas费用。

##### 参数

请参阅platon_call参数。

##### 返回

`QUANTITY` - 返回gas数量.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_estimateGas","params":[{see above}],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x5208" // 21000
}
```

***

#### platon_getBlockByHash

按哈希返回有关块的信息。


##### 参数

1. `DATA`, 32个字节-块哈希值。
2. `Boolean` - 如果为true，则返回完整的交易对象；如果为false，则仅返回交易的哈希值。

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   true
]
```

##### 返回

Object - 块对象，如果未找到块，则为null：

   - `number`：`QUANTITY` - 块号。当它的未决块时，为"null"。
   - `hash`：`DATA`，32字节 - 块的哈希值。当它的未决块时，为"null"。
   - `parentHash`：`DATA`，32字节 - 父块的哈希值。
   - `nonce`：`DATA`，8字节 - 生成的工作量证明的哈希。当它的未决块时，为"null"。
   - `sha3Uncles`：`DATA`，32字节 - 块中叔叔数据的SHA3。
   - `logsBloom`：`DATA`，256字节 - 用于块日志的Bloom过滤器。当它的未决块时，为"null"。
   - `transactionsRoot`：`DATA`，32字节 - 块的事务Trie的根。
   - `stateRoot`：`DATA`，32 Bytes - 块的最终状态Trie的根。
   - `receiptsRoot`：`DATA`，32 Bytes - 块的收据Trie的根。
   - `miner`：`DATA`，string - bech32格式的地址字符串 - 给予采矿奖励的受益人的地址。
   - `difficulty`：`QUANTITY` - 该方块难度的整数。
   - `totalDifficulty`：`QUANTITY` - 直到该区块为止链的总难度的整数。
   - `extraData`：`DATA` - 该块的“额外数据"字段。
   - `size`：`QUANTITY` - 该块的大小，以字节为单位。
   - `gasLimit`：`QUANTITY` - 此区块中允许的最大气体。
   - `gasUsed`：`QUANTITY` - 该区块中所有交易的总使用气体。
   - `timestamp`：`QUANTITY` - 整理块时的unix时间戳。
   - `transactions`：`Array` - 事务对象的数组，或32字节的事务哈希，具体取决于最后一个给定的参数。
   - `uncles`：`Array` - 叔叔哈希数组。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getBlockByHash","params":["0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331", true],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
"id":1,
"jsonrpc":"2.0",
"result": {
    "number": "0x1b4", // 436
    "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
    "nonce": "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
    "miner": "lax1fejlmgs4j432f9he7dfzlzgj9gcgsjt6c4ujsh",
    "difficulty": "0x027f07", // 163591
    "totalDifficulty":  "0x027f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size":  "0x027f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e" // 1424182926
    "transactions": [{...},{ ... }] 
    "uncles": ["0x1606e5...", "0xd5145a9..."]
  }
}
```

***

#### platon_getBlockByNumber

按块号返回有关块的信息。

##### 参数

1. `QUANTITY|TAG` - 块高, 或字符串:  `"earliest"`, `"latest"` 或 `"pending"`.
2. `Boolean` - 如果为true，则返回完整的交易对象；如果为false，则仅返回交易的哈希值。

```js
params: [
   '0x1b4', // 436
   true
]
```

##### 返回

参考platon_getBlockByHash。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getBlockByNumber","params":["0x1b4", true],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
```

结果参考platon_getBlockByHash。

***

#### platon_getTransactionByHash

返回有关交易哈希请求的交易信息。


##### 参数

1. `DATA`, 32个字节-交易哈希。

```js
params: [
   "0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"
]
```

##### 返回

Object - 交易对象，如果未找到交易，则为null：

   - `hash`: `DATA`，32字节 - 事务的哈希值。
   - `nonce`: `QUANTITY` - 发送者在此之前进行的交易次数。
   - `blockHash`: `DATA`，32字节 - 此事务所在的块的哈希。`null`，待处理。
   - `blockNumber`: `QUANTITY` - 该交易所在的区块号。`null`，待处理。
   - `transactionIndex`: `QUANTITY` - 交易索引在区块中的位置的整数。当待处理时为null。
   - `from`: `DATA`，string - 发送者的bech32格式的地址字符串。
   - `to`: `DATA`，string - 接收者的bech32格式的地址字符串。当其为合同创建交易时为`null`。
   - `value`: `QUANTITY` - 以von转移的值。
   - `gasPrice`: `QUANTITY` - 发送者在von中提供的汽油价格。
   - `gas`: `QUANTITY` - 发送者提供的气体。
   - `input`: `DATA` - 数据与交易一起发送。


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getTransactionByHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
"id":1,
"jsonrpc":"2.0",
"result": {
    "hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce":"0x",
    "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
    "blockNumber": "0x15df", // 5599
    "transactionIndex":  "0x1", // 1
    "from":"lax1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpnkqrx3",
    "to":"lax163hgm4nut5etaqzchw8tjuy8purjg3t87dtrgq",
    "value":"0x7f110" // 520464
    "gas": "0x7f110" // 520464
    "gasPrice":"0x09184e72a000",
    "input":"0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360",
  }
}
```

***

#### platon_getTransactionByBlockHashAndIndex

按区块哈希和交易索引位置返回有关交易的信息。


##### 参数

1. `DATA`, 32字节-块的哈希。
2. `QUANTITY` - 交易索引

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   '0x0' // 0
]
```

##### 返回

参考platon_getBlockByHash。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getTransactionByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
```

参考platon_getTransactionByHash。

***

#### platon_getTransactionByBlockNumberAndIndex

通过块号和交易索引位置返回有关交易的信息。


##### 参数

1. `QUANTITY|TAG` - 块高, 或字符串:  `"earliest"`, `"latest"` 或 `"pending"`.
2. `QUANTITY` - 交易索引位置。

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

##### 返回

参考platon_getBlockByHash。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getTransactionByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
```

结果参考platon_getTransactionByHash.

***

#### platon_getTransactionReceipt

根据交易hash返回交易回执数据

**注意** 在pending中的交易没有回执数据。


##### 参数

1. `DATA`, 32个字节-交易hash。

```js
params: [
   '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238'
]
```

##### 返回

`Object` - 交易收据对象，如果未找到收据，则为null：

   - `transactionHash`: `DATA`，32字节 - 事务的哈希。
   - `transactionIndex`: `QUANTITY` - 交易索引在区块中的位置的整数。
   - `blockHash`: `DATA`，32字节 - 此事务所在的块的哈希。
   - `blockNumber`: `QUANTITY` - 此交易所在的区块号。
   - `cumulativeGasUsed`: `QUANTITY` - 在区块中执行此交易时使用的天然气总量。
   - `gasUsed`: `QUANTITY` - 仅此特定交易使用的天然气量。
   - `contractAddress`: `DATA`，string - bech32格式的地址字符串 - 如果交易是创建合同，则创建的合同地址，否则为空。
   - `logs`: `Array` - 此事务生成的日志对象数组。
##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getTransactionReceipt","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
"id":1,
"jsonrpc":"2.0",
"result": {
     transactionHash: '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238',
     transactionIndex:  '0x1', // 1
     blockNumber: '0xb', // 11
     blockHash: '0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b',
     cumulativeGasUsed: '0x33bc', // 13244
     gasUsed: '0x4dc', // 1244
     contractAddress: 'lax1kc8gm4sut5etaqzchw8tjuy8purjxv245450s0' // or null, if none was created
     logs: [{
         // logs as returned by getFilterLogs, etc.
     }, ...]
  }
}
```

***

#### platon_newFilter

基于过滤器选项创建一个过滤器对象，以通知状态何时更改(日志)。
要检查状态是否已更改，请调用`platon_getFilterChanges`。

##### 参数

1. `Object` - 过滤器选项: 
  - `fromBlock`: `QUANTITY|TAG` - (可选, 默认: `"latest"`) 块高, 或 `"latest"` 最高块高 或 `"pending"`, `"earliest"` 尚未进行的交易。
  - `toBlock`: `QUANTITY|TAG` - (可选, 默认: `"latest"`) 块高, 或 `"latest"` 最高块高 或 `"pending"`, `"earliest"` 尚未进行的交易。
  - `address`: `DATA|Array`, string - bech32格式的地址字符串 - (可选) 合同地址或应从中产生日志的地址列表。
  - `topics`: `Array of DATA`, - (可选) 32字节`DATA`主题数组。 每个主题也可以是带有"or"选项的`DATA`数组。

```js
params: [{
  "fromBlock": "0x1",
  "toBlock": "0x2",
  "address": "lax13zy0ruv447se9nlwscrfskzvqv85e8d35gau40",
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b", null, [0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b, 0x000000000000000000000000aff3454fce5edbc8cca8697c15331677e6ebccc]]
}]
```

##### 返回

`QUANTITY` - 过滤器ID。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_newFilter","params":[{"topics":["0x12341234"]}],"id":73}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### platon_newBlockFilter

在节点中创建一个过滤器，以通知何时有新块到达。
要检查状态是否已更改，请调用platon_getFilterChanges。

##### 参数
None

##### 返回

`QUANTITY` - 过滤器ID。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_newBlockFilter","params":[],"id":73}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```

***

#### platon_newPendingTransactionFilter

在节点中创建一个过滤器，以通知新的挂起事务何时到达。
要检查状态是否已更改，请调用platon_getFilterChanges。

##### 参数
None

##### 返回

`QUANTITY` - 过滤器ID。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_newPendingTransactionFilter","params":[],"id":73}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```

***

#### platon_uninstallFilter

卸载具有给定ID的过滤器。当不再需要手表时，应始终调用它。
在一段时间内未通过platon_getFilterChanges请求超时时，可以额外地过滤超时。


##### 参数

1. `QUANTITY` - 过滤器ID。

```js
params: [
  "0xb" // 11
]
```

##### 返回

`Boolean` - `true` 如果过滤器已成功卸载，则为true；否则为false。

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_uninstallFilter","params":["0xb"],"id":73}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### platon_getFilterChanges

筛选器的轮询方法，该方法返回自上次轮询以来发生的日志数组。


##### 参数

1. `QUANTITY` - 过滤器ID。

```js
params: [
  "0x16" // 22
]
```

##### 返回

Array - 日志对象的数组，如果自上次轮询以来没有任何变化，则为空数组。

 - 对于使用`platon_newBlockFilter`创建的过滤器，返回的是块哈希值(`DATA`，32 Bytes)，例如`["0x3454645634534 ..."]`。
 - 对于使用`platon_newPendingTransactionFilter`创建的过滤器，返回的是交易哈希(`DATA`，32字节)，例如`["0x6345343454645 ..."]`。
 - 对于使用`platon_newFilter`日志创建的过滤器，对象是具有以下参数的对象：

   - `type`：`TAG` - `pending`，待处理日志。 `mined`，如果日志已经被挖掘。
   - `logIndex`：`QUANTITY` - 块中日志索引位置的整数。当其挂起的日志时为`null`。
   - `transactionIndex`：`QUANTITY` - 创建交易索引位置日志的整数。当其挂起的日志时为`null`。
   - `transactionHash`：`DATA`，32字节 - 创建此日志的事务的哈希值。当其挂起的日志时为`null`。
   - `blockHash`：`DATA`，32字节 - 此日志所在的块的哈希。`null`，待处理。当其挂起的日志时为`null`。
   - `blockNumber`：`QUANTITY` - 该日志所在的块号。`null`，待处理。当其挂起的日志时为`null`。
   - `address`：`DATA`，以bech32格式的地址字符串 - 此日志源自的地址。
   - `data`：`DATA` - 包含一个或多个日志的32字节非索引参数。
   - 主题：数据数组 - 索引日志参数的0到4个32字节DATA数组。 (在*solidity*中：第一个主题是事件签名的*hash*(例如`Deposit(address，bytes32，uint256)`)，除非您用`anonymous`说明符声明了该事件。)


##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getFilterChanges","params":["0x16"],"id":73}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "logIndex": "0x1", // 1
    "blockNumber":"0x1b4" // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
    "transactionIndex": "0x0", // 0
    "address": "lax1zmzhskk9vtl5rckulhuzn3dpgtclenta5fjs08",
    "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },{
      ...
    }]
}
```

***

#### platon_getFilterLogs

返回具有给定id的所有与筛选器匹配的所有日志的数组。


##### 参数

1. `QUANTITY` - 过滤器ID。

```js
params: [
  "0x16" // 22
]
```

##### 返回

参考platon_getFilterChanges.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getFilterLogs","params":["0x16"],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com
```

结果参考platon_getFilterChanges.

***

#### platon_getLogs

返回与给定过滤器对象匹配的所有日志的数组。

##### 参数

1. `Object` - 过滤器对象，请参阅platon_newFilter参数。

```js
params: [{
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]
}]
```

##### 返回

参考platon_getFilterChanges.

##### 例子
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"platon_getLogs","params":[{"topics":["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]}],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com
```

结果参考platon_getFilterChanges.

***

#### platon_evidences
查询节点双签证据。

##### 参数
none

##### 返回
`String` - 证据字符串包含三种类型的证据：duplicatePrepare，duplicateVote，duplicateViewchange。每种类型都包含多个证据，因此它是一个数组结构。解析时请注意。

##### 例子
```js
// Request
Curl -X POST --data '{"jsonrpc":"2.0","method":"platon_evidences","params":[],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": "evidences data..."
}
```

***

#### admin_addPeer

添加一个对端节点。


##### 参数

1. `String` - 对端节点字符串详细信息。


##### 返回

`Boolean` - 添加成功或失败(注意：返回true不一定会添加成功)。


##### 例子
```js
// Request
curl -X POST --data '{ "jsonrpc": "2.0", "method": "admin_addPeer", "params": [ "enode: //acb2281452fb9fc25d40113fb6afe82b498361de0ee4ce69f55c180bb2afce2c5a00f97bfbe0270fadba174264cdf6da76ba334a6380c0005a84e8a6449c2502@127.0.0.1: 14789"], "id": 74 }' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### admin_nodeInfo

返回当前客户端节点的详细信息。


##### 参数

no


##### 返回

`Object` - 当前节点详情。


##### 例子
```js
// Request
Curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_nodeInfo","params":[],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": {node information }
}
```

***

#### admin_peers

返回连接到当前客户端的对端节点的详细信息。


##### 参数

no


##### 返回

`Array` - 数组，连接的对端节点详细信息。


##### 例子
```js
// Request
Curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_peers","params":[],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "Result": [{Node 1 information} ,  {Node 2 information}, ..., {node information N}]
}
```

***

#### admin_getProgramVersion

查询代码的版本和签名。


##### 参数

no


##### 返回

`Object` - 包含两个字段，`版本`和`版本签名`。


##### 例子
```js
// Request
Curl -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"admin_getProgramVersion","params":[],"id":74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": {
        "Version": 1794,
        "Sign": "0xa5eb0a935f63006b8f3a2f4dcb007a2bf50c6eb4755f3c27ff3b3af63078da2f5f1eed9beafd1e2dd8f4e588a8eafa60337b7f95aba5a0167fa600115542763a00"
    }
}
```

***

#### admin_getSchnorrNIZKProve

获取bls证明


##### 参数

none


##### 返回

`String` - bls证明。


##### 例子
```js
// Request
curl -X POST --data '{ "jsonrpc": "2.0", "method": "admin_getSchnorrNIZKProve", "params": [], "id": 74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": "02705b94701eec4f4619d42796f3241a93035b8b8df3f098ae21f428339ed90599e77f4c90944854b70cbca341cb22480c8872da3b0ae4f6fda29df7293df93d"
}
```

***

#### admin_datadir

获取节点的数据目录。

##### 参数
no

##### 返回
`String` - 节点数据目录。

##### 例子
```js
// Request
curl -X POST --data '{ "jsonrpc": "2.0", "method": "admin_datadir", "params": [], "id": 74}' -H 'content-type: application/json'  https://rpc.alayascan.com

// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": "/home/platon/network/data"
}
```
***

#### personal_openWallet

OpenWallet启动硬件钱包的打开程序，建立USB连接，并试图通过提供的口令进行验证。

##### Parameters

1. `String` - 钱包文件路径。
2. `String` - 钱包的密码。

##### Returns

`Boolean` - 成功返回`true`, 否则返回`false`.

##### Example

```js
// Request
curl -X POST --data '{ "jsonrpc": "2.0", "method": "personal_openWallet", "params": ["keycard://044def09","abcdefg"], "id": 75}' -H 'content-type: application/json'  https://rpc.alayascan.com

```

***

#### personal_sendTransaction

在一次call调用中发送交易并签名。帐户不需要解锁来进行这个调用，之后也不会被解锁。

##### Parameters

1. `Object` - 交易对象。
  - `from`: `DATA`，string - 发送事务的bech32格式的地址字符串。
  - `to`: `DATA`，string - bech32格式的地址字符串-(在创建新合约时是可选的)交易指向的地址。
  - `gas`: `QUANTITY` - (可选，默认值: 90000)为交易执行提供的gas的整数。它将返回未使用的气体。
  - `gasPrice`: `QUANTITY` - (可选，默认值: 待定)gasPrice。
  - `value`: `QUANTITY` - (可选)此交易发送的值的整数。
  - `data`: `DATA` - (可选)合同的编译代码。
  - `nonce`: `QUANTITY` - (可选)随机数的整数。这样可以覆盖使用相同随机数的未决事务。

```js
params: [{
  "from": "lat1kc8gm4sut5etaqzchw8tjuy8purjxv24msxq7q",
  "to": "lat163hgm4nut5etaqzchw8tjuy8purjg3t83gevx0",
  "gas": "0x76c0", // 30400,
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

##### Returns

`DATA`, 32字节-交易哈希，如果交易不可用，则为零字节哈希。

##### Example


```js
//Request
curl --data '{"method":"personal_sendTransaction","params":[{"from":"lat1kc8gm4sut5etaqzchw8tjuy8purjxv24msxq7q","to":"lat163hgm4nut5etaqzchw8tjuy8purjg3t83gevx0","data":"0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e","value":"0x186a0"},"hunter2"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789

//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0x62e05075829655752e146a129a044ad72e95ce33e48ff48118b697e15e7b41e4"
}
```

***

#### personal_ecRecover

返回与用于计算`personal_sign`中签名的私钥相关的地址。

##### Parameters

1. `Data` - hash数据的原文。
2. `Data` - hash签名后的数据。

```js
params: [
  "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
  "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
]
```

##### Returns

`Address` - 签名数据的地址。

##### Example

```js
//Request
curl --data '{"method":"personal_ecRecover","params":["0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675","0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789

//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "lat1kc8gm4sut5etaqzchw8tjuy8purjxv24msxq7q"
}
```

***

#### personal_importRawKey

将给定的私钥导入仓库，用口令对其进行加密。

##### Parameters

1. `String` - 16进制的私钥明问。
2. `String` - 账户口令。

##### Returns

`String` - 账户的地址。

##### Example

```js
//Request
curl --data '{"method":"personal_importRawKey","params":["cd3376bb711cb332ee3fb2ca04c6a8b9f70c316fcdf7a1f44ef4c7999483295e","password1234"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789

//Result
"lat1kc8gm4sut5etaqzchw8tjuy8purjxv24msxq7q"
```

***

#### personal_listAccounts

列出所有账户地址。

##### Parameters
no

##### Returns

`Array` - 20字节的账户地址列表。

##### Example

```js
//Request
curl --data '{"method":"personal_listAccounts","params":[],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    "lat1kc8gm4sut5etaqzchw8tjuy8purjxv24msxq7q",
    "lat1e2q84y8avn0w6as0hx9lpp5mgawydy6g4lups0"
  ]
}
```

***

#### personal_listWallets

列出你以前使用过的所有钱包和一个空的新钱包。

##### Parameters
no

##### Returns

`rawWalletArray` - 钱包列表。

##### Example

```js
//Request
curl --data '{"method":"personal_listWallets","params":[],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  accounts: [{
      address: "atp1v79he42uvxghmmajx4r2gxqrckl7l0r6w2pwk8",
      url: "keycard://044d/m/44'/60'/0'/0/0"
  }],
  status: "Online",
  url: "keycard://044def09"
}
```

***

#### personal_lockAccount

解锁指定账户。

##### Parameters

1. `String` - 账户地址。
2. `Function` - (可选) 可选的回调，返回一个错误对象作为第一个参数，返回结果作为第二个参数。

##### Returns

`Boolean` - 如果账户被成功锁定，则为 "true"，否则为 "false"。

##### Example

```js
//Request
curl --data '{"method":"personal_lockAccount","params":["lat1v79he42uvxghmmajx4r2gxqrckl7l0r6huhkfg"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  result: true
}
```

***

#### personal_newAccount

创建新账户。

注意：它将成为当前新的解锁账户。一次只能有一个解锁的账户。

##### Parameters

1. `String` - 新账户的密码。

##### Returns

`Address` - 20 字节 - 新账户的标识符。

##### Example

```js
//Request
curl --data '{"method":"personal_newAccount","params":["abc123"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "lat13upz04zc2wjsam753h20asjatvlay227nmyk6p"
}
```

***

#### personal_sign

计算一个PlatON特定的签名： sign(keccak256("PlatON Signed Message: " + len(message) + message))。

##### Parameters

1. `Data` - 要签名的数据。
2. `Address` - 签名的账户地址。
3. `String` - 签名账户的口令。

##### Returns

`Data` - 被签名的数据。

##### Example

```js
//Request
curl --data '{"method":"personal_sign","params":[0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675","lat1kc8gm4sut5etaqzchw8tjuy8purjxv24msxq7q","hunter"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "0xe7225f986f192f859a9bf84e34b2b7001dfa11aeb5c7164f81a2bee0d79943e2587be1faa11502eba0f803bb0ee071a082b6fe40fba025f3309263a1eef52c711c"
}
```

***

#### personal_signTransaction

签署一个交易，而不把它派发到网络。之后可以使用`platon_sendRawTransaction`提交。帐户不需要解锁来进行这个调用，之后也不会被解锁。

##### Parameters

1. `Object` - 带有可选的条件字段的交易对象。见platon_sendRawTransaction。
2. `String` - 解锁账户的密码。

##### Returns

`Object` - 签署的交易及其细节:
- raw: 数据 - 经过签名的、RLP编码的交易。
- tx: 对象 - 交易对象。
- from: 地址 - 20字节 - 交易的发送地址。
- to: 地址 - (可选) 20字节 - 交易指向的地址。
- gas: 数量 - （可选）为交易执行提供的gas的整数。 eth_call消耗的gas为零，但某些执行可能需要这个参数。
- gasPrice: 数量 -（可选）每个付费gas使用的gas价格的整数。
- value: 数量 - （可选）随该交易发送的价值的整数。
- data: 数据 -（可选）方法签名的4个字节的哈希值，后面是编码的参数。详见Ethereum Contract ABI。
- nonce: 数量 - （可选）nonce的整数。这允许覆盖你自己的使用相同nonce的未决交易。
- condition: 对象 - （可选）交易的有条件提交。可以是整数块数 { block: 1 } 或UTC时间戳（秒） { time: 1491290692 } 或空。

##### Example

```js
//Request
curl --data '{"method":"personal_signTransaction","params":[{"from":"lat1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpunjvg7","to":"lat14984xa8uuhkmer32s6tuz5e3valxa0ct4z0qkm","data":"0x41cd5add4fd13aedd64521e363ea279923575ff39718065d38bd46f0e6632e8e","value":"0x186a0"},"hunter2"],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "raw": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
    "tx": {
      "hash": "0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
      "nonce": "0x0",
      "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
      "blockNumber": "0x15df",
      "transactionIndex": "0x1",
      "from": "lat1gp7h8k9ynm4ct5ev73j4qlwhr4g8zqxpunjvg7",
      "to": "lat1s5l58k9ynm4ct5ev73j4qlwhr4g8zqxpkhv75y",
      "value": "0x7f110",
      "gas": "0x7f110",
      "gasPrice": "0x09184e72a000",
      "input": "0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360"
    }
  }
}
```

***

#### personal_unlockAccount

从节点的key store中解锁指定的账户。该方法在底层1.1.1版本及以上默认禁用，需要在节点的启动参数中加入--allow-insecure-unlock以启用。

解锁的账户会保存到内存中直到解锁周期超时。默认的解锁周期为300秒。如果将解锁周期设为0s，将永久解锁账户,直到节点被关闭。

账号解锁后可以用于platon_sign和platon_sendTransaction调用。

##### Parameters

1. `Address` - 20字节 - 要解锁的账户地址。
2. `String` - 解锁账户的密码。
3. `Quantity` - (默认值：300) 整数或空值 - 帐户解锁的时间，以秒为单位，应该保持多久。

##### Returns

`Boolean` - 调用是否成功。

##### Example

```js
//Request
curl --data '{"method":"personal_unlockAccount","params":["lat13upz04zc2wjsam753h20asjatvlay227nmyk6p","hunter2",null],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### miner_setGasPrice

设定开采交易时可接受的最小gas价格。任何低于此限制的交易都被排除在开采过程之外。

##### Parameters

1. Uint - gas数量。

##### Returns

`Boolean` - 设置成功返回`true`，否则返回`false`。


##### Example
```js
//Request
curl --data '{"method":"miner_setGasPrice","params":[19999999],"id":1,"jsonrpc":"2.0","bech32":true}' -H "Content-Type: application/json" -X POST localhost:6789
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### txpool_status

txpool的status属性可以用来查询交易池中当前等待打包进下一个区块的交易数量等信息。

##### Parameters

none

##### Returns

`Object` - status属性的值是一个包含两个字段的对象：pending和queued，每个字段的值都是一个关联数组。


##### Example
```js
//Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"txpool_status","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
		"pending": "0x0",
		"queued": "0x0"
	}
}
```

***

#### txpool_content

txpool的content属性可以用来查询当前在交易池中的待定和排队交易清单。

##### Parameters

none

##### Returns

`Object` - content属性的值是一个包含两个字段的对象：pending和queued，每个字段都是一个关联数组。


##### Example
```js
//Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"txpool_content","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
//Result
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
		"pending": {
			"lat1fx2pjucpzk3zm7fdyqez4yhxyqkrqlzhgpzcf7": {
				806: [{
						blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
						blockNumber: null,
						from: "lat1fx2pjucpzk3zm7fdyqez4yhxyqkrqlzhgpzcf7",
						gas: "0x5208",
						gasPrice: "0xba43b7400",
						hash: "0xaf953a2d01f55cfe080c0c94150a60105e8ac3d51153058a1f03dd239dd08586",
						input: "0x",
						nonce: "0x326",
						to: "lat1c7kuxvr4wdswy89p049spsk6ddwtqhjqcp4uef",
						transactionIndex: null,
						value: "0x19a99f0cf456000"
				}]
			}
		},
		"queued": {
			"lat1h926hjvmfnhenwrx7tzr2mcwk2wq80dvzzeqjj": {
				2: [{
					blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
					blockNumber: null,
					from: "lat1h926hjvmfnhenwrx7tzr2mcwk2wq80dvzzeqjj",
					gas: "0x15f90",
					gasPrice: "0xba43b7400",
					hash: "0x3a3c0698552eec2455ed3190eac3996feccc806970a4a056106deaf6ceb1e5e3",
					input: "0x",
					nonce: "0x2",
					to: "lat1smxhhr3ufxnqv55agjtq3cjsng4rqx489p6d0m",
					transactionIndex: null,
					value: "0xebec21ee1da40000"
				}],
			}
		}
	}
}
```

***

#### txpool_inspect

txpool的inspect属性可以列出交易池中当前等待打包进下一个区块的交易的概要信息。 

##### Parameters

none

##### Returns

`Object` - inspect属性的值是一个包含两个字段的对象：pending和queued，每个字段都是一个关联数组。


##### Example
```js
//Request
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","bech32":true,"method":"txpool_inspect","params":[],"id":1}' -H 'content-type: application/json'  https://rpc.alayascan.com
//Result
{
	"jsonrpc": "2.0",
	"id": 1,
	"result": {
		"pending": {
			"lat1wv9jphl4gxj8jyazl63qvfzp4sq2zv82u0zl2n": {
				"13975": "lat1d5cqy3j8462xrywh9h08jndpfky78rt82qapul: 1 von + 21000 gas × 1000000000 von",
				"13976": "lat1qtyzgqm8n0vcv2cnmyvqnvzeures9ggr38yf9k: 1 von + 21000 gas × 1000000000 von",
				"13977": "lat1j5zyyyztejn7d67f4vk3c0uydhgx8qu0quhj7e: 1 von + 21000 gas × 1000000000 von",
				"13978": "lat1jvy3w9ps7t076w4pclhv9n9x3ng4zjxedhwdcj: 1 von + 21000 gas × 1000000000 von",
				"13979": "lat1ft23z88sfdqaphp6u7lt5eaaha3x9tqpwq6geu: 1 von + 21000 gas × 1000000000 von"
			},
			"lat1z9ayvyxhtepectk92gd8rkfzuk48wq3pxyexrs": {
				"13829": "lat1896fjn0fsun8xnpy58jcku9zpgdpzta07565v4: 1 von + 21000 gas × 1000000000 von",
				"13830": "lat1e2aew0nh6dcung2mg0xuqtdp0fl5x9sm36jmvl: 1 von + 21000 gas × 1000000000 von",
				"13831": "lat1kvyd32vqqwtrnw0687hvccjezm5jnl8a5lm5hf: 1 von + 21000 gas × 1000000000 von"
			}
		},
		"queued": {
			"lat1g62rmjcmd5v8ezyp0levzakp2t3g2x3qfjeeyy": {
				"13813": "lat16z74lt8sj75mdw9lxpjpce22x9u6h5tslealgp: 1 von + 21000 gas × 1000000000 von",
				"13814": "lat1hff2xwvh7p34rayy695xg48f78j7nmkwctgqld: 1 von + 21000 gas × 1000000000 von"
			},
			"lat14zgzfkz6sfh5sy3csvwsfcphg458kgdhhc3e5r": {
				"14192": "lat1wyfjc62wp9axjcjg6jupeclnrklzhk2m58he44: 1 von + 21000 gas × 1000000000 von",
				"14193": "lat1rz5dq9ea8ay6mj4chlzq9cn6qkvunad6ezynnn: 1 von + 21000 gas × 1000000000 von",
				"14194": "lat1lvcl6w2nfhjhachqsncfe4nvafe5seykfj05vm: 1 von + 21000 gas × 1000000000 von"
			}
		}
	}
}
```

***

#### debug_getWaitSlashingNodeList

获取零出块的节点，因为零出块而被观察的节点列表。

##### 参数
no

##### 返回
`array` - 零出块的节点列表，每个结构包含三个字段，NodeId：零出块的节点ID，Round：观察期内第一次零出块时所处共识轮数，CountBit：零出块次数位图（从Round开始，1表示该轮未出块）。

##### 例子
```js
// Request
curl -X POST --data '{ "jsonrpc": "2.0", "method": "debug_getWaitSlashingNodeList", "params": [], "id": 74}'
// Result
{
  "id": 74,
  "jsonrpc": "2.0",
  "result": "[{
    "NodeId": "8e91f562c1798dc8c567a5c4a99a840eb86e43324b622fd0a4a8defdf873baf8f822313d7f35227fe15b6f4a2767dfb9ea7f7968d0a3a243e57b4d1090f6fc6c",
    "Round": 1000,
    "CountBit": 7
  }]"
}
```