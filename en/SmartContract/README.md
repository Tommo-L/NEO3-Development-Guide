<!-- TOC -->

- [Smart Contracts](#smart-contracts)
    - [Changes in NEO3](#changes-in-neo3)
    - [Manifest](#manifest)
    - [Trigger](#trigger)
    - [Native Contract](#native-contract)
        - [Introduction](#introduction)
            - [NeoToken](#neotoken)
            - [GasToken](#gastoken)
            - [PolicyToken](#policytoken)
        - [NativeContract Deployment](#nativecontract-deployment)
        - [NativeContract Invocation](#nativecontract-invocation)
    - [Interop Service](#interop-service)
        - [Principle of Interop Service](#principle-of-interop-service)
        - [Usage of Interop Service](#usage-of-interop-service)
        - [System Part](#system-part)
            - [System.ExecutionEngine.GetScriptContainer](#systemexecutionenginegetscriptcontainer)
            - [System.ExecutionEngine.GetExecutingScriptHash](#systemexecutionenginegetexecutingscripthash)
            - [System.ExecutionEngine.GetCallingScriptHash](#systemexecutionenginegetcallingscripthash)
            - [System.ExecutionEngine.GetEntryScriptHash](#systemexecutionenginegetentryscripthash)
            - [System.Runtime.Platform](#systemruntimeplatform)
            - [System.Runtime.GetTrigger](#systemruntimegettrigger)
            - [System.Runtime.CheckWitness](#systemruntimecheckwitness)
            - [System.Runtime.Notify](#systemruntimenotify)
            - [System.Runtime.Log](#systemruntimelog)
            - [System.Runtime.GetTime](#systemruntimegettime)
            - [System.Runtime.Serialize](#systemruntimeserialize)
            - [System.Runtime.Deserialize](#systemruntimedeserialize)
            - [System.Runtime.GetInvocationCounter](#systemruntimegetinvocationcounter)
            - [System.Crypto.Verify](#systemcryptoverify)
            - [System.Blockchain.GetHeight](#systemblockchaingetheight)
            - [System.Blockchain.GetHeader](#systemblockchaingetheader)
            - [System.Blockchain.GetBlock](#systemblockchaingetblock)
            - [System.Blockchain.GetTransaction](#systemblockchaingettransaction)
            - [System.Blockchain.GetTransactionHeight](#systemblockchaingettransactionheight)
            - [System.Blockchain.GetContract](#systemblockchaingetcontract)
            - [System.Header.GetIndex](#systemheadergetindex)
            - [System.Header.GetHash](#systemheadergethash)
            - [System.Header.GetPrevHash](#systemheadergetprevhash)
            - [System.Header.GetTimestamp](#systemheadergettimestamp)
            - [System.Block.GetTransactionCount](#systemblockgettransactioncount)
            - [System.Block.GetTransactions](#systemblockgettransactions)
            - [System.Block.GetTransaction](#systemblockgettransaction)
            - [System.Transaction.GetHash](#systemtransactiongethash)
            - [System.Contract.Call](#systemcontractcall)
            - [System.Contract.Destroy](#systemcontractdestroy)
            - [System.Storage.GetContext](#systemstoragegetcontext)
            - [System.Storage.GetReadOnlyContext](#systemstoragegetreadonlycontext)
            - [System.Storage.Get](#systemstorageget)
            - [System.Storage.Put](#systemstorageput)
            - [System.Storage.PutEx](#systemstorageputex)
            - [System.Storage.Delete](#systemstoragedelete)
            - [System.StorageContext.AsReadOnly](#systemstoragecontextasreadonly)
        - [Neo Part](#neo-part)
            - [Neo.Native.Deploy](#neonativedeploy)
            - [Neo.Crypto.CheckSig](#neocryptochecksig)
            - [Neo.Crypto.CheckMultiSig](#neocryptocheckmultisig)
            - [Neo.Header.GetVersion](#neoheadergetversion)
            - [Neo.Header.GetMerkleRoot](#neoheadergetmerkleroot)
            - [Neo.Header.GetNextConsensus](#neoheadergetnextconsensus)
            - [Neo.Transaction.GetScript](#neotransactiongetscript)
            - [Neo.Transaction.GetWitnesses](#neotransactiongetwitnesses)
            - [Neo.Witness.GetVerificationScript](#neowitnessgetverificationscript)
            - [Neo.Account.IsStandard](#neoaccountisstandard)
            - [Neo.Contract.Create](#neocontractcreate)
            - [Neo.Contract.Update](#neocontractupdate)
            - [Neo.Contract.GetScript](#neocontractgetscript)
            - [Neo.Contract.IsPayable](#neocontractispayable)
            - [Neo.Storage.Find](#neostoragefind)
            - [Neo.Enumerator.Create](#neoenumeratorcreate)
            - [Neo.Enumerator.Next](#neoenumeratornext)
            - [Neo.Enumerator.Value](#neoenumeratorvalue)
            - [Neo.Enumerator.Concat](#neoenumeratorconcat)
            - [Neo.Iterator.Create](#neoiteratorcreate)
            - [Neo.Iterator.Key](#neoiteratorkey)
            - [Neo.Iterator.Keys](#neoiteratorkeys)
            - [Neo.Iterator.Values](#neoiteratorvalues)
            - [Neo.Iterator.Concat](#neoiteratorconcat)
            - [Neo.Json.Serialize](#neojsonserialize)
            - [Neo.Json.Deserialize](#neojsondeserialize)
    - [Fees](#fees)
    - [Accessing to Internet Resources (TO BE ADDED)](#accessing-to-internet-resources-to-be-added)
    - [Contract Invocation](#contract-invocation)
    - [Contract Upgrade](#contract-upgrade)
    - [Contract Destroying](#contract-destroying)

<!-- /TOC -->

# Smart Contracts
## Changes in NEO3

All transactions in NEO3 are the invocation of the smart contract. In addition to some interop services and OpCode adjustments, NEO3 also features the following changes.
- Add the [Manifest](#manifest) file to describe the characteristics of the contract
- Add [Native Contract](#native-contract)
- Reduce the [system fee](#fees) for OpCode and interop services
- Provide the contract with the support for [accessing to network resources](#accessing-to-internet-resources-to-be-added)
- Add new trigger type of `System` 

## Manifest
Now each contract is required to provide a manifest file to describe its properties, including Groups, Features, ABI, Permissions, Trusts, SafeMethods, as shown below:

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

- **Groups**：Declare the groups to which the contract belongs. Each group is identified by a public key and a signature.
- **Features**：Declare the features of the smart contract. Where the attribute value `storage` indicates that the contract can access the storage area, and the `payable` indicates the contract can accept the transfer of assets.
- **ABI**：Declare the interface information about the smart contract, you can refer to [NEP-3](https://github.com/neo-project/proposals/blob/master/nep-3.mediawiki) for the details. The basic properties of the interface include:
  - Hash: the script hash of the contract. It is encoded as a hexadecimal string in big-endian;
  - EntryPoint: a Method object which describes the details of the entry point of the contract, including name, parameters, and the value returned;
  - Methods: an array of Method objects which describe the details of each method in the contract;
  - Events: an array of Event objects which describe the details of each event in the contract. 

  Based on the ABI information, mutual invocation between contracts can be realized.
- **Permissions**：Declare which contracts may be invoked and which methods are called. If a contract invokes a contract or method that is not declared in the manifest at runtime, the invocation will fail.
- **Trusts**：Declare which contracts or groups can call the contract safely.
- **SafeMethods**：Declare an array containing a set of method names. SafeMethods usually won't modify the storage area, only involved in reading the blockchain data. If a method is marked as safe, the user interface will not give any warnings when it is called by any other contract.

## Trigger
Triggers allow contracts to execute different logic depending on the usage scenario.

* **System** This type of trigger is newly added in NEO3. It is triggered when the node receives a new block and currently only triggers the execution of the native contract. After receiving a new block, the node will be triggered by the System Trigger to invoke the onPersist method in all native contracts before the persistence.
* **Application** An application trigger is used to invoke a contract as an application function, which can accept multiple parameters, change blockchain status, and return values of any type. Here is a simple c# smart contract：

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

All transactions in NEO3 are the invocation of the smart contract. When a transaction is broadcast and confirmed, the smart contract is executed by the consensus node, and the normal node does not execute the smart contract when forwarding the transaction. Successful execution of a smart contract does not represent the success of the transaction, and the success of the transaction does not determine the success of the smart contract execution.

* **Verification** The purpose of the validation trigger is to call the contract as a verification function that accepts multiple arguments and should return a Boolean value indicating the validity of the transaction or block.

When you transfer tokens from account A to account B, the verification contract will be triggered. All the nodes that receive the transaction (including the normal node and the consensus node) will verify the contract of the account A. If the return value is true, the transfer is successful, and fails otherwise.

If the authentication contract fails to execute, the transaction will not be included in the blockchain.

The following code is a simple example of a verification contract. It returns true if condition A is satisfied, indicating the success of the transfer. Otherwise, it returns false and the transfer fails.

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

The following code works the same as above, but with an additional check for the trigger in the runtime. The code of the verification part is executed only when the trigger is a verification trigger, which is of great use in complex smart contracts. If a smart contract implements multiple triggers, the type of triggers should be judged in the `Main` method.
  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.Neo;

  public static bool Main(byte[] signature)
  {
      if (Runtime.Trigger == TriggerType.Verification)
      {
          if (/* condition A*/)
                  return true;
              else
                  return false;
      }  
  }
  ```

## Native Contract
### Introduction
Native contracts are executed directly in native code, rather than in a virtual machine. The native contract exposes its service name to other contracts for invocation. Currently, available NativeContract includes NeoToken, GasToken, and PolicyToken.

#### NeoToken

Referred to as NEO, it acts as the governance token which is used to enforce the management of the Neo network and conforms to the NEP-5 standard. It has a hard cap total of 100 million tokens, with the smallest unit of 1, and is not divisible. NEO was pre-mined during the genesis block creation. The specific interface details are as follows：

- **unClaimGas**：Get the amount of GAS unclaimed at the specified height

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

<table class="mytable">
<tr >
<th rowspan="3">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>account</td>
<td >Hash60</td>
<td>ScriptHash of the account</td>
</tr>
<tr >
<td >end</td>
<td >Integer</td>
<td >The height to be queried</td>
</tr>
<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Integer</td>
<td colspan="2" >GAS unclaimed </td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.03</td>
</tr>
</table>

- **RegisterValidator**：Register to become a candidate for the validator

```csharp
[ContractMethod(0_05000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { 
  ContractParameterType.PublicKey 
}, 
ParameterNames = new[] { "pubkey" })]
private StackItem RegisterValidator(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>pubKey</td>
<td >PublicKey</td>
<td>Public key of the account</td>
</tr>
<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result. true: sucess, false: failure</td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.05</td>
</tr>
</table>

- **getRegisteredValidators**：Gets the information of currently registered validators and backup nodes 

```csharp
[ContractMethod(1_00000000, 
  ContractParameterType.Array,
  SafeMethod = true)]
private StackItem GetRegisteredValidators(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
  <th >Parameters</th>
  <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Array</td>
<td colspan="2" >Public keys of all validators and backup nodes </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >1.00</td>
</tr>
</table>

- **getValidators**: Get all current validators

```csharp
[ContractMethod(1_00000000, 
  ContractParameterType.Array, 
  SafeMethod = true)]
private StackItem GetValidators(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Array</td>
<td colspan="2" >Public keys of all current validators </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >1.00</td>
</tr>
</table>

- **getNextBlockValidators**: Get validators of the next block

```csharp
[ContractMethod(1_00000000, 
  ContractParameterType.Array, 
  SafeMethod = true)]
private StackItem GetNextBlockValidators(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Array</td>
<td colspan="2" >Public keys of validators in the next round of consensus </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >1.00</td>
</tr>
</table>


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

<table class="mytable">
<tr >
<th rowspan="3">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>account</td>
<td >Hash60</td>
<td>Voter's ScriptHash</td>
</tr>
<tr >
<td >pubkeys</td>
<td >Array</td>
<td >The public keys of validators one vote to</td>
</tr>
<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result. true: success, false: failure</td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >5.00</td>
</tr>
</table>

- **name***： Name of the token

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "name", 
  SafeMethod = true)]
protected StackItem NameMethod(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >String</td>
<td colspan="2" >Name of the token  </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.00</td>
</tr>
</table>

- **symbol***：Symbol of the token

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "symbol", 
  SafeMethod = true)]
protected StackItem SymbolMethod(ApplicationEngine engine, VMArray args)
```


<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >String</td>
<td colspan="2" >Symbol of the token  </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.00</td>
</tr>
</table>

- **decimals***: Decimals of the token

```csharp
[ContractMethod(0, 
  ContractParameterType.Integer, 
  Name = "decimals", 
  SafeMethod = true)]
protected StackItem DecimalsMethod(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >uint</td>
<td colspan="2" >Decimal  </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.00</td>
</tr>
</table>

- **totalSupply***: Total supply

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
protected StackItem TotalSupply(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >BigInteger</td>
<td colspan="2" >Total supply  </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.01</td>
</tr>
</table>


- **balanceOf***: Balance of an account

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" }, 
  SafeMethod = true)]
protected StackItem BalanceOf(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>account</td>
<td >Hash60</td>
<td>ScriptHash of the account</td>
<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Integer</td>
<td colspan="2" >Balance </td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.01</td>
</tr>
</table>

- **transfer***: Transfer token from one to another

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

<table class="mytable">
<tr >
<th rowspan="4">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>from</td>
<td >Hash60</td>
<td>ScriptHash who want to transfer</td>
</tr >
<tr >
<td>to</td>
<td >Hash60</td>
<td>ScriptHash of the receiver</td>
</tr >
<tr >
<td>amount</td>
<td >Integer</td>
<td>Amount of the token </td>
</tr >
<tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result, true: success，false: failure </td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.08</td>
</tr>
</table>

> Method marked * is [NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki) standard interface.

#### GasToken

Referred to as GAS, Neo's fuel token, used to pay the handling fee, in line with NEP-5 standards. Transaction costs and consensus node incentives on the Neo network are paid in GAS. The total amount of GAS is 100 million, and the minimum unit is 0.00000001. GAS is registered in the founding block but not distributed.
GAS distribution mechanism: When a new block is generated, a new GAS is generated, and the generated GAS and system fees are distributed to NEO holders. The calculation and distribution of GAS are automatically performed when the NEO balance in the user's address changes. The calculation method is as follows:  

![GAS distribution](../../images/GAS_distribution.png) 

Where `m` is the height of the block where the user last extracted the GAS, `n` is the current block height, and `NEO` is the number of NEOs held by the user during `m` to `n`. `BlockBonus` represents the amount of GAS that each block can extract. `SystemFee`  represents the sum of the system fees for all transactions in this block.

GasToken methods details following：

- **getSysFeeAmount**: Total system fee till a specific height

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { ContractParameterType.Integer }, 
  ParameterNames = new[] { "index" }, 
  SafeMethod = true)]
private StackItem GetSysFeeAmount(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>index</td>
<td >Integer</td>
<td>The height one want to query</td>
</tr>
<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Integer</td>
<td colspan="2" >Total system fee </td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.01</td>
</tr>
</table>

- **name***: Name of the token

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "name", 
  SafeMethod = true)]
protected StackItem NameMethod(ApplicationEngine engine, VMArray args)
```


<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >String</td>
<td colspan="2" >Name of the token </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.00</td>
</tr>
</table>

- **symbol**: Symbol of the token

```csharp
[ContractMethod(0, 
  ContractParameterType.String, 
  Name = "symbol", 
  SafeMethod = true)]
protected StackItem SymbolMethod(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >String</td>
<td colspan="2" >Symbol of the token </td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.00</td>
</tr>
</table>

- **decimals***: Decimals of the token

```csharp
[ContractMethod(0, 
  ContractParameterType.Integer, 
  Name = "decimals", 
  SafeMethod = true)]
protected StackItem DecimalsMethod(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >uint</td>
<td colspan="2" >Decimal</td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.00</td>
</tr>
</table>


- **totalSupply***: Total supply

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
protected StackItem TotalSupply(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >BigInteger</td>
<td colspan="2" >Total supply</td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.01</td>
</tr>
</table>

- **balanceOf***: Balance of a specific account

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" }, 
  SafeMethod = true)]
protected StackItem BalanceOf(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>account</td>
<td >Hash60</td>
<td>ScriptHash of the account to be queried </td>
</tr>
<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Integer</td>
<td colspan="2" >Balance </td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.01</td>
</tr>
</table>

- **transfer***: Transfer token from one account to another

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

<table class="mytable">
<tr >
<th rowspan="4">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>from</td>
<td >Hash60</td>
<td>ScriptHash of the sender's account</td>
</tr >
<tr >
<td>to</td>
<td >Hash60</td>
<td>ScriptHash of the receiver's account</td>
</tr >
<tr >
<td>amount</td>
<td >Integer</td>
<td> Transferred amount</td>
<tr >

<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result, true: success，false: failure </td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.08</td>
</tr>
</table>


> Method marked * is [NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki) standard interface

#### PolicyToken

It is the contract for configuring the consensus strategy and saves relevant parameters in the consensus process, including maximum transactions per block, maximum low-priority transactions per block, maximum low-priority-transaction size, the fee per byte, etc. The interface is described in detail below：

- getMaxTransactionPerBlock: Get maximum transactions per block.

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
private StackItem GetMaxTransactionsPerBlock(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Integer</td>
<td colspan="2" >Maximum transaction count per block</td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.01</td>
</tr>
</table>

- **GetFeePerByte**： Get system fee per byte

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Integer, 
  SafeMethod = true)]
private StackItem GetFeePerByte(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Integer</td>
<td colspan="2" >Fee per byte</td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.01</td>
</tr>
</table>

- **getBlockedAccounts**: Get the addresses in the blacklist

```csharp
[ContractMethod(0_01000000, 
  ContractParameterType.Array, 
  SafeMethod = true)]
private StackItem GetBlockedAccounts(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
    <th >Parameters</th>
    <th colspan="2" >none</th>
</tr>
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Array</td>
<td colspan="2" >Address blacklist</td>
</tr>
<tr >
    <th >Fee(GAS)</th>
    <td colspan="2" >0.01</td>
</tr>
</table>


- **setMaxTransactionsPerBlock**: Set maximum transactions count per block

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Integer }, 
  ParameterNames = new[] { "value" })]
private StackItem SetMaxTransactionsPerBlock(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>value</td>
<td >Integer</td>
<td>Maximum transactions count to be set</td>
</tr>

<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result. true: success, false: failure</td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.03</td>
</tr>
</table>

- **setFeePerByte**：Set system fee per byte.

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Integer }, 
  ParameterNames = new[] { "value" })]
private StackItem SetFeePerByte(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>value</td>
<td >Integer</td>
<td> System fee per byte </td>
</tr>

<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result. true: success, false: failure</td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.03</td>
</tr>
</table>

*0.03*

- **blockAccount**：Add an account into the blacklist.

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" })]
private StackItem BlockAccount(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>account</td>
<td >Hash160</td>
<td> ScriptHash of the account to be added</td>
</tr>

<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result. true: success, false: failure</td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.03</td>
</tr>
</table>


- **unblockAccount**：Remove an account from the blacklist.

```csharp
[ContractMethod(0_03000000, 
  ContractParameterType.Boolean, 
  ParameterTypes = new[] { ContractParameterType.Hash160 }, 
  ParameterNames = new[] { "account" })]
private StackItem UnblockAccount(ApplicationEngine engine, VMArray args)
```

<table class="mytable">
<tr >
<th rowspan="2">Parameters</th>
<th >Parameter</th>
<th >Type</th>
<th  >Description</th>
</tr>
<tr >
<td>account</td>
<td >Hash160</td>
<td> ScriptHash of the account to be removed </td>
</tr>

<tr >
<th  rowspan="2">Return</th>
<th>Type</th>
<th colspan="2">Description</th>
</tr>
<tr >
<td  >Boolean</td>
<td colspan="2" >Result. true: success, false: failure</td>
</tr>
<tr >
<th >Fee(GAS)</th>
<td colspan="3" >0.03</td>
</tr>
</table>

**For more NativeContract, Please stay stunned**

### NativeContract Deployment
NativeContract is deployed in the Genesis Block by calling the interop interface of Neo.Native.Deploy, and can only be executed in the Genesis Block.
### NativeContract Invocation
There are two ways to invoke a native contract. The first one is to invoke the contract with its ScriptHash, just like the normal contract, and the other is specific to the native contract-specific, that is, direct invocation through the interop service. You can refer to [Usage of Interop Service](#Usage-of-Interop-Service)

- **Specific Way**：Using the interop service

Each NativeContract registers an interop interface with the name of its ServiceName, which is under the Neo.Native namespace.
The ServiceName corresponding to each NativeContract is as follows：

|NativeContract|ServiceName|
|---|---|
|NeoToken|Neo.Native.Tokens.NEO|
|GasToken|Neo.Native.Tokens.GAS|
|PolicyToken|NeoNeo.Native.Policy|

If you want to implement a function for transferring GAS, a smart contract written in C# can be as follows:

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

- **General Way**：Using ScriptHash and System.Contract.Call

Since the ScriptHash of the native contract is fixed, it can be invoked with the interop interface of the System.Contract.Call and the ScriptHash, just like any other normal contract. The existing script hash of the native contract is as follows：

|NativeContract|ScriptHash|
|---|---|
|NeoToken| 0x43cf98eddbe047e198a3e5d57006311442a0ca15 |
|GasToken|0xa1760976db5fcdfab2a9930e8f6ce875b2d18225|
|PolicyToken|0x9c5699b260bd468e2160dd5d45dfd2686bba8b77|

For details please refer to [Contract Invokation](#Contract-Invocation)

## Interop Service
The interop service layer provides APIs for smart contracts to access the blockchain data, including the block information, transaction information, contract information, asset information, etc. Besides, the interop service layer provides the persistence capability for each contract. Each Neo smart contract is allowed to optionally enable a private storage area with the form of key-value pair. The context of persisting the storage for Neo smart contracts is determined by the callee of the contract, not the caller. The caller needs to pass its storage context to the callee (that is, authorization) before the callee can perform the read-write operation. There are two types of interop service, namely System and Neo.

### Principle of Interop Service 
When the Neo program starts, it registers a series of interop interfaces to the virtual machine for smart contract invocation.
* **Service Name** Each interoperation has a service name, such as `System.Contract.Call`。
* **Mapping Method** Each interop service corresponds to a  native method. Also taking the `System.Contract.Call` as an example, `private static bool Contract_Call(ApplicationEngine engine)` is the method invoked in the client.
* **System Fee** Each interop interface has its method for system fee calculation or fixed system fee.
* **Triggering** Each interop interface has a supported trigger mode. For instance, `TriggerType.All` supports all triggers.

When registering, the Neo client encapsulates the service name of the interop interface, the mapping method, the method of calculating system fee, and the supported trigger mode as an InteropDescriptor and stores it in the Dictionary with the index of the hash value of the interop interface name, which is calculated as follows:
`BitConverter.ToUInt32(Encoding.ASCII.GetBytes(ServiceName).Sha256(), 0)`

For example the hash of `System.Contract.Call` is `0x627d5b52`

### Usage of Interop Service

* **Smart Contract** The interop interface used in the smart contract is provided by the corresponding smart contract development framework, which can be invoked directly. When compiled, the interop interface is compiled by the compiler into the operating instructions that can be executed in NeoVM.
* **Script of the Transaction** The execution script can be constructed with the hash value of the interop service name and the SYSCALL operator.

Here is an example for calling the `name` method of the contract `0x43cf98eddbe047e198a3e5d57006311442a0ca15` via `System.Contract.Call`：

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

In C#, it can be called as follows：

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

Interop services are divided into System part and Neo part. The specific interfaces are as follows:

### System Part

#### System.ExecutionEngine.GetScriptContainer 

| Description | Get the script container of the smart contract |
|--|--|
| C# Function | byte[] GetScriptContainer() |

#### System.ExecutionEngine.GetExecutingScriptHash

| Description | Get the script hash of the contract being executed |
|--|--|
| C# Function| byte[] GetExecutingScriptHash() |

#### System.ExecutionEngine.GetCallingScriptHash

| Description | Get the script hash of the contract caller |
|--|--|
| C# Function| byte[] GetExecutingScriptHash() |

#### System.ExecutionEngine.GetEntryScriptHash  

| Description | Get the script hash of the entry point of the smart contract <br/>(the starting point of the contract invocation chain) |
|--|-- |
| C# Function| byte[] GetEntryScriptHash() |

#### System.Runtime.Platform

| Description | Get the platform information of the contract being executed |
|--|--|
| C# Function| string Platform() |

#### System.Runtime.GetTrigger

| Description | Get the triggering condition of the contract |
|--|--|
| C# Function | TriggerType Trigger() |

#### System.Runtime.CheckWitness

| Description |  Verify whether the container calling the contract is signed by the<br/>script hash of the specific account |
|--|--|
| C# Function | bool CheckWitness(byte[] hashOrPubKey) |

#### System.Runtime.Notify

| Description | Notify the client executing the contract |
|--|--|
| C# Function | bool Notify(params object[] state) |

#### System.Runtime.Log

| Description | Record the log |
|--|--|
| C# Function | void Log(string message) |

#### System.Runtime.GetTime

| Description | Get the timestamp of the current block |
|--|--|
| C# Function | uint Time |

#### System.Runtime.Serialize

| Description | Serialize an object |
|--|--|
| C# Function | byte[] Serialize(this object source) |

#### System.Runtime.Deserialize

| Description | Deserialize a byte array into object |
|--|--|
| C# Function |  object Deserialize(this byte[] source)|

#### System.Runtime.GetInvocationCounter

| Description | Get invocation count of the current contract |
|--|--|
| C# Function | int GetInvocationCounter() |

#### System.Crypto.Verify

| Description | Verify the signature of the message with the public key |
|--|--|
| C# Function | bool Verify(object message, byte[] signature, byte[] pubKey) |

#### System.Blockchain.GetHeight

| Description | Get the current block height |
|--|--|
| C# Function | uint GetHeight() |

#### System.Blockchain.GetHeader

| Description | Get the current block header|
|--|--|
| C# Function | Header GetHeader(uint height) |
|| Header GetHeader(byte[] hash)  |

#### System.Blockchain.GetBlock

| Description | Get a block by block height or block hash |
|--|--|
| C# Function | Block GetBlock(uint height) |
|| Block GetBlock(byte[] hash)  |

#### System.Blockchain.GetTransaction

| Description | Get transaction by transaction ID |
|--|--|
| C# Function | Transaction GetTransaction(byte[] hash) |

#### System.Blockchain.GetTransactionHeight

| Description | Get the index of the block including the transaction by transaction ID |
|--|--|
| C# Function | int GetTransactionHeight(byte[] hash) |

#### System.Blockchain.GetContract

| Description | Get contract by scripthash |
|--|--|
| C# Function | Contract GetContract(byte[] scriptHash) |

#### System.Header.GetIndex

| Description | Get block index from block header |
|--|--|
| C# Function | uint Index |

#### System.Header.GetHash

| Description | Get block hash from block header |
|--|--|
| C# Function | byte[] Hash |

#### System.Header.GetPrevHash

| Description | Get hash of the previous block from block header |
|--|--|
| C# Function | byte[] PreHash |

#### System.Header.GetTimestamp

| Description | Get timestamp from block header |
|--|--|
| C# Function | byte[] Timestamp |

#### System.Block.GetTransactionCount

| Description | Get the number of transactions included in the block |
|--|--|
| C# Function | int GetTransactionCount |

#### System.Block.GetTransactions

| Description | Get all transactions included in the block |
|--|--|
| C# Function | Transaction[] GetTransactions() |

#### System.Block.GetTransaction

| Description | Get a transaction in the block by transaction index |
|--|--|
| C# Function | Transaction[] GetTransaction(int index) |

#### System.Transaction.GetHash

| Description | Get hash of the transaction |
|--|--|
| C# Function | byte[] Hash |

#### System.Contract.Call 

| Description | Invoke a contract |
|--|--|
| C# Function | void Call(byte[] scriptHash, string method, object[] args) |
|  | void Call(Contract contract, string method, object[] args) |

#### System.Contract.Destroy

| Description | Destroy current contract |
|--|--|
| C# Function | void Destroy() |

#### System.Storage.GetContext

| Description | Get storage context of the current contract |
|--|--|
| C# Function | StorageContext GetContext() |
| Explanation | the IsReadOnly flag of StorageContext is false |

#### System.Storage.GetReadOnlyContext

| Description | Get storage context of the current contract in read-only mode |
|--|--|
| C# Function | StorageContext GetContext() |
| Explanation | the IsReadOnly flag of StorageContext is true |

#### System.Storage.Get

| Description | Get value from the storage by key |
|--|--|
| C# Function | byte[] Get(StorageContext context, byte[] key) |

#### System.Storage.Put

| Description | Put key-value into storage based on the storage context |
|--|--|
| C# Function | byte[] Get(StorageContext context, byte[] key, byte[] value) |

#### System.Storage.PutEx

| Description | Put Key-Value into the storage based on the storage context and the flag |
|--|--|
| C# Function | byte[] Get(StorageContext context, byte[] key, byte[] value, StorageFlags flags) |
| Explanation | The parameter StorageFlags defines the attribute of the written data. The default value <br/>is None, indicating that data can be read and written. If it is Constant, the data cannot be <br/>modified or deleted once it is written to the storage area.|

#### System.Storage.Delete

| Description | Delete the stored Key-Value data from the storage area by the Key|
|--|--|
| C# Function | void Delete(StorageContext context, byte[] key) |
| Explanation | Data cannot be deleted if its StorageFlags is Constant|

#### System.StorageContext.AsReadOnly

| Description | Set the current context to read-only mode |
|--|--|
| C# Function | void AsReadOnly(this StorageContext context) |
| Explanation | Set the IsReadOnly flag of the StorageContext to true |

### Neo Part

#### Neo.Native.Deploy

| Description | Deploy and initialize all native contracts |
|--|--|
| Explanation | It can only be invoked in the genesis block |

#### Neo.Crypto.CheckSig

| Description | Verify signature of the current script container by public key |
|--|--|
| C# Function | bool CheckSig(byte[] signature, byte[] pubKey) |

#### Neo.Crypto.CheckMultiSig

| Description | Verify the multiple signature of the current script container by public key |
|--|--|
| C# Function | bool CheckMultiSig(byte[][] signatures, byte[][] pubKeys) |

#### Neo.Header.GetVersion

| Description | Get version from block header |
|--|--|
| C# Function | uint Version |

#### Neo.Header.GetMerkleRoot

| Description | Get MerkleRoot built with all included transactions from block header |
|--|--|
| C# Function | byte[] MerkleRoot |

#### Neo.Header.GetNextConsensus

| Description | Get the script hash of the next-round validators' multi-signature contract |
|--|--|
| C# Function | byte[] NextConsensus |

#### Neo.Transaction.GetScript

| Description | Get the script in the transaction |
|--|--|
| C# Function | byte[] Script |

#### Neo.Transaction.GetWitnesses

| Description | Get witnesses in the transaction |
|--|--|
| C# Function | Witness[] GetWitnesses(this Transaction transaction) |

#### Neo.Witness.GetVerificationScript

| Description | Get verification script in the transaction |
|--|--|
| C# Function | byte[] VerificationScript |

#### Neo.Account.IsStandard

| Description | Check if the account is standard |
|--|--|
| C# Function | bool IsStandard(byte[] scriptHash) |

#### Neo.Contract.Create

| Description | Create a contract |
|--|--|
| C# Function | Contract Create(byte[] script, string manifest) |
| Explanation | The size of the script contract cannot exceed 1MB and the size of the manifest cannot exceed 2KB |

#### Neo.Contract.Update

| Description | Update a contract |
|--|--|
| C# Function | Contract Create(byte[] script, string manifest) |
| Explanation | The size of the script contract cannot exceed 1MB and it cannot be deployed before. The size of the <br/> manifest cannot exceed 2KB. The old contract will be destroyed after the upgrade. |

#### Neo.Contract.GetScript

| Description | Get script of the contract |
|--|--|
| C# Function | byte[] Script |

#### Neo.Contract.IsPayable

| Description | Check if the contract is able to receive assets |
|--|--|
| C# Function | bool IsPayable(this Contract contract) |

#### Neo.Storage.Find

|Description | Find the data in the storage area of the current storage context with the specified prefix | 
|--|--|
| C# Function | Iterator < byte[], byte[] > Find(StorageContext context, byte[] prefix) |

#### Neo.Enumerator.Create

| Description | Create an enumerator |
|--|--|
| C# Function | Enumerator Create(object[] array) |

#### Neo.Enumerator.Next

| Description | Check if the enumerator has more element|
|--|--|
| C# Function | bool Next(this Enumerator enumerator) |

#### Neo.Enumerator.Value

| Description | Get the current value of the enumerator |
|--|--|
| C# Function | object Value(this Enumerator enumerator) |

#### Neo.Enumerator.Concat

| Description | Concat two enumerators |
|--|--|
| C# Function | Enumerator Concat(Enumerator enumerator1, Enumerator enumerator2) |

#### Neo.Iterator.Create

| Description | Create an iterator |
|--|--|
| C# Function | Iterator Create(object[] array) |
| | Iterator Create(Dictionary<object, object> map) |

#### Neo.Iterator.Key

| Description | Get the current key of the iterator |
|--|--|
| C# Function | object Key(this Iterator it) |

#### Neo.Iterator.Keys

| Description | Get all keys of the iterator |
|--|--|
| C# Function | Iterator Keys(this Iterator it) |

#### Neo.Iterator.Values

| Description | Get all values of the iterator |
|--|--|
| C# Function | Iterator Values(this Iterator it) |

#### Neo.Iterator.Concat

| Description | Concat two iterators |
|--|--|
| C# Function | Iterator Concat(Iterator iterator1, Iterator iterator2) |

#### Neo.Json.Serialize

| Description | Serialize a string to Json object |
|--|--|
| C# Function | JObject Serialize(string jsonStr) |

#### Neo.Json.Deserialize

| Description | Deserialize a json object to string |
|--|--|
| C# Function | string Deserialize(JObject jsonObj) |

## Fees

| Interop Service | Fee (GAS) |
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


## Accessing to Internet Resources (TO BE ADDED) 
## Contract Invocation
When writing a contract, you can invoke other contracts through the interop service provided by the development framework[System.Contract.Call](#SystemContractCall). Here is an example in C#:

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

In the case of the execution script constructed manually, the contract should be invoked with the interop service [System.Contract.Call](#SystemContractCall) and the ScriptHash of the contract. Please refer to [How to use interop services](#Usage-of-Interop-Service)

Here is an example for invoking method `transfer` of the contract `0x43cf98eddbe047e198a3e5d57006311442a0ca15` via `System.Contract.Call`：

```
PUSHBYTES4  0x00e1f505
PUSHBYTES20 0xfb5fd311a3ae2b2c8ab6b63c10502f9cf58ebeed
PUSHBYTES20 0xa6b9b510a67009e61f4f95115c188437f76dd2d0
PUSH3
PACK                                                      //The third parameter, parameter list
PUSHBYTES4  0x6e616d65                                    //The second parameter, method name
PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15    //The first parameter, contract's ScriptHash
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

## Contract Upgrade
You can upgrade the contract to modify the logic of the contract and keep the data in the storage area after the contract is deployed, but you need to implement the upgrade interface in the old contract through invoking the [Neo.Contract.Update](#NeoContractUpdate) method.

When the upgrade interface is called in the old contract, the method will build a new smart contract based on the parameters passed in. If the old contract has a storage area, the storage area will be transferred to the new contract. After the upgrade, the old contract will be deleted, as well as its storage area (if any). The old contract will then be unavailable and instead, the hash of the new contract should be used.

C# code:

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

## Contract Destroying
The smart contract supports the destruction operation after the release, but the interface for destroying the contract needs to be reserved in the old contract, mainly involved in the invocation of `System.Contract.Destroy` method.

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
