<!-- TOC -->

- [Smart Contracts](#smart-contracts)
    - [Changes in NEO3](#changes-in-neo3)
    - [Manifest](#manifest)
    - [Trigger](#trigger)
    - [Native Contract](#native-contract)
        - [Introduction](#introduction)
            - [NeoToken](#neotoken)
            - [GasToken](#gastoken)
            - [PolicyContract](#policycontract)
        - [NativeContract Deployment](#nativecontract-deployment)
    - [Interop Service](#interop-service)
        - [Principle of Interop Service](#principle-of-interop-service)
        - [Usage of Interop Service](#usage-of-interop-service)
        - [System Part](#system-part)
            - [System.Binary.Serialize](#systembinaryserialize)
            - [System.Binary.Deserialize](#systembinarydeserialize)
            - [System.Blockchain.GetHeight](#systemblockchaingetheight)
            - [System.Blockchain.GetBlock](#systemblockchaingetblock)
            - [System.Blockchain.GetTransaction](#systemblockchaingettransaction)
            - [System.Blockchain.GetTransactionHeight](#systemblockchaingettransactionheight)
            - [System.Blockchain.GetTransactionFromBlock](#systemblockchaingettransactionfromblock)
            - [System.Blockchain.GetContract](#systemblockchaingetcontract)
            - [System.Contract.Create](#systemcontractcreate)
            - [System.Contract.Update](#systemcontractupdate)
            - [System.Contract.Destroy](#systemcontractdestroy)
            - [System.Contract.Call](#systemcontractcall)
            - [System.Contract.CallEx](#systemcontractcallex)
            - [System.Contract.IsStandard](#systemcontractisstandard)
            - [System.Enumerator.Create](#systemenumeratorcreate)
            - [System.Enumerator.Next](#systemenumeratornext)
            - [System.Enumerator.Value](#systemenumeratorvalue)
            - [System.Enumerator.Concat](#systemenumeratorconcat)
            - [System.Iterator.Create](#systemiteratorcreate)
            - [System.Iterator.Key](#systemiteratorkey)
            - [System.Iterator.Keys](#systemiteratorkeys)
            - [System.Iterator.Values](#systemiteratorvalues)
            - [System.Iterator.Concat](#systemiteratorconcat)
            - [System.Json.Serialize](#systemjsonserialize)
            - [System.Json.Deserialize](#systemjsondeserialize)
            - [System.Runtime.Platform](#systemruntimeplatform)
            - [System.Runtime.GetTrigger](#systemruntimegettrigger)
            - [System.Runtime.GetTime](#systemruntimegettime)
            - [System.Runtime.GetScriptContainer](#systemruntimegetscriptcontainer)
            - [System.Runtime.GetExecutingScriptHash](#systemruntimegetexecutingscripthash)
            - [System.Runtime.GetCallingScriptHash](#systemruntimegetcallingscripthash)
            - [System.Runtime.GetEntryScriptHash](#systemruntimegetentryscripthash)
            - [System.Runtime.CheckWitness](#systemruntimecheckwitness)
            - [System.Runtime.GetInvocationCounter](#systemruntimegetinvocationcounter)
            - [System.Runtime.Log](#systemruntimelog)
            - [System.Runtime.Notify](#systemruntimenotify)
            - [System.Runtime.GetNotifications](#systemruntimegetnotifications)
            - [System.Storage.GetContext](#systemstoragegetcontext)
            - [System.Storage.GetReadOnlyContext](#systemstoragegetreadonlycontext)
            - [System.Storage.Get](#systemstorageget)
            - [System.Storage.Find](#systemstoragefind)
            - [System.Storage.Put](#systemstorageput)
            - [System.Storage.PutEx](#systemstorageputex)
            - [System.Storage.Delete](#systemstoragedelete)
            - [System.StorageContext.AsReadOnly](#systemstoragecontextasreadonly)
        - [Neo Part](#neo-part)
            - [Neo.Native.Deploy](#neonativedeploy)
            - [Neo.Crypto.ECDsaVerify](#neocryptoecdsaverify)
            - [Neo.Crypto.ECDsaCheckMultiSig](#neocryptoecdsacheckmultisig)
    - [Accessing to Internet Resources](#accessing-to-internet-resources)
    - [Contract Invocation](#contract-invocation)
        - [Invoke another contract in contract](#invoke-another-contract-in-contract)
        - [Invoke contract in script](#invoke-contract-in-script)
    - [Contract Upgrade](#contract-upgrade)
    - [Contract Destroying](#contract-destroying)

<!-- /TOC -->

# Smart Contracts
## Changes in NEO3

All transactions in NEO3 are the invocation of the smart contract. In addition to some interop services and OpCode adjustments, NEO3 also features the following changes.

- ADD 
    - [Manifest](#manifest): used to describe the features of the contract and deployed with NEF files
    - [native contracts](#native-contract): running in the native code rather than in the virtual machine, including NeoToken, GasToken and PolicyToken
    - [Accessing to network resources](#accessing-to-internet-resources): to be added
    - [System Trigger](#trigger): triggered when the node receives a new block and currently only triggers the execution of the native contract
    - Interop Service: `System.Binary.Serialize`, `System.Binary.Deserialize`, `System.Contract.Create`, `System.Contract.Update`, `System.Contract.Call`, `System.Contract.CallEx`, `System.Contract.IsStandard`, 
    `System.Enumerator.Create`, `System.Enumerator.Next`, `System.Enumerator.Value`, `System.Enumerator.Concat`, `System.Iterator.Create`, `System.Iterator.Key`, `System.Iterator.Keys`, `System.Iterator.Values`, `System.Iterator.Concat`, `System.Json.Serialize`, `System.Json.Deserialize`, `System.Runtime.GetScriptContainer`, `System.Runtime.GetScriptContainer`,`System.Runtime.GetExecutingScriptHash`, `System.Runtime.GetCallingScriptHash`, `System.Runtime.GetEntryScriptHash`, `System.Runtime.GetInvocationCounter`, `System.Runtime.GetNotifications`, `System.Storage.Find`, `Neo.Native.Deploy`, `Neo.Crypto.ECDsaVerify`, `Neo.Crypto.ECDsaCheckMultiSig`.
    
- UPDATE
    - Reduce the [system fee](#fees) for OpCode and interop services

- DELETE
    - Interop Service:`System.ExecutionEngine.GetScriptContainer`, `System.ExecutionEngine.GetExecutingScriptHash`, `System.ExecutionEngine.GetCallingScriptHash`, `System.ExecutionEngine.GetEntryScriptHash`, `System.Runtime.Serialize`, `System.Runtime.Deserialize`, `System.Header.GetIndex`, `System.Header.GetHash`, `System.Header.GetPrevHash`, `System.Header.GetTimestamp`, `System.Block.GetTransactionCount`, `System.Block.GetTransactions`, `System.Block.GetTransaction`, `System.Transaction.GetHash`, `System.Contract.GetStorageContext`, 
    `Neo.Runtime.GetTrigger`, `Neo.Runtime.CheckWitness`, `Neo.Runtime.Notify`, `Neo.Runtime.Log`, `Neo.Runtime.GetTime`, `Neo.Runtime.Serialize`, `Neo.Runtime.Deserialize`, `Neo.Blockchain.GetHeight`, `Neo.Blockchain.GetHeader`, `Neo.Blockchain.GetBlock`, `Neo.Blockchain.GetTransaction`, `Neo.Blockchain.GetTransactionHeight`, `Neo.Blockchain.GetAccount`, `Neo.Blockchain.GetValidators`, `Neo.Blockchain.GetAsset`, `Neo.Blockchain.GetContract`, `Neo.Header.GetHash`, `Neo.Header.GetVersion`, `Neo.Header.GetPrevHash`, `Neo.Header.GetMerkleRoot`, `Neo.Header.GetTimestamp`, `Neo.Header.GetIndex`,  `Neo.Header.GetConsensusData`, `Neo.Header.GetNextConsensus`, `Neo.Block.GetTransactionCount` , `Neo.Block.GetTransactions`, `Neo.Block.GetTransaction`, `Neo.Transaction.GetHash`, `Neo.Transaction.GetType`,  `Neo.Transaction.GetAttributes`, `Neo.Transaction.GetInputs`, `Neo.Transaction.GetOutputs`, `Neo.Transaction.GetReferences`, `Neo.Transaction.GetUnspentCoins`, `Neo.Transaction.GetWitnesses`, `Neo.InvocationTransaction.GetScript`, `Neo.Witness.GetVerificationScript`, `Neo.Attribute.GetUsage`, `Neo.Attribute.GetData`, `Neo.Input.GetHash`, `Neo.Input.GetIndex`, `Neo.Output.GetAssetId`, `Neo.Output.GetValue`, `Neo.Output.GetScriptHash`, `Neo.Account.GetScriptHash` , `Neo.Account.GetVotes`, `Neo.Account.GetBalance`, `Neo.Account.IsStandard`, `Neo.Asset.Create`, `Neo.Asset.Renew`, `Neo.Asset.GetAssetId` , `Neo.Asset.GetAssetType`, `Neo.Asset.GetAmount`, `Neo.Asset.GetAvailable`, `Neo.Asset.GetPrecision`, `Neo.Asset.GetOwner`, `Neo.Asset.GetAdmin`, `Neo.Asset.GetIssuer`, `Neo.Contract.Create`, `Neo.Contract.Migrate`, `Neo.Contract.Destroy`, `Neo.Contract.GetScript`, `Neo.Contract.IsPayable`, `Neo.Contract.GetStorageContext`, `Neo.Storage.GetContext` , `Neo.Storage.GetReadOnlyContext`,  `Neo.Storage.Get`, `Neo.Storage.Put`, `Neo.Storage.Delete`, `Neo.Storage.Find`, `Neo.StorageContext.AsReadOnly`, `Neo.Enumerator.Create`, `Neo.Enumerator.Next`, `Neo.Enumerator.Value`, `Neo.Enumerator.Concat`, `Neo.Iterator.Create`, `Neo.Iterator.Key`, `Neo.Iterator.Keys`, `Neo.Iterator.Values`, `Neo.Iterator.Concat`.

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
    "abi": {
      "hash": "0x562851057d8afbc08fabc8c438d7cc771aef2195",
      "entrypoint": {
        "name": "main",
        "parameters": [
          {
            "name": "operation",
            "type": "String"
          },
          {
            "name": "args",
            "type": "Array"
          }
        ],
        "returntype": "Any"
      },
      "methods": [
        {
          "name": "name",
          "parameters": [],
          "returntype": "string"
        }
      ],
      "events": [
        {
          "name": "transfered",
          "parameters": [
            {"name": "from","type": "Hash160"},
            {"name": "to","type": "Hash160"},
            {"name": "value","type": "Integer"}
          ]
        }
      ]
    },
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

* **System Trigger** 

  This type of trigger is newly added in NEO3, which is only used for native contract, such as NEO and GAS. It is triggered when the node receives a new block and currently only triggers the execution of the native contract. After receiving a new block, the node will be triggered by the System Trigger to invoke the onPersist method in all native contracts before the persistence.

  This system trigger will not bring any affect to the normal smart contract. 

* **Application Trigger**
  
   An application trigger is used to invoke a contract as an application function, which can accept multiple parameters, change blockchain status, and return values of any type. Here is a simple c# smart contract：

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

* **Verification Trigger** 

  The purpose of the validation trigger is to call the contract as a verification function that accepts multiple arguments and should return a Boolean value indicating the validity of the transaction or block.

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
> Method marked * is [NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki) standard interface.
- **name**： Name of the token

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
  Example in C# contract

    ```csharp
    using Neo.SmartContract.Framework;
    using Neo.SmartContract.Framework.System;

    public static object Main(string method, object[] args)
    {
      private static string neoScriptHash = "0x43cf98eddbe047e198a3e5d57006311442a0ca15";
        if (Runtime.Trigger == TriggerType.Application)
        {
            if (method == "neoName") {
              string name = Contract.Call(neoScriptHash.HexToBytes(), "name", new object[]{});
              return name;
            }
        }  
    }
    ```
  Build Script

  Using System.Contract.Call to invoke contract:
  ```
  PUSH0
  NEWARRAY
  PUSHBYTES4  6e616d65
  PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15
  SYSCALL     0x627d5b52
  ```

  C# code to build the script：
  ```
  ScriptBuilder sb = new ScriptBuilder()
  UInt160 scriptHash = UInt160.Parse("0x43cf98eddbe047e198a3e5d57006311442a0ca15");
  sb.EmitPush(0);
  sb.Emit(OpCode.NEWARRAY);
  sb.EmitPush("name");
  sb.EmitPush(scriptHash.ToArray());
  sb.EmitSysCall(InteropService.System_Contract_Call);
  byte[] script = sb.ToArray();
  ```
- **symbol**：Symbol of the token

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

- **decimals**: Decimals of the token

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

- **balanceOf**: Balance of an account

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

- **transfer**: Transfer token from one to another

  <table class="mytable">
  <tr >
  <th rowspan="4">Parameters</th>
  <th >Parameter</th>
  <th >Type</th>
  <th  >Description</th>
  </tr>
  <tr >
  <td>from</td>
  <td >Hash160</td>
  <td>ScriptHash who want to transfer</td>
  </tr >
  <tr >
  <td>to</td>
  <td >Hash160</td>
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
  Example in C# contract

  ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.System;

  public static object Main(string method, object[] args)
  {
    private static string neoScriptHash = "0x43cf98eddbe047e198a3e5d57006311442a0ca15";
      if (Runtime.Trigger == TriggerType.Application)
      {
          if (method == "transferNeo") {
            byte[] from  = "AesUJTLg93cWMTSzp2snxpBJSCets89ebM".ToScriptHash();
            byte[] to    = "AMhbbwR8r6LuTx5okkZudvvp3LW6Fh1Y7o".ToScriptHash();
            BigInterger value = new BigInteger(100000000);
            bool result = Contract.Call(neoScriptHash.HexToBytes(), "transfer", new Object[]{from, to, value.AsByteArray()});
            return result;
          }
      }  
  }
  ```
  Build Script

  Using System.Contract.Call to invoke contract:
  ```
  PUSHBYTE4   00e1f505
  PUSHBYTE20  4101b2a928fd88e1d976fd23c2db25a822338a08
  PUSHBYTE20  fd59e6a0e3eee5cd9cea7233f01e1cc9c8b23502
  PUSH3
  PACK
  PUSHBYTES4  7472616e73666572
  PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15
  SYSCALL     0x627d5b52
  ```

  C# code to build the script：
  ```
  ScriptBuilder sb = new ScriptBuilder()
  UInt160 scriptHash = UInt160.Parse("0x43cf98eddbe047e198a3e5d57006311442a0ca15");
  UInt160 from = UInt160.Parse("0xfd59e6a0e3eee5cd9cea7233f01e1cc9c8b23502");
  UInt160 to = UInt160.Parse("0x4101b2a928fd88e1d976fd23c2db25a822338a08");
  long value = 1000000000;
  sb.EmitPush(value);
  sb.EmitPush(to);
  sb.EmitPush(from);
  sb.Emit(OpCode.PUSH3);
  sb.Emit(OpCode.PACK);
  sb.EmitPush("transfer");
  sb.EmitPush(scriptHash.ToArray());
  sb.EmitSysCall(InteropService.System_Contract_Call);
  byte[] script = sb.ToArray();
  ```
- **unClaimGas**：Get the amount of GAS unclaimed at the specified height

  <table class="mytable">
  <tr >
  <th rowspan="3">Parameters</th>
  <th >Parameter</th>
  <th >Type</th>
  <th  >Description</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash160</td>
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
  Example in C# contract

    ```csharp
  using Neo.SmartContract.Framework;
  using Neo.SmartContract.Framework.System;

  public static object Main(string method, object[] args)
  {
    private static string neoScriptHash = "0x43cf98eddbe047e198a3e5d57006311442a0ca15";
      if (Runtime.Trigger == TriggerType.Application)
      {
          if (method == "accountUnClaimGas") {
            byte[] account = "AXx1A21wcoXuVxxxggkQChxQP5EGYe6zsN".ToScriptHash();
            int height = 1000000;
            int gas = Contract.Call(neoScriptHash.HexToBytes(), "unClaimGas", new Object[]{account, height});
            return gas;
          }
      }  
  }
    ```
  Build Script

    Using System.Contract.Call to invoke contract:
    ```
    PUSHBYTE3   40420f
    PUSHBYTE20  b16c70b94928ddb62f5793fbc98d6245ee308ecd
    PUSH2
    PACK
    PUSHBYTES4  756e436c61696d476173
    PUSHBYTES20 0x43cf98eddbe047e198a3e5d57006311442a0ca15
    SYSCALL     0x627d5b52
    ```

    C# code to build the script：
    ```
    ScriptBuilder sb = new ScriptBuilder()
    UInt160 scriptHash = UInt160.Parse("0x43cf98eddbe047e198a3e5d57006311442a0ca15");
    UInt160 account = UInt160.Parse("0xb16c70b94928ddb62f5793fbc98d6245ee308ecd");
    int height = 1000000
    sb.EmitPush(height);
    sb.EmitPush(account);
    sb.Emit(OpCode.PUSH2);
    sb.Emit(OpCode.PACK);
    sb.EmitPush("unClaimGas");
    sb.EmitPush(scriptHash.ToArray());
    sb.EmitSysCall(InteropService.System_Contract_Call);
    byte[] script = sb.ToArray();
    ```
- **RegisterValidator**：Register to become a candidate for the validator

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

  <table class="mytable">
  <tr >
  <th rowspan="3">Parameters</th>
  <th >Parameter</th>
  <th >Type</th>
  <th  >Description</th>
  </tr>
  <tr >
  <td>account</td>
  <td >Hash160</td>
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

#### GasToken

Referred to as GAS, Neo's fuel token, used to pay the handling fee, in line with NEP-5 standards. Transaction costs and consensus node incentives on the Neo network are paid in GAS. The total amount of GAS is 100 million, and the minimum unit is 0.00000001. GAS is registered in the founding block but not distributed.
GAS distribution mechanism: When a new block is generated, a new GAS is generated, and the generated GAS and system fees are distributed to NEO holders. The calculation and distribution of GAS are automatically performed when the NEO balance in the user's address changes. The calculation method is as follows:  

![GAS distribution](../../images/GAS_distribution.png) 

Where `m` is the height of the block where the user last extracted the GAS, `n` is the current block height, and `NEO` is the number of NEOs held by the user during `m` to `n`. `BlockBonus` represents the amount of GAS that each block can extract. `SystemFee`  represents the sum of the system fees for all transactions in this block.

GasToken methods details following：
> Method marked * is [NEP-5](https://github.com/neo-project/proposals/blob/master/nep-5.mediawiki) standard interface
- **name**: Name of the token

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

- **decimals**: Decimals of the token

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

- **totalSupply**: Total supply

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

- **balanceOf**: Balance of a specific account

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

- **transfer**: Transfer token from one account to another

  <table class="mytable">
  <tr >
  <th rowspan="4">Parameters</th>
  <th >Parameter</th>
  <th >Type</th>
  <th  >Description</th>
  </tr>
  <tr >
  <td>from</td>
  <td >Hash160</td>
  <td>ScriptHash of the sender's account</td>
  </tr >
  <tr >
  <td>to</td>
  <td >Hash160</td>
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

- **getSysFeeAmount**: Total system fee till a specific height

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

#### PolicyContract

It is the contract for configuring the consensus strategy and saves relevant parameters in the consensus process, including maximum transactions per block, maximum low-priority transactions per block, maximum low-priority-transaction size, the fee per byte, etc. The interface is described in detail below：

- getMaxTransactionPerBlock: Get maximum transactions per block.

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

- **blockAccount**：Add an account into the blacklist.

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

#### System.Binary.Serialize  

| Description | Convert StackItem to byte array|
|--|-- |
| Fee (GAS) | 0.001 |

#### System.Binary.Deserialize  

| Description | Convert byte array to StackItem  |
|--|-- |
| Fee (GAS) | 0.005 |

#### System.Blockchain.GetHeight

| Description | Get the height of the current block|
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Blockchain.GetBlock

| Description | Get a block with the hash or block height |
|--|-- |
| Fee (GAS) | 0.025 |

#### System.Blockchain.GetTransaction

| Description | Get a transaction with the txid |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Blockchain.GetTransactionHeight

| Description | Get the height of the block where the transaction included with the txid|
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Blockchain.GetTransactionFromBlock

| Description | Get a transaction with the txid in the block |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Blockchain.GetContract

| Description | Get a contract with the hash |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Contract.Create
| Description | Deploy a contract |
|--|-- |
| Explanation | The size of the script contract cannot exceed 1MB and the size of the manifest cannot exceed 2KB |
| Fee (GAS) | (Script.Size + Manifest.Size) * GasPerByte |

#### System.Contract.Update
| Description | Upgrade a contract |
|--|-- |
| Explanation | The size of the script contract cannot exceed 1MB and it cannot be deployed before. The size of the <br/> manifest cannot exceed 2KB. The old contract will be destroyed after the upgrade. |
| Fee (GAS) | (Script.Size + Manifest.Size) * GasPerByte |

#### System.Contract.Destroy

| Description | Destroy a contract |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Contract.Call 

| Description | Invoke a contract |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Contract.CallEx
| Description | Invoke a contract with the Flag |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Contract.IsStandard
| Description | Check whether the contract is a standard contract |
|--|-- |
| Fee (GAS) | 0.0003 |

#### System.Enumerator.Create
| Description | Create a enumerator |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Enumerator.Next
| Description | Check if the enumerator has more element |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Enumerator.Value
| Description | Get the current value of the enumerator |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Enumerator.Concat
| Description | Concat two enumerators |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Iterator.Create
| Description | Create an iterator |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Iterator.Key
| Description | Get the current key of the iterator |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Iterator.Keys
| Description | Get all keys of the iterator |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Iterator.Values
| Description | Get all values of the iterator |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Iterator.Concat
| Description | Concat two iterators |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Json.Serialize
| Description | Serialize a stack item to byte array |
|--|-- |
| Fee (GAS) | 0.001 |

#### System.Json.Deserialize
| Description | Convert json object to stack item |
|--|-- |
| Fee (GAS) | 0.005 |

#### System.Runtime.Platform

| Description | Get the platform information of the contract being executed |
|--|-- |
| Fee (GAS) | 0.0000025 |

#### System.Runtime.GetTrigger

| Description | Get the triggering condition of the contract |
|--|-- |
| Fee (GAS) | 0.0000025 |

#### System.Runtime.GetTime

| Description | Get the timestamp of the current block |
|--|-- |
| Fee (GAS) | 0.0000025 |

#### System.Runtime.GetScriptContainer
| Description | Get the script container of the contract |
|--|-- |
| Fee (GAS) | 0.0000025 |

#### System.Runtime.GetExecutingScriptHash
| Description | Get the script hash of the executing contract|
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Runtime.GetCallingScriptHash
| Description | Get the script hash of the calling contract |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Runtime.GetEntryScriptHash
| Description | Get the script hash of the entry contract |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Runtime.CheckWitness

| Description | Verify whether the container calling the contract is signed by the<br/>script hash of the specific account |
|--|-- |
| Fee (GAS) | 0.0003 |

#### System.Runtime.GetInvocationCounter

| Description | Get invocation count of the current contract |
|--|-- |
| Fee (GAS) | 0.000004 |

#### System.Runtime.Log

| Description | Record the log  |
|--|-- |
| Fee (GAS) | 0.005 |
#### System.Runtime.Notify

| Description | Notify the client executing the contract |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Runtime.GetNotifications

| Description | Get notifications of a contract |
|--|-- |
| Fee (GAS) | 0.0001 |

#### System.Storage.GetContext

| Description | Get storage context of the current contract |
|--|-- |
| Explanation | the IsReadOnly flag of StorageContext is false |
| Fee (GAS) | 0.000004 |

#### System.Storage.GetReadOnlyContext

| Description | Get storage context of the current contract in read-only mode |
|--|-- |
| Explanation | the IsReadOnly flag of StorageContext is true  |
| Fee (GAS) | 0.000004 |

#### System.Storage.Get

| Description | Get value from the storage by key |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Storage.Find

| Description | Find the data in the storage area of the current storage context with the specified prefix |
|--|-- |
| Fee (GAS) | 0.01 |

#### System.Storage.Put

| Description | Put key-value into storage based on the storage context |
|--|-- |
| Fee (GAS) | (Key.Size + Value.Size) * GasPerByte |

#### System.Storage.PutEx

| Description | Put Key-Value into the storage based on the storage context and the flag|
|--|-- |
| Explanation | The parameter StorageFlags defines the attribute of the written data. The default value <br/>is None, indicating that data can be read and written. If it is Constant, the data cannot be <br/>modified or deleted once it is written to the storage area.|
| Fee (GAS) | (Key.Size + Value.Size) * GasPerByte |

#### System.Storage.Delete

| Description | Delete the stored Key-Value data from the storage area by the Key |
|--|-- |
| Explanation |  Data cannot be deleted if its StorageFlags is Constant |
| Fee (GAS) | 0.01 |

#### System.StorageContext.AsReadOnly

| Description | Set the current context to read-only mode |
|--|-- |
| Explanation | Set the IsReadOnly flag of the StorageContext to true |
| Fee (GAS) | 0.000004 |

### Neo Part

#### Neo.Native.Deploy

| Description | Deploy and initialize all native contracts |
|--|-- |
| Explanation | It can only be invoked in the genesis block |
| Fee (GAS) | 0 |

#### Neo.Crypto.ECDsaVerify
| Description | Verify signature of the current script container by public key |
|--|-- |
| Fee (GAS) | 0.01 |

#### Neo.Crypto.ECDsaCheckMultiSig
| Description | Verify the multiple signature of the current script container by public key |
|--|-- |
| Fee (GAS) | 0.01 * n |

## Accessing to Internet Resources
## Contract Invocation
### Invoke another contract in contract
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
### Invoke contract in script
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
  sb.EmitPush(scriptHash.ToArray());
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

*Click [here](../../cn/合约) to see the Chinese edition of the Smart Contract*
