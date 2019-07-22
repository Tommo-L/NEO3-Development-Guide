<div align="center">  
<h1>NEO3 Development Guide</h1>
<img src="images/neo-rebranding.png" alt="NEO3 Development Guide" height="150">

<p>A development guide for basic tool developers of NEO3 to facilitate the underlying construction</p>
</div>

## Table 
- [Wallet](en/wallet)
- [Transactions](en/transactions)
- [RPC](en/RPC)
- [Smart contracts](en/smartContracts)
- [NeoVM](en/NeoVM)





补充一部分，关于NEO3 变动部分的汇总介绍



1. 钱包部分： 地址合约发生变动，Opcode.CheckSig, Opcode.CheckMultiSig  换成互操作访问。
2. 其他部分需要补充，并format组织下。

### NEO3 Changed

| Items          | Neo2.x                                         | Neo3                                                      |
| -------------- | ---------------------------------------------- | --------------------------------------------------------- |
| Address script | 0x21 + publicKey(compressed, 33 bytess) + 0xac | 0x21 + publicKey(compressed, 33 bytes)+ 0x68 + 0x747476aa |