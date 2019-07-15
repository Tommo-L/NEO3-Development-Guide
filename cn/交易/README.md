# 交易
Neo中的交易是带签名的数据包，是操作Neo网络的唯一方式。Neo区块链账本中的每个区块都包含有一个或多个交易，使得每个区块可以对交易进行批处理。一笔交易由客户端发起，经由钱包封装属性并签名后发往钱包所属的节点。网络中的任意节点都可以对接收到的交易进行验证，并转发至共识节点。共识节点会选择性的将交易打包到提案块中并广播进行共识。共识通过后广播至网络中的节点，各节点会对区块中的每笔交易进行验证并更新各自的账本。

## 交易序列化

除IP地址和端口号外，Neo中所有变长的整数类型都使用小端存储。

交易序列化时将按以下字段顺序执行序列化操作：

-   version
-   nonce
-   sender
-   systemFee
-   networkFee
-   validUntilBlock
-   attributes
-   script
-   witnesses

## 交易签名
一笔交易由客户端发起，经钱包模块封装属性并附加数字签名，确保在后续传输和处理中能随时验证交易是否被篡改。Neo采用的ECDSA数字签名方法。交易的转账转出方地址，为ECDSA签名时所用的公钥publicKey。Neo系统没有使用比特币中的SegWit，每笔交易都包含自己的Script.witness，而Script.Witness使用的是智能合约。

见证人，实际上是可执行的验证脚本。InvocationScript 脚本传递了VerificationScript脚本所需要的参数。只有当脚本执行返回真时，验证成功。

| 字段 | 类型 | 说明|
|-----|----|-------|
| `InvocationScript`   | byte[]| 调用脚本，向验证脚本传递参数  |
| `VerificationScript` | byte[]| 验证脚本   |

调用脚本进行压栈操作相关的指令，用于向验证脚本传递参数（如签名等）。脚本解释器会先执行调用脚本代码，然后再执行验证脚本代码。

Block.NextConsensus所代表的多方签名脚本，填充签名参数后的可执行脚本，如下图所示，Opt.CHECKMULTISIG 在NVM内部执行时，完成对签名以及公钥之间的多方签名校验。

![](../../images/nextconsensus_witness.jpg)

