# Transactions  

<!-- TOC -->

- [Transaction Structure](#Transaction-Structure)
    - [version](#version)
    - nonce
    - validUntilBlock
    - [sender](#sender)
    - [systemFee](#systemFee)
    - [networkFee](#networkFee)
    - [attributes](#attributes)
        - [usage types](#usage-types)
    - [script](#script)
    - [witnesses](#witnesses)
        - [Invocation Script](#Invocation-Script)
        - [Verification Script](#Verification-Script)
- [Transaction Serialization](#Transaction-Serialization)
- [Transaction Signature](#Transaction-Signature)

<!-- /TOC -->

A Neo Transaction is a signed data package with an instruction for the network and the only way to operate the Neo network. Each block in the Neo blockchain ledger contains one or more transactions, making each block a transaction batch. After transaction attributes encapsulated and signed by the client wallet, the transaction is sent to the node to which the wallet belongs. Any node in the network can verify the received transaction and forward it to the consensus node. The consensus node selectively packages transactions into a proposal block and broadcast it to reach an agreement. Once the validators agree on the new block, they will broadcast the new block to the entire network. For the new block received, the node will process all individual transactions in the block and then update the ledger.

![tx flow graph](../../images/tx_flow_graph.png)

## Changes in NEO3

- NEO3 abandoned UTXO model with only account balance model retained
-  NEO3 cancels the free discount of 10 GAS for each transaction. The total fee is subject to the quantity and type of instructions in the contract script
- The address script in NEO3 has changed not using the Opcode.CheckSig and OpCode.CheckMultiSig but the interoperable service call `SysCall "Neo.Crypto.CheckSig".hash2uint`, `SysCall "Neo.Crypto .CheckMultiSig".hash2unit` instead.

## Transaction Structure

A normal transaction has the following attributes:

| Field        | Type    | Description                              |
|--------------|---------|------------------------------------------|
| `version`    | byte   | the version of the transaction, currently 0 |
| `nonce`    | uint   | random number                  |
| `validUntilBlock`    | uint   |  block expiration time                |
| `sender`    | UInt160   |address script hash of the sender  |
| `systemFee`    | long   |  Fee to pay the network for resource usage  |
| `networkFee`    | long   | Fee to pay the validator for including transactions in the block   |
| `attributes` | tx_attr[]   | Additional features for the transaction  |
| `script`     | byte[]   | contract script of the transaction |
| `witnesses`  | Witness[]   | scripts used to validate the transaction   |

### version 
The version attribute allows updates to the transaction structure with backward compatibility, currently 0. 
### sender
Since NEO3 abandoned UTXO model with only account balance model retained. The transfer transaction of the native assets NEO and GAS are unified as the way of NEP-5 assets, so the input and output fields are removed from the transaction structure, instead, the sender field is used to track the source of the transaction. This field is the script hash of the transaction initiation account in the wallet.
### systemFee
The system fee is a fixed cost calculated by instructions to be executed by the Neo virtual machine. NEO3 cancels the free discount of 10 GAS for each transaction. The total fee is subject to the quantity and type of instructions in the contract script. The calculation formula is as follows:

![system fee](../../images/system_fee.png)

where OpcodeSet is opcode set,  is the cost of instruction i,  is the number of instruction i in the contract script.
### networkFee
The network fee is the fee paid by users when they submit transactions to the Neo network and is used to pay the validator for producing new blocks. For each transaction, there is a minimum network fee, which is calculated as follows,

![network fee](../../images/network_fee.png)

where VerificationCost is the costs for transaction signature verification in NeoVM, tx.Length is the byte length of transaction data, FeePerByte is the fee per byte. The network fee paid by users must be greater than or equal to NetworkFee, otherwise, the verification of the transaction will fail.
### attributes
Depending on the transaction type, it is allowed to add attributes to the transaction. For each attribute, a usage type has to be specified, together with the external data and the size of the external data.

| Field | Type | Description |
|----------|-------|------------------------|
| `usage`  | uint8 | Attribute usage type                  |
| `data`   | byte[] | Script of the transaction to be validated. When usage is `0x20` , the data type must be UInt160. |

#### Attribute usage types
The following usage types can be included in the transaction attributes.


| Value    | Name| Description | Type|
|---------------|-------------|---------------|--------------|
| `0x20`           | `Cosigner`    |  Indicating the multi-signature  transaction         | `byte`   |
| `0x81`           | `Url`          | URL for description    | `byte`  |

A maximum of 16 attributes can be added to each transaction.

## script
Contract script executed by the virtual machine.
## witnesses 
A witness is used to verify the validity and integrity of the transaction. There are two attributes for each witness in the witnesses array.

| Field | Description|
|--------------|------------------|
| `InvocationScript`   | Script to push validation signatures to the verification script      |
| `VerificationScript` | Verification script to push the public keys corresponding to the contract   |

You can add multiple witnesses to each transaction or use a multi-signature witness as well.

### Invocation Script

The invocation script is constructed with the following steps:

1.    `0x40`（PUSHBYTES64） followed by the (first) 64-byte signature

The invocation script can push multiple signatures for a multi-signature contract by repeating these steps.

### Verification Script

The verification script with a single signature is constructed with the following steps:

1. `0x21`（PUSHBYTES33）followed by the 33-byte public key that corresponds to the signature
2. `0x68`（SysCall）System call
3. `0xaa767474` invoke the interop service `Neo.Crypto.CheckSig` to verify the signature with the provided public key

The single signature script is shown in the following figure:

![](../../images/checksig_script.png)

The verification script with a multi-signature contract is constructed with the following steps:

1. `0x52`（PUSH2）to `0x60`（PUSH16）to indicate the required amount of signatures
2. `0x21`（PUSHBYTES33）followed by the first 33 byte public key for the multi-signature contract, repeated for each public key in the multi-signature contract
3. `0x52`（PUSH2）to `0x60`（PUSH16）to indicate the total amount of public keys for the signature
4. `0x68`（SysCall）System call
5. `0xba4cc3c7` invoke the interop service `Neo.Crypto.CheckMultiSig` to verify the signatures with the provided public keys

To maintain performance it is required to have the same order for public keys and signatures during the verification of a multi-signature contract. The multi-signature script is shown in the following figure: 

![](../../images/checkmultisig_script.jpg)

##  Transaction Serialization

Except for IP addresses and ports, all variable-length integer types in Neo are stored in the small-endian mode. The serialization operation takes the following order to serialize a transaction:

| Field | Description |
|----------|------------|
| `version`  | - |
| `nonce`   | - |
| `sender`   | - |
| `systemFee`   | - |
| `networkFee`   | -|
| `validUntilBlock`   | - |
| `attributes`   | First serialize the array length using `WriteVarInt (length)', and then serialize the elements of the array separately |
| `script`   |  First serialize the array length using `WriteVarInt (length)', and then serialize the elements of the array separately  |
| `witnesses`   |  First serialize the array length using `WriteVarInt (length)', and then serialize the elements of the array separately  |


> Note: WriteVarInt (value) stores variable-length types based on the value itself, and determines the storage size according to the value range.
> 
> | Value Range | Type |
> |--------------------|--------------|
> | value < 0xFD | byte(value) |
> | value <= 0xFFFF | 0xFD + ushort(value) |
> | value <= 0xFFFFFFFF | 0xFE + uint(value) |
> | value > 0xFFFFFFFF | 0xFF + value |

## Transaction Signature
A transaction is initiated by the client, encapsulated by the wallet module and attached with a digital signature to ensure that the transaction can be verified at any time in the later transmission and processing. Neo employs the ECDSA digital signature method. The script hash of the transaction output is a public key used for ECDSA signature. Neo does not use SegWit in Bitcoin. Each transaction contains its own Script.Witness, while the Script.Witness is a smart contract.

The witness is an executable verification script. The InvocationScript provides the parameters for the VerificationScript to execute. Verification succeeds only when the script execution returns true. Invocation script performs stack operation instructions, provides parameters for verification script (eg, signatures). The script interpreter executes the invocation script code first, and then the verification script code.

> Note: Transaction signature is to sign the data of the transaction itself (not including signature-attached data, namely witness).




