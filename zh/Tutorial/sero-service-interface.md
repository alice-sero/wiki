# SERO Service Interface (SSI) 接口说明



Gero上开放了SSI服务，使得第三方可以通过rpc/web3连接gero，获得无状态的公链服务。

SSI的使用方式是不维护第三方创建的公私钥，因此，第三方需要自己创建存储公私钥，并在需要的时候提供给SSI。



## CreateKr

创建公私钥对

* Request

```json
  {
    "id":1,
    "jsonrpc":"2.0",
    "method":"ssi_createKr",
    "param":[]
  }
```


* Successful Response

```json
  {
      "id":1,
      "jsonrpc":"2.0",
      "result":{
          PKr:"0x....",     //暂存公钥(收款码)
          SKr:"0x...."      //暂存私钥
      }
  }
```





## GetBlocksInfo

用来主动同步区块数据

* Request

```json
  {
    "id":1,
    "jsonrpc":"2.0",
    "method":"ssi_getBlocksInfo",
    "param":[
        "0x0",                //起始块
        "0x5"                 //最多获取多少个块
    ]
  }
```


* Successful Response

```json
  {
      "id":1,
      "jsonrpc":"2.0",
      "result":[
          {
              Num:"0x1"               //块号
              Nils:["0x...""],        //本次作废码列表
              Outs:[{
                  PKr:"0x...",        //该Out的暂存公钥
                  Root:"0x..."        //该Out的Root
              }]
          },
          ...
      ]
  }
```





## Detail

获取对应Out的明文信息

* Request

```json
  {
    "id":1,
    "jsonrpc":"2.0",
    "method":"ssi_detail",
    "param":[
        ["0x...","0x..."],  //需要解出明文的out的root列表，需要有相同的PKr。
        "0x..."             //该Out的PKr对应的SKr
    ]
  }
```


* Successful Response

```json
  {
      "id":1,
      "jsonrpc":"2.0",
      "result":[
          {
              Asset:{                       //Out的资产
                  Tkn:{                     //Token资产
                      Currency:"SERO",      //Token的币名
                      Value:"10000000"      //Token的金额
                  },
                  Tkt:{                     //Ticket资产
                      Category:"XXXX",      //Ticket类型
                      Value:"0x..."         //Ticket的值
                  }
              },
              Memo:"...",                   //本Out的备注
              Nil:"0x..."                   //本Out的作废码
          },
          ...
      ]
  }
```



## GenTx

创建交易，需要发送方自己配平Out和In，注意In和Out的个数越多，交易生成的时间越长。

* Request

```json
  {
    "id":1,
    "jsonrpc":"2.0",
    "method":"ssi_genTx",
    "param":[{
        Gas: 10000,                       //本次交易的GasLimit
        GasPrice: 1000000000,             //本次交易的GasPrice
        From: "0x...",                    //代表发送者的SKr
        Ins: [
            {
               SKr: "0x...",              //输入的UTXO对应的SKr
               Root: "0x..."              //输入的UTXO对应的Root
            },
            ...
        ],
        Outs: [
            {
               PKr: "0x...",              //输出的PKr
               Asset: {...},              //输出的资产，参考Detail
               Memo: "0x....."            //本次输出的备注
            },
            ...
        ]
    }]
  }
```


* Successful Response

```json
  {
      "id":1,
      "jsonrpc":"2.0",
      "result":"0x..."                   //该交易的hash
  }
```





## CommitTx

提交已经创建的交易

* Request

```json
  {
    "id":1,
    "jsonrpc":"2.0",
    "method":"ssi_commitTx",
    "param":[
        "0x..."                        //之前genTx创建的交易的hash
    ]
  }
```


* Successful Response

```json
  {
      "id":1,
      "jsonrpc":"2.0",
      "result":null
  }
```





## 建议对接方案

1. 用户创建
   1. 为每个用户创建不同的Kr并存储在数据库中 
2. 充值：
   1. 不断的调用GetBlocksInfo获取最新的区块信息 
   2. 查找区块的Out中的PKr是否为自己创建，如果是，就调用Detail解出Out的明文信息以及对应的作废码Nil。
   3. 查找区块中的Nil是否在Detail的时候解出过，如果找到，说明对应的UTXO已经被用掉了。
3. 提现
   1. 获取已经存在的UTXO作为In，并构造出Out，配平后调用GenTx生成Tx。
   2. 调用CommitTx提交该Tx。