﻿<div align="center">  
<h1>NEO3 Development Guide</h1>
<img src="images/neo-rebranding.png" alt="NEO3 Development Guide" height="150">

<p>A development guide for basic tool developers of NEO3 to facilitate the underlying construction</p>
</div>

## Table 
- [Wallets](en/Wallets)
- [Transactions](en/Transactions)
- [RPC](en/RPC)
- [Smart contracts](en/SmartContract)
- [NeoVM](en/NeoVM)



## Changes in NEO3

### Wallet

- UPDATE
    - [Address Script](en/Wallets#Address): change the way to construct the address script with the public key
        - Ordinary Address

        ```
        NEO2: 0x21 + publicKey(compressed 33bytes) + 0xac()
        NEO3: 0x21 + publicKey(compressed 33bytes) + 0x68 + 0x747476aa
        ```

        - Multi-Signature Address

        ```
        NEO2: emitPush(N) + 0x21 + publicKey1(compressed 33bytes) + .... + 0x21 + publicKeym(compressed 33bytes)  + emitPush(M) + 0xae()
        NEO3: emitPush(N) + 0x21 + publicKey1(compressed 33bytes) + .... + 0x21 + publicKeym(compressed 33bytes)  + emitPush(M) + 0x68 + 0xc7c34cba
        ```

### Transactions

- UPDATE
    - [System Fee](en/Transactions#systemfee): cancel the free discount of 10 GAS for each transaction and redefine the [fee](en/NeoVM#fee) of each OpCode
    - [Network Fee](en/Transactions#networkfee): redefine the calculation formula for the network fee
    
- DELETE
    - Transaction Type: discard the previous 9 types of the transaction in NEO2 and use the unified `transaction` instead, as well as the redefinition of the [transaction structure](en/Transactions#transaction-structure)
    - [Assets](en/SmartContract#native-contract): discard the UTXO model for the NEO and GAS token, using the account model implemented by the native contract instead

    
### RPC

- UPDATE
    - Invocation Style: [getblockheader](en/RPC/api/getblockheader.md)，[getrawmempool](en/RPC/api/getrawmempool.md)
    - Returns: [getblock](en/RPC/api/getblock.md)，[getblockheader](en/RPC/api/getblockheader.md)，[getrawtransaction](en/RPC/api/getrawtransaction.md)，[getversion](en/RPC/api/getversion.md)，[getcontractstate](en/RPC/api/getcontractstate.md)

- DELETE
    - Discard the following commands: `claimgas`, `dumpprivkey`, `getaccountstate`, `getapplicationlog`, `getassetstate`, `getbalance`, `getclaimable`, `getmetricblocktimestamp`, `getnep5balances`, `getnep5transfers`, `getnewaddress`, `gettxout`, `getunclaimed`, `getunclaimedgas`, `getunspents`, `getwalletheight`, `importprivkey`, `invoke`, `listaddress`, `sendfrom`, `sendtoaddress`, `sendmany`, etc.

### Smart Contracts

- ADD 
    - [Manifest](en/SmartContract#manifest): used to describe the features of the contract and deployed with AVM files
    - [native contracts](en/SmartContract#native-contract): running in the native code rather than in the virtual machine, including NeoToken, GasToken and PolicyToken
    - [Accessing to network resources](en/SmartContract#accessing-to-internet-resources): to be added
    - [System Trigger](en/SmartContract#trigger): triggered when the node receives a new block and currently only triggers the execution of the native contract

- UPDATE
    - Reduce the [system fee](en/SmartContract#fees) for OpCode and interop services

### NeoVM

- ADD
    -  OpCode [DUPFROMALTSTACKBOTTOM](en/NeoVM#stack-operation)

- DELETE
    - Discard the following opcodes: `APPCALL`, `TAILCALL`, `SHA1`, `SHA256`, `HASH160`, `HASH256`, `CHECKSIG`, `VERIFY`, `CHECKMULTISIG`, `CALL_I`, `CALL_E`, `CALL_ED, `CALL_ET, `CALL_EDT`, etc.

*Click [here](README.CN.md) to see the Chinese edition of the README*



