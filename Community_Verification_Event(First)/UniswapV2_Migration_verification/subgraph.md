## subgraph

环境
ubuntu 1804

仓库

https://github.com/graphprotocol/graph-node.git

安装依赖
    1. 安装rust
    2. 安装ipfs
    3. 安装postgresql

### 部署

运行ipfs

```
$ ipfs init

$ nohup ipfs daemon&
```

运行postgresql

```
$ sudo systemctl start postgresql
```

创建数据库

```
$ sudo su postgres
$ psql
$ create database graphnode;
```

运行rpc代理

```
$ git clone https://github.com/a2a6c172b3f1b60a8ce26f/rpc-adaptor.git
$ npm i
$ node index.js
```

运行graph节点

```
$ cargo run -p graph-node --release -- \
  --postgres-url postgresql://postgres:postgres@localhost:5432/graphnode \
  --ethereum-rpc lat:http://127.0.0.1:3000 \
  --ipfs 127.0.0.1:5001
```


部署ethereum-blocks

```
$ git clone https://github.com/blocklytics/ethereum-blocks.git
$ cd ethereum-blocks
$ npm i
$ npm run build
$ npm run create-local
$ npm run deploy-local
```

部署v2-subgraph

```
$ git clone https://github.com/Uniswap/v2-subgraph.git
$ cd v2-subgraph
$ npm i
$ npm run build
$ npm run create-local
$ npm run deploy-local
```

