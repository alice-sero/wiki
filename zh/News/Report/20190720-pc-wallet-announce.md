# SERO共识升级公告（PC钱包用户）

最新的SERO全节点程序已经发布，请尽快更新程序。

SERO网络在130万块高度的时候会切换共识为 POS+POW。



## 下面是注意事项：

### PC全节点钱包会触发自动更新，更新的内容包括：

* 钱包客户端
  * 请在钱包的 `关于` 对话框确认版本号为`v0.1.6`
  * 如果版本号不一致，请在github下载最新的钱包程序
    * 链接<https://github.com/sero-cash/wallet/releases>

* 钱包附带的全节点后台（gero）
  * 钱包客户端会自动升级这个后台程序
  * 请在钱包的 `关于对话框` 确认节点版本号大于 `v1.0.0-rc2`
  * 如果不符合，请卸载钱包重新安装。
    * 卸载钱包不会清除区块数据
    * 请注意备份好keystore文件

### 重点

* 本次更新更换了账户交易余额分析服务，可能会导致更新后一段时间内账户余额异常。
  * 由`balance`服务转为`exchange`服务来分析账户余额。
  * `exchange`服务与第三方对接接口一致，运行更稳定。

* 如果账户异常可以进入账户界面查看分析进度。
  * ![image.png](http://sero-media.s3-website-ap-southeast-1.amazonaws.com/images/jianshu/277023-4c1fcf19f38247e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)