# PlatOn Uniswap Info (V2)

website：https://devnet-info.ddex.cc/

code： https://github.com/platon-uniswap/info



### To Start Development

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



### 部署

#### 克隆仓库

~~~bash
$ git clone https://github.com/platon-uniswap/info.git
~~~

#### 修改自己部署的请求链接

**src/apollo/client.js 第7、15、39行**

```javascript
export const client = new ApolloClient({
  link: new HttpLink({
    uri: '自我部署链接',
  }),
  cache: new InMemoryCache(),
  shouldBatch: true,
})

export const healthClient = new ApolloClient({
  link: new HttpLink({
    uri: '自我部署链接',
  }),
  cache: new InMemoryCache(),
  shouldBatch: true,
})

export const blockClient = new ApolloClient({
  link: new HttpLink({
    uri: '自我部署链接',
  }),
  cache: new InMemoryCache(),
})
```

**src/apollo/queres.js 第6行**

~~~javascript
export const SUBGRAPH_HEALTH = gql`
  query health {
    indexingStatusForCurrentVersion(subgraphName: "自我部署名称") {
      synced
      health
      chains {
        chainHeadBlock {
          number
        }
        latestBlock {
          number
        }
      }
    }
  }
`
~~~

#### 修改FACTORY_ADDRESS

~~~javascript
// src/constants/index.js 第一行
export const FACTORY_ADDRESS = '0x154b2f6202a5033Cb150931177B86a2B03E485dF'
~~~

#### Installing dependencies

```bash
$ yarn
```

#### Build

```bash
$ yarn build
```

#### Nginx 部署

~~~
server {
        listen       8080;
        server_name  localhost;

        location / {
            // 编译文件路径地址
            root   .../info/build;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
}
~~~

nginx重新reload：

```bash
$ nginx -s reload
```
