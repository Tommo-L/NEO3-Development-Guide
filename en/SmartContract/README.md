<!-- TOC -->

- [Smart Contracts](#Smart-Contracts)
    - [Changes in NEO3](#Changes-in-NEO3)
    - [Manifest](#Manifest)
    - [Trigger](#Trigger)
    - [Native Contract](#Native-Contract)
        - [Introduction](#Introduction)
            - [NeoToken](#NeoToken)
            - [GasToken](#GasToken)
            - [PolicyToken](#PolicyToken)
        - [NativeContract Deploy](#NativeContract-Deploy)
        - [NativeContract Invokation](#NativeContract-Invokation)
    - [Interop Service](#Interop-Service)
        - [Interop Service Principle](#Interop-Service-Principle)
        - [Usage of Interop Service](#Usage-of-Interop-Service)
        - [System Part](#System-Part)
        - [Neo Part](#Neo-Part)
    - [System Fee](#System-Fee)
    - [Internet Resources Access](#Internet-Resources-Access)
    - [Contract Invokation](#Contract-Invokation)
    - [Contract Update](#Contract-Update)
    - [Contract Destroy](#Contract-Destroy)

<!-- /TOC -->

# Smart Contracts
## Changes in NEO3
All transactions in NEO3 are invokations to smart contracts. In addition to some interop service and OpCode adjustments, the larger features of NEO3 include:
* Add [Manifest](#Manifest) file to describe the characteristics of the contract
* Add [Native Contract](#Native-Contract)
* Reduce the handling [system fee](#System-Fee) for OpCode and interop service
* Increase the contract's support for [access to network resources](#Internet-Resources-Access).
## Manifest
Now each contract requires a corresponding manifest file to describe its properties, including: Groups, Features, ABI, Permissions, Trusts, SafeMethods.
Take a manifest file for example：
```json
{
  "groups": [
    {
      "pubKey": "",
      "signature": ""
    }
  ],
  "features": {
    "storage": false,
    "payable": false
  },
  "abi": null,
  "permissions": [
    {
      "contract": "*",
      "methods": "*"
    }
  ],
  "trusts": "*",
  "safemethods": "*"
}
```
- **Groups**：Declare that the group to which this contract belongs can support multiple, each group is represented by a public key and signature.
- **Features**：Declare the features of a smart contract. Where the attribute value `storage` indicates that the contract can access the storage area, the `payable` table indicates the contract can accept the transfer of assets.
- **ABI**：Declare the interface information of the smart contract, you can refer to [NEP-3](https://github.com/neo-project/proposals/blob/master/nep-3.mediawiki)。The basic properties of the interface include:
  - Hash: Hex-encoded contract script hash;
  - EntryPoint: Provides details of the contract entry method, including method name, method parameters, and method return value;
  - Methods: An array of details of the contract method;
  - Events: An array of contract events. Based on ABI information, mutual call between contracts can be realized。
- **Permissions**：Declare other contracts and methods that the contract can call. When the contract call is executed, the configuration in Permission is checked.
Permissions, if there is no corresponding permission, the call operation will fail.
- **Trusts**：Declare which contracts or which contract groups can be safely called。
- **SafeMethods**：Declare which method is SafeMethod, SafeMethod usually does not modify the memory area, only the method of reading the blockchain data, will not return a warning message to the user interface when called。

## Trigger
Triggers allow contracts to execute different logic depending on the usage scenario。

* **System** This trigger adds a trigger type to NEO3. When the node triggers after receiving a new block, it will only trigger the execution of NativeContract. When the node receives the new block, all NativeContract's onPersist methods are called before persistence. The trigger mode is System.。
* **Application** The purpose of the application trigger is to call the contract as an application function. The application function can accept multiple parameters, change the state of the blockchain, and return any type of return value. Here is a simple c# smart contract：

  ```csharp
  public static Object Main(string operation, params object[] args)
  {
    if (Runtime.Trigger == TriggerType.Application)
    {
        if (operation == "FunctionA") return FunctionA(args);
    }  
  }
  public static bool FunctionA(params object[] args)
  {
    //some code  
  }
  ```

All transactions in NEO3 are contract calls. When a transaction is broadcast and confirmed, the smart contract is executed by the consensus node, and the normal node does not execute the smart contract when forwarding the transaction. Successful execution of a smart contract does not represent the success of the transaction, and the success of the transaction does not determine the success of the smart contract execution.

* **Verifycation** The purpose of the validation trigger is to call the contract as a validation function that accepts multiple arguments and should return a valid Boolean value indicating the validity of the transaction or block.

When you want to transfer money from the A account to the B account, the verification contract will be triggered. All the nodes that receive the transaction (including the normal node and the consensus node) will verify the contract of the A account. If the return value is true, the transfer is successful. . If false is returned, the transfer fails.

If the authentication contract fails to execute, the transaction will not be written to the blockchain.

The following code is a simple example of a validation contract that returns true when condition A is satisfied, ie the transfer is successful. Otherwise it returns false and the transfer fails.
  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.Neo;

  public static bool Main(byte[] signature)
  {
      if (/*条件A*/)
          return true;
      else
          return false;
  }
  ```

The following code works basically the same as above, but the runtime trigger is judged, and the code of the verification part is executed only when the trigger is a verification trigger, which is useful in complex smart contracts. A smart contract implements a variety of triggers, and the trigger should be evaluated in the Main method.
  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.Neo;

  public static bool Main(byte[] signature)
  {
      if (Runtime.Trigger == TriggerType.Verification)
      {
          if (/*条件A*/)
                  return true;
              else
                  return false;
      }  
  }
  ```

## Native Contract
### Introduction
Native contracts are executed directly in native code, not contracts that run in a virtual machine. The native contract discloses its service name for other contracts to call. Currently available NativeContract includes NeoToken, GasToken, and PolicyToken.

#### NeoToken

NEO, referred to as Neo's governance token, is used to enforce management of the Neo network and is compliant with the NEP-5 standard. The total amount of NEO is 100 million, and the minimum unit is 1, and it is indivisible. Neo is registered in the Genesis block. The specific interface details are as follows：

- **unClaimGas**：Get the specified height, the number of unclaimed GAS
  
 ```csharp
[ContractMethod(0_03000000, 
ContractParameterType.Integer, 
ParameterTypes = new[] { 
ContractParameterType.Hash160, 
ContractParameterType.Integer 
}, 
ParameterNames = new[] { "account", "end" }, 
SafeMethod = true)]
private StackItem UnclaimedGas(ApplicationEngine engine, VMArray args)
 ```

Parameters

|Parameter | Type | Description |
|--|--|--|
|account|Hash160| ScriptHash of the account |
| end | Integer | the height to be queried |

Return Value

| Type | Description |
|--|--|
|Integer| GAS unclaimed |

费用(GAS)
    
*0.03*

- **RegisterValidator**：regist to become candidate

```csharp
[ContractMethod(0_05000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] { 
ContractParameterType.PublicKey 
}, 
ParameterNames = new[] { "pubkey" })]
private StackItem RegisterValidator(ApplicationEngine engine, VMArray args)
```
Parameters

| Parameter | Type | Description |
|--|--|--|
| pubKey	| PublicKey | public key |

Return

| Type | Description |
|--|--|
| Boolean | result，true：sucess， false：fail |

Fee(GAS)

*0.05*

- **getRegisteredValidators**：Get all validators

```csharp
[ContractMethod(1_00000000, 
ContractParameterType.Array,
SafeMethod = true)]
private StackItem GetRegisteredValidators(ApplicationEngine engine, VMArray args)
```

Parameters 

*none*  

Return

| Type | Description |
|--|--|
| Array | public keys of all validators |

Fee(GAS)  

*1.00*

- **getValidators**: Get all current validators

```csharp
[ContractMethod(1_00000000, ContractParameterType.Array, SafeMethod = true)]
private StackItem GetValidators(ApplicationEngine engine, VMArray args)
```

Parameters

*none*

Return

| Type | Description |
|--|--|
| Array | public keys of all current validators |

费用(GAS) 

*1.00*

- **getNextBlockValidators**: validators of next block

```csharp
[ContractMethod(1_00000000, ContractParameterType.Array, SafeMethod = true)]
private StackItem GetNextBlockValidators(ApplicationEngine engine, VMArray args)
```

Parameters 

*none*  

Return

| Type | Description |
|--|--|
| Array | all validators' public keys of next block |

Fee(GAS)  

*1.00*

- **vote**：Vote for to validators

```csharp
[ContractMethod(5_00000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] {         
ContractParameterType.Hash160, 
ContractParameterType.Array }, 
ParameterNames = new[] { "account", "pubkeys" })]
private StackItem Vote(ApplicationEngine engine, VMArray args)
```

参数列表

|Parameter | Type | Description |
|--|--|--|
| account	| Hash60 | voter's ScriptHash |
| pubkeys | Array | the public keys of validators one vote to |

返回值

| Type | Description |
|--|--|
| Boolean | result，true：success， false：fail |

费用(GAS)  

*5.00*

- **name***： Name of the token

```csharp
[ContractMethod(0, ContractParameterType.String, Name = "name", SafeMethod = true)]
protected StackItem NameMethod(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| String | neme of the token |

Fee(GAS)

*0.00*

- **symbol***：Symbol of the token
```csharp
[ContractMethod(0, ContractParameterType.String, Name = "symbol", SafeMethod = true)]
protected StackItem SymbolMethod(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| String | Symbol of the token |

Fee(GAS) 

*0.00*

- **decimals***: Decimals of the token

```csharp
[ContractMethod(0, ContractParameterType.Integer, Name = "decimals", SafeMethod = true)]
protected StackItem DecimalsMethod(ApplicationEngine engine, VMArray args)
```

Parameters 

*none*  

Return

| Type | Description |
|--|--|
| uint | decimal |

Fee(GAS)  

*0.00*

- **totalSupply***: Total supply

```csharp
[ContractMethod(0_01000000, ContractParameterType.Integer, SafeMethod = true)]
protected StackItem TotalSupply(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| BigInteger | total supply |

Fee(GAS)  

*0.01*

- **balanceOf***: Balance of an account

```csharp
[ContractMethod(0_01000000, 
    ContractParameterType.Integer, 
    ParameterTypes = new[] { ContractParameterType.Hash160 }, 
    ParameterNames = new[] { "account" }, 
    SafeMethod = true)]
protected StackItem BalanceOf(ApplicationEngine engine, VMArray args)
```

Parameters

|Parameter | Type | Description |
|--|--|--|
|account|Hash160| ScriptHash of an account |

Return

| Type | Description |
|--|--|
|Integer| balance |

Fee(GAS)  

*0.01*

- **transfer***: transfer token from one to another

```csharp
[ContractMethod(0_08000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] { 
ContractParameterType.Hash160, 
ContractParameterType.Hash160, 
ContractParameterType.Integer 
}, 
ParameterNames = new[] { "from", "to", "amount" })]
protected StackItem Transfer(ApplicationEngine engine, VMArray args)
```

Parameters

|Parameter | Type | Description |
|--|--|--|
|from|Hash160| scriptHash who want to transfer |
|from|Hash160| scriptHash of the receiver |
|amount|Integer|amount of the token|

Return

| Type | Description |
|--|--|
|Boolean| Result，true：success，false：fail |

Fee(GAS)  

*0.08*

> Method marked * is [NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki) standard interface.

#### GasToken

Referred to as GAS, Neo's fuel token, used to pay the handling fee, in line with NEP-5 standards. Transaction costs and consensus node incentives on the Neo network are paid in GAS. The total amount of GAS is 100 million, and the minimum unit is 0.00000001. GAS is registered in the founding block but not distributed.
GAS distribution mechanism: When a new block is generated, a new GAS is generated, and the generated GAS and system fees are distributed to NEO holders. The calculation and distribution of GAS is automatically performed when the NEO balance in the user's address changes. The calculation method is as follows:  

![GAS distribution](../../images/GAS_distribution.png) 

Where `m` is the height of the block where the user last extracted the GAS, `n` is the current block height, and `NEO` is the number of NEOs held by the user during `m` to `n`. `BlockBonus` represents the amount of GAS that each block can extract. `SystemFee`  represents the sum of the system fees for all transactions in this block.

GasToken methods details following：

- **getSysFeeAmount**: Total system fee till specific height

```csharp
[ContractMethod(0_01000000, 
    ContractParameterType.Integer, 
    ParameterTypes = new[] { ContractParameterType.Integer }, 
    ParameterNames = new[] { "index" }, 
    SafeMethod = true)]
private StackItem GetSysFeeAmount(ApplicationEngine engine, VMArray args)
```

Parameters

| Parameter | Type | Description |
|--|--|--|
|index|int| the height one want to query |

Return

| Type | Description |
|--|--|
|Integer| total system fee |

Fee(GAS)  

*0.01*

- **name***: Name of the token
```csharp
[ContractMethod(0, ContractParameterType.String, Name = "name", SafeMethod = true)]
protected StackItem NameMethod(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| String | name of the token |

Fee(GAS)  

*0.00*

- **symbol**: symbol of the token

```csharp
[ContractMethod(0, ContractParameterType.String, Name = "symbol", SafeMethod = true)]
protected StackItem SymbolMethod(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

返回值

| Type | Description |
|--|--|
| String | symbol of the token |

Fee(GAS) 

*0.00*

- **decimals***: Decimals of the token

```csharp
[ContractMethod(0, ContractParameterType.Integer, Name = "decimals", SafeMethod = true)]
protected StackItem DecimalsMethod(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| uint | decimal |

Fee(GAS)  

*0.00*

- **totalSupply***: Total supply

```csharp
[ContractMethod(0_01000000, ContractParameterType.Integer, SafeMethod = true)]
protected StackItem TotalSupply(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| String | total supply |

Fee(GAS)  

*0.01*

- **balanceOf***: Balance of a specific account

```csharp
[ContractMethod(0_01000000, 
ContractParameterType.Integer, 
ParameterTypes = new[] { ContractParameterType.Hash160 }, 
ParameterNames = new[] { "account" }, 
SafeMethod = true)]
protected StackItem BalanceOf(ApplicationEngine engine, VMArray args)
```

Parameters

| Parameter | Type | Description |
|--|--|--|
|account|Hash160| ScriptHash of an account to query |

Return

| Type | Description |
|--|--|
|Integer| balance |

Fee(GAS) 

*0.01*

- **transfer***: transfer token from one to another

```csharp
[ContractMethod(0_08000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] { 
ContractParameterType.Hash160, 
ContractParameterType.Hash160, 
ContractParameterType.Integer 
}, 
ParameterNames = new[] { "from", "to", "amount" })]
protected StackItem Transfer(ApplicationEngine engine, VMArray args)
```

参数

| Parameter | Type | Description |
|--|--|--|
|from|Hash160|ScriptHash of the account transfer from |
|from|Hash160|ScriptHash of the receiver |
|amount|Integer| amount to transfer |

返回值

| Type | Description |
|--|--|
|Boolean| result，true：success，false：fail |

Fee(GAS) 

*0.08*

> Method marked * is [NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki) standard interface

#### PolicyToken

The contract for configuring the formula strategy saves relevant parameters in the consensus process, such as the maximum number of transactions in the block, the handling fee per byte, and so on. The interface is described in detail below：

- getMaxTransactionPerBlock  
Get maximum transactions count limitation per block.

```csharp
[ContractMethod(0_01000000, ContractParameterType.Integer, SafeMethod = true)]
private StackItem GetMaxTransactionsPerBlock(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| Integer | maximum transaction count per block  |

Fee(GAS)  

*0.01*

- **GetFeePerByte**： Get system fee per byte

```csharp
[ContractMethod(0_01000000, ContractParameterType.Integer, SafeMethod = true)]
private StackItem GetFeePerByte(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| Integer | fee per byte |

Fee(GAS)  

*0.01*

- **getBlockedAccounts**: Get the addresses in the blacklist

```csharp
[ContractMethod(0_01000000, ContractParameterType.Array, SafeMethod = true)]
private StackItem GetBlockedAccounts(ApplicationEngine engine, VMArray args)
```

Parameters  

*none*  

Return

| Type | Description |
|--|--|
| Array | blacklist |

Fee(GAS)  

*0.01*

- **setMaxTransactionsPerBlock**: Set the miximum limitation of a block

```csharp
[ContractMethod(0_03000000, ContractParameterType.Boolean, ParameterTypes = new[] { ContractParameterType.Integer }, ParameterNames = new[] { "value" })]
private StackItem SetMaxTransactionsPerBlock(ApplicationEngine engine, VMArray args)
```

Parameters  

| Parameter | Type | Description |
|--|--|--|
| value | Integer | the maximum limitation |

返回值

| Type | Description |
|--|--|
| Boolean | Result.true：success，false：fail |

Fee(GAS)  

*0.03*

- **setFeePerByte**：Set system fee per byte.

```csharp
[ContractMethod(0_03000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] { ContractParameterType.Integer }, 
ParameterNames = new[] { "value" })]
private StackItem SetFeePerByte(ApplicationEngine engine, VMArray args)
```

Parameters  

| Parameter | Type | Description |
|--|--|--|
| value | Integer | system fee per byte |

返回值

| Type | Description |
|--|--|
| Boolean | Result. true：success. false：fail. |

费用(GAS)  

*0.03*

- **blockAccount**：Add an account into blacklist.

```csharp
[ContractMethod(0_03000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] { ContractParameterType.Hash160 }, 
ParameterNames = new[] { "account" })]
private StackItem BlockAccount(ApplicationEngine engine, VMArray args)
```

Parameters  

| Parameter | Type | Description |
|--|--|--|
| account | Hash160 | account |

Return

| Type | Description |
|--|--|
| Boolean | Result. true：success，false：failed |

Fee(GAS)  

*0.03*

- **unblockAccount**：Remove account from blacklist.

```csharp
[ContractMethod(0_03000000, 
ContractParameterType.Boolean, 
ParameterTypes = new[] { ContractParameterType.Hash160 }, 
ParameterNames = new[] { "account" })]
private StackItem UnblockAccount(ApplicationEngine engine, VMArray args)
```

Parameters  

| Parameter | Type | Description |
|--|--|--|
| account | Hash160 | account to remove from blacklist |

Return

| Type | Description |
|--|--|
| Boolean | Result. true: success, false：failed. |

Fee(GAS)  

*0.03*


**More NativeContract, Please wait**

### NativeContract Deploy
NativeContract is deployed in the Genesis Block calling the Neo.Native.Deploy Interop Interface, which can only be executed in the Genesis Block.
### NativeContract Invokation
There are two ways to call NativeContract. The first one is called by the script's ScriptHash, just like the normal contract, and the other is NativeContract-specific, which is called directly through the interop service.[Usage of Interop Service](#Usage-of-Interop-Service)

- **Special Way**：Using interop serivce.
  Each NativeContract registers an interop interface with the name of its ServiceName, which belongs to the Neo.Native namespace.
  The ServiceName corresponding to each NativeContract is as follows：

  |NativeContract|ServiceName|
  |---|---|
  |NeoToken|Neo.Native.Tokens.NEO|
  |GasToken|Neo.Native.Tokens.GAS|
  |PolicyToken|NeoNeo.Native.Policy|

  For example, in c# writing smart contracts, if you need to call GAS transfer, you can write as follows：
  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.Neo;

  namespace MyContract
  {
    public class MyContract: SmartContract
    {
      public static object main(string method, object[] args)
      {
        if (method == "test") {
          if (args.Length < 3) return false;
          return Native.Tokens.GAS("transfer", args);
        }
      }
    }
  }
  ```
- **General Way**：Using ScriptHash and System.Contract.Call.

  NativeContract's ScriptHash is fixed and can be called with the System.Contract.Call interop interface and NativeContract's ScriptHash just like any other normal contract. The existing NativeContract ScriptHash is as follows：

  |NativeContract|ScriptHash|
  |---|---|
  |NeoToken| 0x43cf98eddbe047e198a3e5d57006311442a0ca15 |
  |GasToken|0xa1760976db5fcdfab2a9930e8f6ce875b2d18225|
  |PolicyToken|0x9c5699b260bd468e2160dd5d45dfd2686bba8b77|

  For details please refer [Contract Invokation](#Contract-Invokation)
  
## Interop Service
The interoperability service layer provides APIs for smart contractes to access blockchain data. These APIs provide access to block information, transaction information, contract information, asset information, and more. In addition to this, the interoperable service layer provides a persistent storage area for each contract. Each of Neo's smart contracts is optionally enabled with a private storage area. The storage area is in the form of a key-value. The Neo Smart Contract is determined by the callee of the contract to persist the context of the storage area, not the caller. To decide. Of course, after the caller needs to pass his storage context to the callee (ie, complete the authorization), the callee can perform read and write operations. Interoperability services are divided into the System part and the Neo part.
### Interop Service Principle
When the Neo program starts, it registers a series of interop interfaces to the virtual machine for smart contract execution.
* **ServiceName** Each interoperation has a name, for example `System.Contract.Call`。
* **Mapping Method** Each interoperable service has a corresponding native method, such as`private static bool Contract_Call(ApplicationEngine engine)`，It is the method actually called in the client's native code.
* **System Fee** Each interop interface has its own system fee calculation method or fixed system fee.
* **Trigger** Each interop interface has a supported trigger mode, such as `TriggerType.All` supports all triggers.

When registering, the service name of the interop interface of the Neo client, the mapping method, the system fee calculation method, and the supported trigger are encapsulated into an InteropDescriptor stored in the Dictionary, and the index is the hash value of the name of the interop interface. The hash method is:
`BitConverter.ToUInt32(Encoding.ASCII.GetBytes(ServiceName).Sha256(), 0)`

For example: The hash of `System.Contract.Call` is `0x627d5b52`

### Usage of Interop Service

* **SmartContract** The use of interoperable interfaces in smart contracts is provided by the corresponding smart contract development framework, which can be called directly. When compiled, it will be compiled by the compiler into operator instructions that can be executed in NEO-VM.
* **Script in Transaction** Usually you need to manually splice the execution script. Using the hash value of the interface name of the interop service and the SYSCALL operator.

For example, if you want to call the `name` method of contract `0x43cf98eddbe047e198a3e5d57006311442a0ca15` via `System.Contract.Call`：
  ```
  PUSH0
  NEWARRAY
  PUSHBYTES4  6e616d65
  PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15
  SYSCALL     0x627d5b52
  ```
  C# code：
  ```csharp
  ScriptBuilder sb = new ScriptBuilder()
  sb.EmitPush(0);
  sb.Emit(OpCode.NEWARRAY);
  sb.EmitPush(operation);
  sb.EmitPush(scriptHash);
  sb.EmitSysCall(InteropService.System_Contract_Call); //Call interop service
  byte[] script = sb.ToArray();
  ```
  For example, in c#, it can be called as follows：
  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.Neo;

  namespace MyContract
  {
    public class MyContract: SmartContract
    {
      public static readonly byte[] GAS = HexToBytes("0xa1760976db5fcdfab2a9930e8f6ce875b2d18225");
      public static object main(string method, object[] args)
      {
        if (method == "transferGAS") {
          if (args.Length < 3) return false;
          return Contract.Call(GAS, "Transfer", args);
        }
      }
    }
  }
  ```

Interoperability services are divided into System part and Neo part. The specific interfaces are as follows:

### System Part

- System.ExecutionEngine.GetScriptContainer  

  | Description | Get SmartContract script container |
  |--|--|
  | C# Function | byte[] GetScriptContainer() |

- System.ExecutionEngine.GetExecutingScriptHash

  | Description | Get ScriptHash of executing contract |
  |--|--|
  | C# Function| byte[] GetExecutingScriptHash() |

- System.ExecutionEngine.GetCallingScriptHash

  | Description | Get ScriptHash of calling contract |
  |--|--|
  | C# Function| byte[] GetExecutingScriptHash() |

- System.ExecutionEngine.GetEntryScriptHash

  | Description | Get the scripthash of the entry point for the smart contract (the starting point of the contract call chain) |
  |--|-- |
  | C# Function| byte[] GetEntryScriptHash() |

- System.Runtime.Platform

  | Description | Get the executing platform of contract |
  |--|--|
  | C# Function| string Platform() |

- System.Runtime.GetTrigger

  | Description | Get the trigger of contract |
  |--|--|
  | C# Function | TriggerType Trigger() |

- System.Runtime.CheckWitness

  | Description |  Verifies that the calling contract has verified the required script hashes of the transaction/block |
  |--|--|
  | C# Function | bool CheckWitness(byte[] hashOrPubKey) |

- System.Runtime.Notify

  | Description | Notifies the client with a notification during smart contract execution |
  |--|--|
  | C# Function | bool Notify(params object[] state) |

- System.Runtime.Log

  | Description | Notifies the client with a log message during smart contract execution |
  |--|--|
  | C# Function | void Log(string message) |

- System.Runtime.GetTime

  | Description | Get the timestamp of current block |
  |--|--|
  | C# Function | uint Time |

- System.Runtime.Serialize

  | Description | serialize an object |
  |--|--|
  | C# Function | object Deserialize(this byte[] source) |

- System.Runtime.Deserialize

  | Description | deserialize a byte array into object |
  |--|--|
  | C# Function | byte[] Serialize(this object source) |

- System.Runtime.GetInvocationCounter

  | Description | Get invoke count of current contract |
  |--|--|
  | C# Function | int GetInvocationCounter() |

- System.Crypto.Verify

  | Description | verify a signature of a message |
  |--|--|
  | C# Function | bool Verify(object message, byte[] signature, byte[] pubKey) |

- System.Blockchain.GetHeight

  | Description | Get height of current block |
  |--|--|
  | C# Function | uint GetHeight() |

- System.Blockchain.GetHeader

  | Description | Get header of a block |
  |--|--|
  | C# Function | Header GetHeader(uint height) |
  || Header GetHeader(byte[] hash)  |

- System.Blockchain.GetBlock

  | Description | Get block by height or hash |
  |--|--|
  | C# Function | Block GetBlock(uint height) |
  || Block GetBlock(byte[] hash)  |

- System.Blockchain.GetTransaction

  | Description | Get transaction by transaction ID |
  |--|--|
  | C# Function | Transaction GetTransaction(byte[] hash) |

- System.Blockchain.GetTransactionHeight

  | Description | Get block height of a transaction by transaction ID |
  |--|--|
  | C# Function | int GetTransactionHeight(byte[] hash) |

- System.Blockchain.GetContract

  | Description | Get contract by scripthash |
  |--|--|
  | C# Function | Contract GetContract(byte[] scriptHash) |

- System.Header.GetIndex

  | Description | Get height from block header |
  |--|--|
  | C# Function | uint Index |

- System.Header.GetHash

  | Description | Get block hash from block header |
  |--|--|
  | C# Function | byte[] Hash |

- System.Header.GetPrevHash

  | Description | Get hash of previous block from block header |
  |--|--|
  | C# Function | byte[] PreHash |

- System.Header.GetTimestamp

  | Description | Get timestamp from block header |
  |--|--|
  | C# Function | byte[] Timestamp |

- System.Block.GetTransactionCount

  | Description | Get transaction count from a block |
  |--|--|
  | C# Function | int GetTransactionCount |

- System.Block.GetTransactions

  | Description | Get all transactions from a block |
  |--|--|
  | C# Function | Transaction[] GetTransactions() |

- System.Block.GetTransaction

  | Description | Get transaction from a block by index |
  |--|--|
  | C# Function | Transaction[] GetTransaction(int index) |

- System.Transaction.GetHash

  | Description | Get hash from a transaction |
  |--|--|
  | C# Function | byte[] Hash |

- System.Contract.Call <a id="contract-call" ></a>

  | Description | invoke contract |
  |--|--|
  | C# Function | void Call(byte[] scriptHash, string method, object[] args) |
  |  | void Call(Contract contract, string method, object[] args) |

- System.Contract.Destroy

  | Description | Destroy current contract |
  |--|--|
  | C# Function | void Destroy() |

- System.Storage.GetContext

  | Description | Get context of current contract |
  |--|--|
  | C# Function | StorageContext GetContext() |
  | Explanation | IsReadOnly of StorageContext is false |

- System.Storage.GetReadOnlyContext

  | Description | Get context of current contract in read-only mode |
  |--|--|
  | C# Function | StorageContext GetContext() |
  | Explanation | IsReadOnly of StorageContext is false |

- System.Storage.Get

  | Description | Get value from storage by key |
  |--|--|
  | C# Function | byte[] Get(StorageContext context, byte[] key) |

- System.Storage.Put

  | Description | Put key-value into storage by current context |
  |--|--|
  | C# Function | byte[] Get(StorageContext context, byte[] key, byte[] value) |

- System.Storage.PutEx

  | Description | Write Key-Value to the storage area according to the flag and storage context. |
  |--|--|
  | C# Function | byte[] Get(StorageContext context, byte[] key, byte[] value, StorageFlags flags) |
  | Explanation | StorageFlags indicates the attributes of the write data. By default, the data can be read and written. If it is Constant, the data cannot be modified or deleted after it is written to the storage area.|

- System.Storage.Delete

  | Description | Delete the stored Key-Value data from the storage area according to the Key value |
  |--|--|
  | C# Function | void Delete(StorageContext context, byte[] key) |
  | Explanation | If the StorageFlags of the data contain Constant, they cannot be deleted. |

- System.StorageContext.AsReadOnly

  | Description | Modify the current context to read-only mode |
  |--|--|
  | C# Function | void AsReadOnly(this StorageContext context) |
  | Explanation | Set IsReadOnly in the StorageContext to true |

### Neo Part

- Neo.Native.Deploy

  | Description | Deploy and initialize all native contracts |
  |--|--|
  | Explanation | Can only be called in the genesis block |

- Neo.Crypto.CheckSig

  | Description | Verify signature of current container by public key |
  |--|--|
  | C# Function | bool CheckSig(byte[] signature, byte[] pubKey) |

- Neo.Crypto.CheckMultiSig

  | Description | Verify the signature of the current script container based on the public key |
  |--|--|
  | C# Function | bool CheckMultiSig(byte[][] signatures, byte[][] pubKeys) |

- Neo.Header.GetVersion

  | Description | Get verison from block header |
  |--|--|
  | C# Function | uint Version |

- Neo.Header.GetMerkleRoot

  | Description | Get root of transactions merkletree from header |
  |--|--|
  | C# Function | byte[] MerkleRoot |

- Neo.Header.GetNextConsensus

  | Description | Get the script hash of the next round validators' multi-signature contract |
  |--|--|
  | C# Function | byte[] NextConsensus |

- Neo.Transaction.GetScript

  | Description | Get the script in the transaction |
  |--|--|
  | C# Function | byte[] Script |

- Neo.Transaction.GetWitnesses

  | Description | Get witnesses from transaction |
  |--|--|
  | C# Function | Witness[] GetWitnesses(this Transaction transaction) |

- Neo.Witness.GetVerificationScript

  | Description | Get verification script from transaction |
  |--|--|
  | C# Function | byte[] VerificationScript |

- Neo.Account.IsStandard

  | Description | check if the account is standard |
  |--|--|
  | C# Function | bool IsStandard(byte[] scriptHash) |

- Neo.Contract.Create

  | Description | deploy contract |
  |--|--|
  | C# Function | Contract Create(byte[] script, string manifest) |
  | Explanation | The content of the script contract cannot exceed 1MB; The content of the manifest cannot exceed 2KB |

- Neo.Contract.Update<a id="contract-update"></a>

  | Description | update contract |
  |--|--|
  | C# Function | Contract Create(byte[] script, string manifest) |
  | Explanation | The content of the script contract cannot exceed 1MB, it cannot be a contract already deployed; the content of the manifest cannot exceed 2KB; the old contract will be destroyed after the upgrade. |

- Neo.Contract.GetScript

  | Description | Get script from contract |
  |--|--|
  | C# Function | byte[] Script |

- Neo.Contract.IsPayable

  | Description | Get if the contract can receive asset |
  |--|--|
  | C# Function | bool IsPayable(this Contract contract) |

- Neo.Storage.Find

   Description | Find the specified prefix content in the storage area in the current storage context |
  |--|--|
  | C# Function | Iterator < byte[], byte[] > Find(StorageContext context, byte[] prefix) |

- Neo.Enumerator.Create

  | Description | Create an enumerator |
  |--|--|
  | C# Function | Enumerator Create(object[] array) |

- Neo.Enumerator.Next

  | Description | Get the next element |
  |--|--|
  | C# Function | bool Next(this Enumerator enumerator) |

- Neo.Enumerator.Value

  | Description | Get the current value |
  |--|--|
  | C# Function | object Next(this Enumerator enumerator) |

- Neo.Enumerator.Concat

  | Description | Connect two enumerators |
  |--|--|
  | C# Function | Enumerator Concat(Enumerator enumerator1, Enumerator enumerator2) |

- Neo.Iterator.Create

  | Description | Create an iterator |
  |--|--|
  | C# Function | Iterator Create(object[] array) |
  | | Iterator Create(Dictionary<object, object> map) |

- Neo.Iterator.Key

  | Description | Get iterator of all keys |
  |--|--|
  | C# Function | object Key(this Iterator it) |

- Neo.Iterator.Keys

  | Description | Get an iterator of all keys |
  |--|--|
  | C# Function | Iterator Keys(this Iterator it) |

- Neo.Iterator.Values

  | Description | Get iterator of all values |
  |--|--|
  | C# Function | Iterator Values(this Iterator it) |

- Neo.Iterator.Concat

  | Description | Connect two iterators |
  |--|--|
  | C# Function | Iterator Concat(Iterator iterator1, Iterator iterator2) |

- Neo.Json.Serialize

  | Description | Serialize a json object to string |
  |--|--|
  | C# Function | string Serialize(JObject jsonObj) |

- Neo.Json.Deserialize

  | Description | deserialize a string to Json object |
  |--|--|
  | C# Function | JObject Deserialize(string jsonStr) |

## System Fee

| OpCode | Systetm Fee (GAS) |
|---|---|
| PUSH0 | 0.00000030 |
| PUSHBYTES1 ~ PUSHBYTES75 | 0.00000120 |
| PUSHDATA1 | 0.00000180 |
| PUSHDATA2 | 0.00013000 |
| PUSHDATA4 | 0.00110000 |
| PUSHM1 | 0.00000030 |
| PUSH1 ~ PUSH16 | 0.00000030 |
| NOP | 0.00000030 |
| JMP | 0.00000070 |
| JMPIF | 0.00000070 |
| JMPIFNOT | 0.00000070 |
| CALL | 0.00022000 |
| RET | 0.00000040 |
| SYSCALL | 0 |
| DUPFROMALTSTACKBOTTOM | 0.00000060 |
| DUPFROMALTSTACK | 0.00000060 |
| TOALTSTACK | 0.00000060 |
| FROMALTSTACK | 0.00000060 |
| XDROP | 0.00000400 |
| XSWAP | 0.0000006 |
| XTUCK | 0.000004 |
| DEPTH | 0.0000006 |
| DROP 	| 0.0000006 |
| DUP 	| 0.0000006 |
| NIP 	| 0.0000006 |
| OVER 	| 0.0000006 |
| PICK 	| 0.0000006 |
| ROLL 	| 0.000004 |
| ROT 	| 0.0000006 |
| SWAP 	| 0.0000006 |
| TUCK 	| 0.0000006 |
| CAT 	| 0.0008 |
| SUBSTR 	| 0.0008 |
| LEFT 	| 0.0008 |
| RIGHT 	| 0.0008 |
| SIZE 	| 0.0000006 |
| INVERT 	| 0.000001 |
| AND 	| 0.000002 |
| OR 	| 0.000002 |
| XOR 	| 0.000002 |
| EQUAL 	| 0.000002 |
| INC 	| 0.000001 |
| DEC 	| 0.000001 |
| SIGN 	| 0.000001 |
| NEGATE 	| 0.000001 |
| ABS 	| 0.000001 |
| NOT 	| 0.000001 |
| NZ 	| 0.000001 |
| ADD 	| 0.000002 |
| SUB 	| 0.000002 |
| MUL 	| 0.000003 |
| DIV 	| 0.000003 |
| MOD 	| 0.000003 |
| SHL 	| 0.000003 |
| SHR 	| 0.000003 |
| BOOLAND 	| 0.000002 |
| BOOLOR 	| 0.000002 |
| NUMEQUAL 	| 0.000002 |
| NUMNOTEQUAL 	| 0.000002 |
| LT 	| 0.000002 |
| GT 	| 0.000002 |
| LTE 	| 0.000002 |
| GTE 	| 0.000002 |
| MIN 	| 0.000002 |
| MAX 	| 0.000002 |
| WITHIN 	| 0.000002 |
| SHA1 	| 0.003 |
| SHA256 	| 0.01 |
| ARRAYSIZE 	| 0.0000015 |
| PACK 	| 0.00007 |
| UNPACK 	| 0.00007 |
| PICKITEM 	| 0.0027 |
| SETITEM 	| 0.0027 |
| NEWARRAY 	| 0.00015 |
| NEWSTRUCT 	| 0.00015 |
| NEWMAP 	| 0.000002 |
| APPEND 	| 0.00015 |
| REVERSE 	| 0.000005 |
| REMOVE 	| 0.000005 |
| HASKEY 	| 0.0027 |
| KEYS 	| 0.000005 |
| VALUES 	| 0.00007 |
| THROW 	| 0.0000003 |
| THROWIFNOT 	| 0.0000003 |

| Interop Service | System Fee (GAS) |
|--|--|
| System.ExecutionEngine.GetScriptContainer | 0.0000025  |
| System.ExecutionEngine.GetExecutingScriptHash| 0.000004  |
| System.ExecutionEngine.GetCallingScriptHash | 0.000004  |
| System.ExecutionEngine.GetEntryScriptHash | 0.000004  |
| System.Runtime.Platform | 0.0000025  |
| System.Runtime.GetTrigger | 0.0000025  |
| System.Runtime.CheckWitness | 0.0003  |
| System.Runtime.Notify | 0.0000025  |
| System.Runtime.Log | 0.003  |
| System.Runtime.GetTime | 0.0000025  |
| System.Runtime.Serialize | 0.001  |
| System.Runtime.Deserialize | 0.005  |
| System.Runtime.GetInvocationCounter | 0.000004  |
| System.Crypto.Verify | 0.01  |
| System.Blockchain.GetHeight | 0.000004  |
| System.Blockchain.GetHeader | 0.00007  |
| System.Blockchain.GetBlock | 0.025  |
| System.Blockchain.GetTransaction | 0.01  |
| System.Blockchain.GetTransactionHeight | 0.01  |
| System.Blockchain.GetContract | 0.01  |
| System.Header.GetIndex | 0.000004  |
| System.Header.GetHash | 0.000004  |
| System.Header.GetPrevHash | 0.000004  |
| System.Header.GetTimestamp | 0.000004  |
| System.Block.GetTransactionCount | 0.000004  |
| System.Block.GetTransactions | 0.0001  |
| System.Block.GetTransaction | 0.000004  |
| System.Transaction.GetHash | 0.000004  |
| System.Contract.Call | 0.01  |
| System.Contract.Destroy | 0.01  |
| System.Storage.GetContext | 0.000004  |
| System.Storage.GetReadOnlyContext | 0.000004  |
| System.Storage.Get | 0.01  |
| System.Storage.Put | (Key.Size + Value.Size) * GasPerByte |
| System.Storage.PutEx | (Key.Size + Value.Size) * GasPerByte |
| System.Storage.Delete | 0.01  |
| System.StorageContext.AsReadOnly | 0.000004  |
| Neo.Native.Deploy | 0 |
| Neo.Crypto.CheckSig| 0.01  |
| Neo.Crypto.CheckMultiSig| 0.01 * n |
| Neo.Header.GetVersion| 0.000004  |
| Neo.Header.GetMerkleRoot| 0.000004  |
| Neo.Header.GetNextConsensus| 0.000004  |
| Neo.Transaction.GetScript| 0.000004  |
| Neo.Transaction.GetWitnesses| 0.0001  |
| Neo.Witness.GetVerificationScript| 0.000004  |
| Neo.Account.IsStandard| 0.0003  |
| Neo.Contract.Create| (Script.Size + Manifest.Size) * GasPerByte |
| Neo.Contract.Update| (Script.Size + Manifest.Size) * GasPerByte |
| Neo.Contract.GetScript| 0.000004  |
| Neo.Contract.IsPayable| 0.000004  |
| Neo.Storage.Find| 0.01  |
| Neo.Enumerator.Create| 0.000004  |
| Neo.Enumerator.Next| 0.01  |
| Neo.Enumerator.Value| 0.000004  |
| Neo.Enumerator.Concat| 0.000004  |
| Neo.Iterator.Create| 0.000004  |
| Neo.Iterator.Key| 0.000004  |
| Neo.Iterator.Keys| 0.000004  |
| Neo.Iterator.Values| 0.000004  |
| Neo.Iterator.Concat| 0.000004  |
| Neo.Json.Serialize| 0.001  |
| Neo.Json.Deserialize| 0.005  |

## Network Resources Access
## Contract Invokation
  When writing contract, you can invoke other contracts through the interop service provided by the development framework[System.Contract.Call](#contract-call)
  For example, in C#, it can be like as follows：
  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.Neo;

  namespace MyContract
  {
    public class MyContract: SmartContract
    {
      public static readonly byte[] GAS = HexToBytes("0xa1760976db5fcdfab2a9930e8f6ce875b2d18225");
      public static object main(string method, object[] args)
      {
        if (method == "transferGAS") {
          if (args.Length < 3) return false;
          return Contract.Call(GAS, "transfer", args);
        }
      }
    }
  }
  ```
  Usually you need to manually splice the execution script. At this point you need to use the interop service [System.Contract.Call] (#contract-call) and the script's ScriptHash to call the contract.[How to use interop services](#Usage-of-Interop-Service)

  For example, if you want to invoke method `transfer` of contract `0x43cf98eddbe047e198a3e5d57006311442a0ca15`：

  ```
  PUSHBYTES4  0x00e1f505
  PUSHBYTES20 0xfb5fd311a3ae2b2c8ab6b63c10502f9cf58ebeed
  PUSHBYTES20 0xa6b9b510a67009e61f4f95115c188437f76dd2d0
  PUSH3
  PACK                                                      //Third parameter, parameter list
  PUSHBYTES4  0x6e616d65                                    //Second parameter, method
  PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15    //First parameter, contract's ScriptHash
  SYSCALL     0x627d5b52                                    //Calling interop service by hash
  ```

  C# code：

  ```csharp
  ScriptBuilder sb        = new ScriptBuilder();
  UInt160 scriptHash      = UInt160.Parse("0x43cf98eddbe047e198a3e5d57006311442a0ca15");
  string operation        = "transfer";
  ContractParameter from  = new ContractParameter(ContractParameterType.Hash160);
  ContractParameter to    = new ContractParameter(ContractParameterType.Hash160);
  ContractParameter value = new ContractParameter(ContractParameterType.Integer);

  from.SetValue("0xa6b9b510a67009e61f4f95115c188437f76dd2d0");
  to.SetValue("0xfb5fd311a3ae2b2c8ab6b63c10502f9cf58ebeed");
  value.SetValue("100000000");

  sb.EmitAppCall(scriptHash, operation, new ContractParameter[]{from, to, value});
  byte[] script = sb.ToArray();

  public static ScriptBuilder EmitAppCall(this ScriptBuilder sb, UInt160 scriptHash, string operation, params ContractParameter[] args)
  {
      for (int i = args.Length - 1; i >= 0; i--)
          sb.EmitPush(args[i]);
      sb.EmitPush(args.Length);
      sb.Emit(OpCode.PACK);
      sb.EmitPush(operation);
      sb.EmitPush(scriptHash);
      sb.EmitSysCall(InteropService.System_Contract_Call);
      return sb;
  }
  ```

## Contract Update
If you need to modify the logic of the contract after the contract is deployed and retain the data in the storage area, you can use the contract upgrade, but you need to implement the upgrade interface in the old contract. Call the [Neo.Contract.Update](#contract-update) method in the old contract upgrade interface.
When the upgrade interface is called in the old contract, the method will build a new smart contract based on the parameters passed in. If the old contract has a storage area, the old contract's storage area will be transferred to the new contract. After the upgrade is complete, the old contract will be deleted. If the old contract has a storage area, the storage area will also be deleted. The old contract will then be unavailable and the Hash value of the new contract will need to be used.
The C# code is as follows:

```csharp
using Neo.SmartContract.Framework;
using Neo.SmartContract.Framework.Neo;
using Neo.SmartContract.Framework.System;

public static bool Main(string method, params object[] args){
    if (method == "update") {
      if (args.Length < 2) return false;
      if (!Runtime.CheckWitness()) return false;
      byte[] newScript = args[0];
      string manifest = (string)args[1];
      update(newScript, manifest);
      return true;
    }
    return true;
}
void update(byte[] newScript, string manifest)
{
  Contract.Update(newScript, manifest);
}
```

## Contract Destroy
The smart contract supports the destruction operation after the release, but the destruction interface needs to be reserved in the old contract.
Contract destruction mainly calls the `System.Contract.Destroy` method.

C# code：

  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.System;

  public static bool Main(string method, params object[] args){
      if (method == "destroy") {
        destroy();
    }
      return true;
  }

  void destroy(){
    Contract.Destroy();
  }
  ```