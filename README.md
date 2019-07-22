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



## NEO3 变动部分总结



### Changes in NEO3

- NEO3中的地址脚本发生了变动，不再使用 Opcode.CheckSig, OpCode.CheckMultiSig 指令， 换成使用互操作服务调用，即SysCall "Neo.Crypto.CheckSig".hash2uint, SysCall "Neo.Crypto.CheckMultiSig".hash2unit 方式。
- NEO3弃用了UTXO模型，仅保留有账户余额模型；
- NEO3取消了每笔交易10 GAS的免费额度，系统费用总额受合约脚本的指令数量和指令类型影响
- NEO3取消了APPCALL，TAILCALL，SHA1，SHA256，HASH160，HASH256，CHECKSIG，VERIFY，CHECKMULTISIG，CALL_I，CALL_E，CALL_ED，CALL_ET，CALL_EDT等Opcode，新增了DUPFROMALTSTACKBOTTOM等Opcode。
- NEO3取消了claimgas，dumpprivkey，getaccountstate，getapplicationlog，getassetstate，getbalance，getclaimable，getmetricblocktimestamp，getnep5balances，getnep5transfers，getnewaddress，gettxout，getunclaimed，getunclaimedgas，getunspents，getwalletheight，importprivkey，invoke，listaddress，sendfrom，sendtoaddress，sendmany等API接口，重新定义了getblockheader，getrawmempool等API接口的调用，并更新了getblock，getblockheader，getrawtransaction，getversion，getcontractstate等API接口的返回内容。
