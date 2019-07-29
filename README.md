﻿<div align="center">  
<h1>NEO3 Development Guide</h1>
<img src="images/neo-rebranding.png" alt="NEO3 Development Guide" height="150">

<p>A development guide for basic tool developers of NEO3 to facilitate the underlying construction</p>
</div>

## Table 
- [Wallet](en/Wallet)
- [Transactions](en/Transactions)
- [RPC](en/RPC)
- [Smart contracts](en/SmartContract)
- [NeoVM](en/NeoVM)



## Changes in NEO3

- The [address script](en/Transactions#verification-script) in NEO3 has changed not using the Opcode.CheckSig and OpCode.CheckMultiSig but the interoperable service call `SysCall "Neo.Crypto.CheckSig".hash2uint`, `SysCall "Neo.Crypto .CheckMultiSig".hash2unit` instead
- [Address script](en/Wallet#Address) changes from the format of `0x21 + publicKey(compressed, 33 bytess) + 0xac` (Neo2.x) to `0x21 + publicKey(compressed, 33 bytes)+ 0x68 + 0x747476aa` (NEO3)
- NEO3 abandoned UTXO model with only account balance model retained
- NEO3 cancels the free discount of 10 GAS for each transaction. The [total fee](en/Transactions#systemFee) is subject to the quantity and type of instructions in the contract script
- NEO3 abandons the following commands: claimgas, dumpprivkey, getaccountstate, getapplicationlog, getassetstate, getbalance, getclaimable, getmetricblocktimestamp, getnep5balances, getnep5transfers, getnewaddress, gettxout, getunclaimed, getunclaimedgas, getunspents, getwalletheight, importprivkey, invoke, listaddress, sendfrom, sendtoaddress, sendmany, etc.
- NEO3 redefines the following commands' references: [getblockheader](en/RPC/api/getblockheader.md) and [getrawmempool](en/RPC/api/getrawmempool.md).
- NEO3 renews the following commands' returned content: [getblock](en/RPC/api/getblock.md), [getblockheader](en/RPC/api/getblockheader.md), [getrawtransaction](en/RPC/api/getrawtransaction.md), [getversion](en/RPC/api/getversion.md) and [getcontractstate](en/RPC/api/getcontractstate.md).
- NEO3 abandons the following opcodes: APPCALL, TAILCALL, SHA1, SHA256, HASH160, HASH256, CHECKSIG, VERIFY, CHECKMULTISIG, CALL_I, CALL_E, CALL_ED, CALL_ET, CALL_EDT, etc.
- NEO3 adds the following opcode: [DUPFROMALTSTACKBOTTOM](en/NeoVM#stack-operation).
- Add the [Manifest](en/SmartContract#Manifest) file to describe the characteristics of the contract
- Add [native contracts](en/SmartContract#Native-Contract)
- Reduce the [system fee](en/SmartContract#fees) for OpCode and interop services
- Provide the contract with the support for [accessing to network resources](en/SmartContract#accessing-to-internet-resources-to-be-added)
