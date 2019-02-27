# 王朝API

当前支持的链，接口chain字段的可选范围：
CHATNS = [ BTC, ETH, LTC, USDT, VCT ]

当前支持的币种，接口coin字段的可选范围：
COINS = [ BTC, ETH, ERC20, LTC, USDT, VCT ]




## API
---

### 签名 所有用户接口都使需要对请求参数内容签名，如签名内容如下：
data: {
    apiKey: '12345678',
    cbUrl: 'http://localhost/api/cb'
}

str=apiKey=12345678&cbUrl=http://localhost/api/cb&ts=1546018930321 + secKey
secKey是用户密钥
然后对 str md5 得到 signture


### 请求应答统一格式 
```
{
    code: 状态码 | 0: 成功 400**: 错误码
    msg: 描述
    data: 数据
}
```


### 通用接口
---------------------------------------------------------------------------

#### 设置回调url

- path: api/setCallbackUrl
- method: POST
- data: {
    apiKey: 用户apiKey
    cbUrl: 回调url
    signture: 签名 | 必填 使用代理则由代理完成签名
}


#### 构造转账事物数据

- path: api/createTransferTxData
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填
    - chain: 链 | 必填 | CHATNS
    - coin: 币种 | 必填 | COINS
    - erc20Address: 合约地址 | ERC20必填 
    - from: 转账发送者 | ETH必填
    - to: 收款地址 | 选填 |
    - value: 收款地址 | 选填 |
    - gasPrice: ETH燃气费 | ETH链有效，选填  如果是个位数时表示档位，如慢中快，待具体设计
    - feeRate: BTC/LTC/USDT 费率 | BTC/LTC/USDT必填 |
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}

- response
```
{
    code: 0,
    msg: "success",
    data: {
        txData: {}, // 事务数据
        requestId: string, // 提交id
    }
}
```

#### 签名转账事务并提交上链

- path: api/signAndSubmitTokenDeploy
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填
    - chain: 链 | 必填 | CHATNS
    - txData: 事物数据 | 必填 |
    - privateKey: 用户私钥 | 必填 |
    - requestId: createTransferTxData返回的requestId | 必填 |
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response
```
{
    code: 0,
    msg: "success",
}
```

#### 提交转账转账事物上链

- path: api/signAndSubmitTransfer
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填 |
    - chain: 链 | 必填 | CHATNS
    - signedTxData: 签名后事物数据 | 必填 |
    - requestId: createTransferTxData求返回的requestId | 选填
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}

- response
```
{
    code: 0,
    msg: "success",
}
```


#### 添加订阅充值地址

- path: api/subscribe
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填
    - addresses: 订阅地址列表，逗号分割 | 必填
    - chain: 链 | 必填 | CHATNS
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response
```
{
    code: 0,
    msg: "success",
    data: {
        "addresses": [],  // 用户提交的订阅地址列表
        "addressesTotalCount": 2  // 用户全部的订阅地址数
    }
}
```


#### 获取余额

