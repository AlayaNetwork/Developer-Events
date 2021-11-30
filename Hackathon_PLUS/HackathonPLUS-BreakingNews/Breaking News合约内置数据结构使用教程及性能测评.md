前期发布过wasm合约内置数据结构的使用教程，详见《[PlatON WASM contract (八) - 内置数据结构](https://devdocs.platon.network/docs/zh-CN/WASM_Contract_8)》。我们正在参加的黑客松活动刚好对链上数据存储及访问有一定性能上的需求，本教程将针对性能方面做一个简单的测评。

## 合约源码

### WasmDataStruct.h

```c++
#pragma once

#include <platon/platon.hpp>
#include <string>
#include <vector>

struct UserDefinedData
{
    platon::u128    uID;
    std::string     uName;

    std::string     uContent;

    PLATON_SERIALIZE(UserDefinedData, (uID)(uName)(uContent))

    platon::u128 UserID() const { return uID; }
    std::string UserName() const { return uName; }
};

class WasmDataStruct
{
    //map
    platon::db::Map<"UserDataMap"_n, platon::u128, UserDefinedData> _mUserMap;

    //multiIndex table
    platon::db::MultiIndex<
        "UserTable"_n, UserDefinedData,
        platon::db::IndexedBy<"id"_n, platon::db::IndexMemberFun<UserDefinedData, platon::u128, &UserDefinedData::UserID,
        platon::db::IndexType::UniqueIndex>>,
        platon::db::IndexedBy<"name"_n, platon::db::IndexMemberFun<UserDefinedData, std::string, &UserDefinedData::UserName,
        platon::db::IndexType::NormalIndex>>>
        
        _mUser_table;

    //StorageType
    platon::StorageType<"UserData"_n, std::map<platon::u128, UserDefinedData>>      _mUser_Data;

public:
    ACTION void init();

    ACTION void AddUser(const UserDefinedData& udd);

    ACTION void ModifyUserInfo(const UserDefinedData& udd);

    ACTION void UserErase(const platon::u128& uID);

    ACTION void Clear();

    CONST std::vector<UserDefinedData> getUserFromTable_ID(const platon::u128& uID);
    CONST std::vector<UserDefinedData> getUserFromTable_Name(const std::string& uName);

    CONST std::vector<UserDefinedData> getUserFromMap(const platon::u128& uID);

    CONST std::vector<UserDefinedData> getUserFromStorageType(const platon::u128& uID);

    // Performance Evaluation
    ACTION void CreateDataForMap(const platon::u128& MinID, const platon::u128& MaxID);
    ACTION void CreateDataForST(const platon::u128& MinID, const platon::u128& MaxID);
    ACTION void CreateDataForMI(const platon::u128& MinID, const platon::u128& MaxID);

    ACTION void AddUserForMap(const UserDefinedData& udd);
    ACTION void AddUserForST(const UserDefinedData& udd);
    ACTION void AddUserForMI(const UserDefinedData& udd);

    ACTION void ModifyForMap(const UserDefinedData& udd);
    ACTION void ModifyForST(const UserDefinedData& udd);
    ACTION void ModifyForMI(const UserDefinedData& udd);

    ACTION void EraseForMap(const platon::u128& uID);
    ACTION void EraseForST(const platon::u128& uID);
    ACTION void EraseForMI(const platon::u128& uID);

    // Clear data who's ID number are less than MaxID
    ACTION void BatchClear(const platon::u128& MaxID);

    CONST std::string getItemDescription();
};

PLATON_DISPATCH(WasmDataStruct, (init)
(CreateDataForMap)(CreateDataForST)(CreateDataForMI)
(AddUserForMap)(AddUserForST)(AddUserForMI)
(ModifyForMap)(ModifyForST)(ModifyForMI)
(EraseForMap)(EraseForST)(EraseForMI)
(getItemDescription)
(BatchClear)
(getUserFromTable_ID)(getUserFromMap)(getUserFromStorageType))

```
### WasmDataStruct.cpp

```c++
#include "WasmDataStruct.h"

void WasmDataStruct::init(){
    
}

void WasmDataStruct::AddUser(const UserDefinedData& udd){
    _mUserMap[udd.uID] = udd;
    _mUser_Data.self()[udd.uID] = udd;

    _mUser_table.emplace([&](auto& userTable) {
        userTable = udd;
        });
}

void WasmDataStruct::ModifyUserInfo(const UserDefinedData& udd){
    //_mUserMap
    if (_mUserMap.contains(udd.uID))
    {
        _mUserMap[udd.uID] = udd;
    }

    //_mUser_Data
    auto udItr = _mUser_Data.self().find(udd.uID);
    if (udItr != _mUser_Data.self().end())
    {
        udItr->second = udd;
    }

    //_mUser_table
    auto uTableItr = _mUser_table.find<"id"_n>(udd.uID);
    if (uTableItr != _mUser_table.cend())
    {
        _mUser_table.modify(uTableItr, [&](auto& userData) {
            userData = udd;
            });
    }
}

void WasmDataStruct::UserErase(const platon::u128& uID){
    //_mUserMap
    if (_mUserMap.contains(uID))
    {
        _mUserMap.erase(uID);
    }

    //_mUser_Data
    auto udItr = _mUser_Data.self().find(uID);
    if (udItr != _mUser_Data.self().end())
    {
        _mUser_Data.self().erase(udItr);
    }

    //_mUser_table
    auto uTableItr = _mUser_table.find<"id"_n>(uID);
    if (uTableItr != _mUser_table.cend())
    {
        _mUser_table.erase(uTableItr);
    }
}

void WasmDataStruct::Clear(){
    //the clear of db::Map and db::MultiIndex are Some trouble
    std::vector<platon::u128> idVec;
    for (auto usItr = _mUser_Data.self().begin(); usItr != _mUser_Data.self().end(); ++usItr)
    {
        idVec.push_back(usItr->first);
    }

    //normal map
    _mUser_Data.self().clear();

    //db::Map
    for (auto idItr = idVec.begin(); idItr != idVec.end(); ++idItr)
    {
        _mUserMap.erase(*idItr);
    }

    //db::MultiIndex
    auto tableItr = _mUser_table.cbegin();
    while (tableItr != _mUser_table.cend())
    {
        auto tempItr = tableItr;
        ++tableItr;
        
        _mUser_table.erase(tempItr);
    }
}

std::vector<UserDefinedData> WasmDataStruct::getUserFromTable_ID(const platon::u128& uID){
    std::vector<UserDefinedData> udVec;

    auto uTableItr = _mUser_table.find<"id"_n>(uID);
    if (uTableItr != _mUser_table.cend())
    {
        
        udVec.push_back(*uTableItr);
    }

    //if not found, return []
    return udVec;
}

std::vector<UserDefinedData> WasmDataStruct::getUserFromTable_Name(const std::string& uName){
    std::vector<UserDefinedData> udVec;
    //Here's where it gets more confusing, maybe set parameters in get_index is better
    auto normalIndexes = _mUser_table.get_index<"name"_n>();
    for (auto itemItr = normalIndexes.cbegin(uName); itemItr != normalIndexes.cend(uName); ++itemItr)
    {
        udVec.push_back(*itemItr);
    }

    return udVec;
}

std::vector<UserDefinedData> WasmDataStruct::getUserFromMap(const platon::u128& uID){
    std::vector<UserDefinedData> udVec;

    if (_mUserMap.contains(uID))
    {
        udVec.push_back(_mUserMap[uID]);
    }

    return udVec;
}

std::vector<UserDefinedData> WasmDataStruct::getUserFromStorageType(const platon::u128& uID){
    std::vector<UserDefinedData> udVec;

    auto udItr = _mUser_Data.self().find(uID);
    if (udItr != _mUser_Data.self().end())
    {
        udVec.push_back(udItr->second);
    }

    return udVec;
}

// Performace Evaluation
void WasmDataStruct::CreateDataForMap(const platon::u128& MinID, const platon::u128& MaxID)
{
    for (auto i = MinID; i < MaxID; ++i)
    {
        UserDefinedData udd;
        udd.uID = i;
        udd.uName = std::to_string(i);
        udd.uContent = "Hello";

        _mUserMap[udd.uID] = udd;
    }
}

void WasmDataStruct::CreateDataForST(const platon::u128& MinID, const platon::u128& MaxID)
{
    for (auto i = MinID; i < MaxID; ++i)
    {
        UserDefinedData udd;
        udd.uID = i;
        udd.uName = std::to_string(i);
        udd.uContent = "Hello";

        _mUser_Data.self()[udd.uID] = udd;
    }
}

void WasmDataStruct::CreateDataForMI(const platon::u128& MinID, const platon::u128& MaxID)
{
    for (auto i = MinID; i < MaxID; ++i)
    {
        UserDefinedData udd;
        udd.uID = i;
        udd.uName = std::to_string(i);
        udd.uContent = "Hello";

        _mUser_table.emplace([&](auto& userTable) {
            userTable = udd;
            });
    }
}

void WasmDataStruct::AddUserForMap(const UserDefinedData& udd)
{
    _mUserMap[udd.uID] = udd;
}

void WasmDataStruct::AddUserForST(const UserDefinedData& udd)
{
    _mUser_Data.self()[udd.uID] = udd;
}

void WasmDataStruct::AddUserForMI(const UserDefinedData& udd)
{
    _mUser_table.emplace([&](auto& userTable) {
        userTable = udd;
        });
}

void WasmDataStruct::ModifyForMap(const UserDefinedData& udd)
{
    //_mUserMap
    if (_mUserMap.contains(udd.uID))
    {
        _mUserMap[udd.uID] = udd;
    }
}

void WasmDataStruct::ModifyForST(const UserDefinedData& udd)
{
    //_mUser_Data
    auto udItr = _mUser_Data.self().find(udd.uID);
    if (udItr != _mUser_Data.self().end())
    {
        udItr->second = udd;
    }
}

void WasmDataStruct::ModifyForMI(const UserDefinedData& udd)
{
    //_mUser_table
    auto uTableItr = _mUser_table.find<"id"_n>(udd.uID);
    if (uTableItr != _mUser_table.cend())
    {
        _mUser_table.modify(uTableItr, [&](auto& userData) {
            userData = udd;
            });
    }
}

void WasmDataStruct::EraseForMap(const platon::u128& uID){
    //_mUserMap
    if (_mUserMap.contains(uID))
    {
        _mUserMap.erase(uID);
    }
}

void WasmDataStruct::EraseForST(const platon::u128& uID){
    //_mUser_Data
    auto udItr = _mUser_Data.self().find(uID);
    if (udItr != _mUser_Data.self().end())
    {
        _mUser_Data.self().erase(udItr);
    }
}

void WasmDataStruct::EraseForMI(const platon::u128& uID){
    //_mUser_table
    auto uTableItr = _mUser_table.find<"id"_n>(uID);
    if (uTableItr != _mUser_table.cend())
    {
        _mUser_table.erase(uTableItr);
    }
}

std::string WasmDataStruct::getItemDescription()
{
    std::string output = "Items count: ";

    auto count = _mUser_Data.self().size();

    output += std::to_string(count);

    return output;
}

void WasmDataStruct::BatchClear(const platon::u128& MaxID){
    //normal map
    auto ST_Itr = _mUser_Data.self().begin();
    while (ST_Itr != _mUser_Data.self().end())
    {
        auto tempItr = ST_Itr;
        ++ST_Itr;
        if (tempItr->first < MaxID)
        {
            _mUser_Data.self().erase(tempItr);
        }
    }

    //db::Map
    for (auto id = 0; id < MaxID; ++id)
    {
        if (_mUserMap.contains(id))
        {
            _mUserMap.erase(id);
        }
    }

    //db::MultiIndex
    // auto tableItr = _mUser_table.cbegin();
    // while (tableItr != _mUser_table.cend())
    // {
    //  if (tableItr->uID < MaxID)
    //  {
    //      auto tempItr = tableItr;
    //      _mUser_table.erase(tempItr);
    //  }

    //  ++tableItr;
    // }

    for (auto id = 0; id < MaxID; ++id)
    {
        auto uTableItr = _mUser_table.find<"id"_n>(id);
        if (uTableItr != _mUser_table.cend())
        {
            _mUser_table.erase(uTableItr);
        }
    }
}

```
### 合约代码说明：

* 在《[PlatON WASM contract (八) - 内置数据结构](https://devdocs.platon.network/docs/zh-CN/WASM_Contract_8)》的基础之上，增加了性能测试的接口；
* 同时为了避免外部调用误操作，《[PlatON WASM contract (八) - 内置数据结构](https://devdocs.platon.network/docs/zh-CN/WASM_Contract_8)》中的基础接口将不再“导出”（即从外部将不可调用）；
* CreateData将用于设置测试规模（增量）；
* 该合约的设计主要针对性能测试，实际开发中，需要注意 `db::Map` 的使用问题（它没有使用迭代器遍历访问的接口，在使用中如果要遍历，可能需要将 `key` 通过其他途径存下来，这一点从使用上说其实是不太方便的，这主要与持久化存储有关，特点是单项数据的查询、修改很快）； 
* 主要针对三种内置数据容器中数据的单项添加、修改、删除操作进行性能测试；
* 值得一提的是，在BatchClear的实现中，对于`db::MultiIndex` ，直观上看，删除操作直接利用迭代器来遍历（代码中注释掉的部分），在每个loop中会少一次查找的开销。但是，通过测试，发现实际上当数据量较多时，迭代器遍历的的开销非常的巨大，有限gas内即使清理一条数据都无法实现，反而是目前代码中的实现方式，gas消耗可以接收（BatchClear可以分多次调用来清理数据）。这与链上持久化存储合约数据的机制有关， `db::Map` 推测有相同的情况，这也是`db::Map` 没有提供迭代器操作的原因。
## 测试用例

### 代码

#### DataStructPerformace.py

```python
from operator import add
from client_sdk_python import Web3, HTTPProvider, WebsocketProvider
from client_sdk_python.eth import PlatON
from client_sdk_python import utils
from client_sdk_python.utils import contracts

import json

ContractAddr = 'lat1au3vujt9867xyln7nmpcjm6l32jvdycyegf29h'

clientAccount = 'lat1hszgljlqnlly6hxcneq6v2vdnaj9hfvm7m7ccz'
devIP = 'http://47.241.69.26:6789'

ABI_filepath = './PlatON/config/WasmDataStruct.abi.json'

ID_Slice = 25
Slice_Range = 8
MaxID = 1000000

def CreateDataForMap():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    for i in range(Slice_Range):
        tx_receipt = hello.functions.CreateDataForMap(i * ID_Slice, (i + 1) * ID_Slice).transact({'from':clientAccount,'gas':9000000})
        receipt_info = platon.waitForTransactionReceipt(tx_receipt)
        print('Create ', ID_Slice, 'data for db::Map. Status: ', receipt_info['status'], '. CumulativeGasUsed: ', receipt_info['cumulativeGasUsed'])

def CreateDataForST():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    for i in range(Slice_Range):
        tx_receipt = hello.functions.CreateDataForST(i * ID_Slice, (i + 1) * ID_Slice).transact({'from':clientAccount,'gas':9000000})
        receipt_info = platon.waitForTransactionReceipt(tx_receipt)
        print('Create ', ID_Slice, 'data for StorageType. Status: ', receipt_info['status'], '. cumulativeGasUsed: ', receipt_info['cumulativeGasUsed'])

def CreateDataForMI():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    for i in range(Slice_Range):
        tx_receipt = hello.functions.CreateDataForMI(i * ID_Slice, (i + 1) * ID_Slice).transact({'from':clientAccount,'gas':9000000})
        receipt_info = platon.waitForTransactionReceipt(tx_receipt)
        print('Create ', ID_Slice, 'data for db::Multi_Index. Status: ', receipt_info['status'], '. cumulativeGasUsed: ', receipt_info['cumulativeGasUsed'])

    
def getItemDescription():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    print(hello.functions.getUserFromMap(450).call())
    print(hello.functions.getUserFromTable_ID(100).call())
    print(hello.functions.getUserFromStorageType(150).call())

def clear():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    tx_receipt = hello.functions.BatchClear(750).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print(receipt_info)

def Add():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    tx_receipt = hello.functions.AddUserForMap([MaxID, 'jason', 'Hello']).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('AddUserForMap: ', receipt_info['cumulativeGasUsed'])

    tx_receipt = hello.functions.AddUserForST([MaxID, 'jason', 'Hello']).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('AddUserForST: ', receipt_info['cumulativeGasUsed'])

    tx_receipt = hello.functions.AddUserForMI([MaxID, 'jason', 'Hello']).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('AddUserForMI: ', receipt_info['cumulativeGasUsed'])

def Modify():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    tx_receipt = hello.functions.ModifyForMap([MaxID, 'jason', 'World']).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('ModifyForMap: ', receipt_info['cumulativeGasUsed'])

    tx_receipt = hello.functions.ModifyForST([MaxID, 'jason', 'World']).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('ModifyForST: ', receipt_info['cumulativeGasUsed'])

    tx_receipt = hello.functions.ModifyForMI([MaxID, 'jason', 'World']).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('ModifyForMI: ', receipt_info['cumulativeGasUsed'])

def Erase():
    fp = open(ABI_filepath)
    Contract_abi = json.load(fp)
    w3 = Web3(HTTPProvider(devIP))
    platon = PlatON(w3)

    hello = platon.wasmcontract(address=ContractAddr, abi=Contract_abi, vmtype=1)

    tx_receipt = hello.functions.EraseForMap(MaxID).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('EraseUserForMap: ', receipt_info['cumulativeGasUsed'])

    tx_receipt = hello.functions.EraseForST(MaxID).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('EraseUserForST: ', receipt_info['cumulativeGasUsed'])

    tx_receipt = hello.functions.EraseForMI(MaxID).transact({'from':clientAccount,'gas':9000000})
    receipt_info = platon.waitForTransactionReceipt(tx_receipt)
    print('EraseUserForMI: ', receipt_info['cumulativeGasUsed'])

#CreateDataForMap()
#CreateDataForST()
#CreateDataForMI()
#getItemDescription()
#clear()
#Add()
#Modify()
#Erase()

```
#### 说明

* create相关操作，分别针对 `db:Map` 、  `StorageType` 、 `db::MultiIndex` 增量的写入数据，每种类型总共插入500条数据，分20轮次，每轮次25条；
* add、modify、erase为单项数据的相关操作；
* clear批量的清理链上数据。
### 测试场景

* 增量测试
    * 分别调用 `CreateDataForMap` 、 `CreateDataForMI` 、 `CreateDataForST` ，向`db:Map` 、  `StorageType` 、 `db::MultiIndex` 中分轮次批量添加500条数据，三种类型分别测试，不受其他数据影响；
    * `db:Map`的gas消耗如下：
![image](public/images/1.png)

    * `db::MultiIndex`的gas消耗如下：
![image](public/images/2.png)

    * `StorageType`的gas消耗如下：
![image](public/images/3.png)

可以看到，实际上最后5次添加数据已经失败了。

    * 通过对三种类型的gas消耗对比分析，可以看出，对于`db:Map` 和`db::MultiIndex` ，虽然数据量持续增长，但每个轮次gas消耗基本稳定；对于`StorageType` 则随着数据的增长gas消耗持续增长。
![image](public/images/4.png)

* clear后，依次调用`CreateDataForMap` 、 `CreateDataForMI` 、 `CreateDataForST` ，分别添加500条数据，共计1500条数据，此时进行单项测试
    * 调用 `Add` 、`Modify` 、`Erase` ，执行添加单项数据操作，结果如下：
![image](public/images/567.png)

    * 从针对三种数据结构的单项数据操作看，再已经填充了数据的情况下（几百条的量级，应该说数据量并不大），三者都消耗了865万左右的gas。
    * 咋看起来三种数据结构几乎性能一致，但是，回顾前文提到的增量测试的情况，对于三种数据结构，每个loop中都插入了25条数据，在 `db::Map` 、`db::MultiIndex`数据结构中，尽管数据量递增，但每个轮次消耗的gas几乎稳定，且相对低很多（约160万和280万）。而在单项测试时，即使Add一条数据，gas消耗都达到了非常高的程度，这应该是不太合理的。
    * 而单项测试中的调用，针对`db::Map` 、`db::MultiIndex` 的情况，与前文对比，从合约状态上看主要是多了 `StorageType` 数据类型的批量数据，所以推测是它对合约性能造成了影响。
下面我们专门针对这一点，进行一些测试来证明我们的推测。

* `StorageType` 对性能的影响
    * 重新部署合约，向`db::Map` 、`db::MultiIndex` 分别添加750条数据，总计1500条，数据总量与前文中的测试数据总量相等，gas消耗的曲线分别为：
![image](public/images/8.png)

    * 分别调用`Add` 、`Modify` 、`Erase` ，结果如下：
![image](public/images/9.png)

    * 可以看出，数据线性增长时，`db::Map` 、`db::MultiIndex` 消耗的gas几乎稳定。同时单项操作的gas消耗非常少；
    * 接下来我们向StorageType中添加200条数据：
![image](public/images/10.png)

    * 再次分别调用`Add` 、`Modify` 、`Erase` ，结果如下：
![image](public/images/11.png)

    * 与前文中推测一致，使用`StorageType` 在链上持久化存储数据，不仅自身消耗很大，同时会影响合约其他类型数据访问的消耗。
## 总结

由 此可见，storagetype虽然使用上灵活性较高，但由于其在底层实现上每一次都需要将全部数据读取出来进行操作，所以性能最低。同时，还类型的数据规模太大，还会提高合约中其他持久化数据操作的开销（原因和合约以及虚拟机数据的整个持久化机制有关）。因此，如非必要，应尽量减少对storagetype类型的使用。目前，在Breaking News中也正在针对这方面进行优化。