- path: api/getBalance
- method: GET
- query: {
    - apiKey: 用户apiKey | 必填
    - address: 地址 | 必填
    - chain: 链 | 必填 | 
    - coin: 币种 | 选填 | CHATNS
    - tokenAddress: 合约地址 | ERC20必填 
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response
```
{
    code: 0,
    msg: "success",
    data: {
        "balanceWaitConfirm": 0, // 收款等待确认余额
        "balanceUseable": "91506.14",  // 可用余额
        "balanceLocaked": "0"  // 锁定余额
    }
}
```


#### 查询转账记录 包括转出转入

- path: api/history
- method: GET
- query: {
    - apiKey: 用户apiKey | 必填
    - address: 地址 | 必填
    - chain: 链 | 必填 | CHATNS
    - coin: 币 | 必填 | COINS
    - tokenKey:  token唯一标识 ERC20合约地址 VCT为token名 | 当查询token转账记录时必填 |
    - pageIndex: 页码 | 选填 |
    - pageSize: 条数 | 选填 |
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response 
```
{
    code: 0,
    msg: "success",
    data: {
        items: [], // 转账列表
        count:  // items数
    }
}
```


#### 查询平台支持的Token列表

- path: api/tokens/list
- method: GET
- query: {
    - apiKey: 用户apiKey | 必填
    - chain: 链 | 必填 | CHATNS
    - coin: 币 | 选填 | 
    - pageIndex: 页码 | 选填
    - pageSize: 条数 | 选填
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response 
```
{
    code: 0,
    msg: "success",
    data: {
        items: [], // 转账列表
        count:  // items数
    }
}
```



### TOKEN 发布相关
---------------------------------------------------------------------------


#### 获取未签名Token事务

- path: api/createTokenTxData
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填
    - chain: 链 | 必填 | CHATNS
    - coin: 币 | 必填 | COIN_TOKEN
    - name: Token名 | 必填 |
    - symbol: Token简称 | 必填 |
    - amount: 发币总量 | 必填 |
    - owner: Token发布者 | ETH必填 |
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response 返回事务需要参数及requestId
```
{
    code: 0,
    msg: "success",
    data: {
        txData: {}, // 事务数据
        requestId:, // 提交id
    }
}
```

#### 签名Token发布事务并提交上链

- path: api/signAndSubmitTokenDeploy
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填
    - chain: 链 | 必填 | CHATNS
    - txData: 签名后事物数据 | 必填 |
    - privateKey: 用户私钥 | 必填 |
    - requestId: createTokenTxData返回的requestId | 必填 |
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response
```
{
    code: 0,
    msg: "success",
}
```

#### 已签名Token事务提交上链

- path: api/signAndSubmitTokenDeploy
- method: POST
- data: {
    - apiKey: 用户apiKey | 必填
    - chain: 链 | 必填 | CHATNS
    - signdTxData: 签名后事物数据 | 必填 |
    - requestId: createTokenTxData返回的requestId | 必填 |
    - signture: 签名 | 必填 | 使用代理则由代理完成签名
}
- response
```
{
    code: 0,
    msg: "success",
}
```


## 通知

### 通知消息请求结构

```
{
    method: 'POST'
    url: ${cbUrl},  // 用户设置的回调url
    data: {
        id: ${ID}, // 消息id，用户可根据此id过滤重发消息，默认消息再没有正常发送情况下会再重发两次
        msg: ${msg}, // json序列化后的数据消息，用户需要使用类似 JSON.parse() 的方法反序列化成对象
        signture: ${msg}, 使用用户secKey对消息签名，用户需验证消息签名正确
    }
}
```

### 用户收到通知后需要回复以下格式数据： 
```
{
    code: 0
}
```

### 消息类型及数据，即msg反序列化后的数据

#### 平台主动转账

- 主动转账提交上链
```
{
    status: SUBMIT_TRANSACTION_TO_CHAIN
    description: 'submit transfer transaction to chain'
    txid
    requestId: 转账id,
}
```

- 主动转账提交上链错误
```
{
    status: SUBMIT_TRANSACTION_ERROR
    description: 'submit transfer transaction to chain error'
    error: 
    requestId: 转账id,
}
```

- 主动转账提交上链错误
```
{
    status: SUBMIT_TRANSACTION_ON_CHAIN_ERROR
    description: 'submit transfer transaction error from chain'
    error:
    requestId: 转账id,
}
```

- 主动转账提交上链等待确认
```
{
    status: SUBMIT_TRANSACTION_ON_CHAIN
    description: 'submit transfer transaction error from chain'
    transferData:
    requestId: 转账id,
}
```

- 主动转账提交上链事务确认
```
{
    status: SUBMIT_TRANSACTION_TO_CHAIN_CONFIRM
    description: 'submit transfer transaction confirm from chain'
    txid:
    confirmedNum:
    requestId: 转账id,
}
```

#### 监听到的链上转账，非通过王朝平台的转账
- 监听到转账
```
{
    status: TRANSACTION_FROM_CHAIN
    description: 'transfer transaction from chain'
    transferData: 
}
```

- 监听到的链上转账事务确认数
```
{
    status: TRANSACTION_FROM_CHAIN_CONFIRM
    description: 'transfer transaction from chain confirm'
    txid:
    confirm_num:
}
```


#### 发布Token

- 提交上链
```
{
    status: SUBMIT_TOKEN_TO_CHAIN
    description: 'submit token transaction to chain'
    txid
    requestId: 提交id,
    requestBody: 请求参数
}
```

- 提交上链错误
```
{
    status: SUBMIT_TOKEN_ERROR
    description: 'submit token transaction to chain error'
    error: 
    requestId: 提交id,
    requestBody: 请求参数
}
```

- 主动转账提交上链错误
```
{
    status: SUBMIT_TOKEN_ON_CHAIN_ERROR
    description: 'submit transfer transaction error from chain'
    error:
    requestId: 提交id,
}
```

- 已上链等待确认
```
{
    status: SUBMIT_TOKEN_ON_CHAIN
    description: 'submit token transaction error from chain'
    tokenData:
    requestId: 提交id,
}
```

- 主动转账提交上链事务确认
```
{
    status: SUBMIT_TOKEN_TO_CHAIN_CONFIRM
    description: 'submit token transaction confirm'
    txid:
    confirmedNum:
    requestId: 提交id,
}
```
