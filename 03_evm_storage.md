# References
https://www.youtube.com/watch?v=vTeav5Rinco

# Basics
- State Variables are stored as chunks of 32 bytes in the Ethereum Virtual Machine (EVM) storage.
- There are 2**256 slots in the EVM storage, each of which can store 32 bytes.
- Slots are assigned in the order in which the state variables are declared in the contract. Rules for dynamic arrays and mappings are different.
- Dynamic arrays and mappings are stored in a separate storage area, and the slot assigned to them is a hash of the key and the slot where the value is stored.
- State variables that are less than 32 bytes are packed together in a single slot.
- 


## sload and sstore
- `sload` is used to load a value from the EVM storage at a given slot.
- `sstore` is used to store a value in the EVM storage at a given slot.

`slot` keyword can be used to check the slot assigned to a state variable.

## Packing of State Variables in a Single Slot
Example:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StoragePacking {
    bytes2 public a = 0x1234;
    bytes4 public b = 0x12345678;
    bytes16 public c = 0x123456789abcdef0123456789abcdef0;  // Padded to be exactly 16 bytes
    bytes16 public d = 0x1234a56bca1b2cd39ac492a765974b01;  // Corrected to be exactly 16 bytes
    bytes16 public e = 0x1234a56bca1b2cd39ac492a765974b02;  // Corrected to be exactly 16 bytes
    bytes16 public f = 0x1234a56bca1b2cd39ac492a765974b03;  // Corrected to be exactly 16 bytes


    // returns - 0x00000000000000000000123456789abcdef0123456789abcdef0123456781234
    function getSlot0() public view returns (bytes32 x) { 
        assembly {
            x := sload(0)
        }
    }

    // returns - 0x1234a56bca1b2cd39ac492a765974b021234a56bca1b2cd39ac492a765974b01
    function getSlot1() public view returns (bytes32 x) { 
        assembly {
            x := sload(1)
        }
    }

    // returns - 0
    function printSlotA() public pure returns (uint x) {
        assembly {
            x := a.slot
        }
    }

    // returns - 0
    function printSlotB() public pure returns (uint x) {
        assembly {
            x := b.slot
        }
    }

    // returns - 0
    function printSlotC() public pure returns (uint x) {
        assembly {
            x := c.slot
        }
    }

    // returns - 1
    function printSlotD() public pure returns (uint x) {
        assembly {
            x := d.slot
        }
    }

    // returns - 1
    function printSlotE() public pure returns (uint x) {
        assembly {
            x := e.slot
        }
    }

    // returns - 2
    function printSlotF() public pure returns (uint x) {
        assembly {
            x := f.slot
        }
    }
}
```
Here
- `a` and `b` and `c` are packed together in a single slot. 
- `d` and `e` are packed together in a single slot.
- `f` is stored in a separate slot.
- The `slot` keyword can be used to check the slot assigned to a state variable.
- `a` is stored at the end of the slot, `b` is stored before `a`, and `c` is stored before `b`.


- The EVM storage is a key-value store that maps 32-byte keys to 32-byte values.


- The EVM storage is a key-value store that maps 32-byte keys to 32-byte values.

## Storage of Structs

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract EVMStorageStruct {
    struct SingleSlot {
        uint128 x; // This takes 16 bytes
        uint64 y; // This takes 8 bytes
        uint64 z; // This takes 8 bytes
    }
    
    struct MultiSlot {
        uint256 a; // This takes 32 bytes
        uint256 b; // This takes 32 bytes
        uint256 c; // This takes 32 bytes
    }
    
    // slot 0
    SingleSlot public singleSlot1  = SingleSlot({x:1, y:2, z:3});
    
    // slot 1, 2, 3
    MultiSlot public multiSlot = MultiSlot({a:1, b:2, c:3});

    // slot 4
    SingleSlot public singleSlot2 = SingleSlot({x: 4, y: 5, z: 6});
    
    function getSlotOfSingleSlot1() public pure returns (uint x) {
        assembly {
            x := singleSlot1.slot
        }
    }
    
    function getSlotOfMultiSlot() public pure returns (uint x) {
        assembly {
            x := multiSlot.slot
        }
    }
    
    function getSlotOfSingleSlot2() public pure returns (uint x) {
        assembly {
            x := singleSlot2.slot
        }
    }
}
```

## Constants and Immutable Variables
- Constants are stored in the code section of the contract and are not stored in the EVM storage.
- Immutable variables are stored in the EVM storage and can be assigned a value only once.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ConstantsAndImmutable {
    // slot 0
    uint256 public s0 = 1;
    uint256 constant public a = 2;
    uint256 immutable public b = 3;
    
    // slot 1
    uint256 public s1 = 4;
    
    function getSlots() public view returns (uint x, uint y) {
        assembly {
            x := sload(0) // returns 1
            y := sload(1) // returns 4
        }
    }
}
```

## Fixed-size Arrays
- Fixed-size arrays with elements of size less than 32 bytes are packed together in a single slot.
- Fixed-size arrays with elements of size greater than 32 bytes are stored in separate slots.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract FixedSizeArrays {

    uint64[3] public a = [1, 2, 3]; // each element takes 8 bytes - [slot 0, slot0, slot 0]


    uint64[5] public b = [4, 5, 6, 7, 8]; // each element takes 8 bytes - [slot 1, slot1, slot 1, slot 1, slot 2]


    uint256[3] public c = [7, 8, 9];  // [slot 3, slot 4, slot 5]


    uint128[2] public d = [10, 11]; // [slot 6, slot 6]

    function getSlot0() public view returns (uint x) {
        assembly {
            x := sload(0)
        }
    }

    function getSlot1() public view returns (uint x) {
        assembly {
            x := sload(1)
        }
    }

    function getSlot2() public view returns (uint x) {
        assembly {
            x := sload(2)
        }
    }

    function getSlot3() public view returns (uint x) {
        assembly {
            x := sload(3)
        }
    }

    function getSlot4() public view returns (uint x) {
        assembly {
            x := sload(4)
        }
    }

    function getSlot5() public view returns (uint x) {
        assembly {
            x := sload(5)
        }
    }

    function getSlot6() public view returns (uint x) {
        assembly {
            x := sload(6)
        }
    }

}
```
### Array `a` (uint64[3])
- **Data Type**: `uint64` occupies 8 bytes.
- **Array Size**: 3 elements.
- **Storage**: Since a `uint64` is 8 bytes, it's possible to fit all three elements of this array within a single 32-byte slot because \(3 \times 8 = 24\) bytes is less than 32 bytes.
- **Slots Used**: All elements of `a` are stored in **slot 0**.

### Array `b` (uint64[5])
- **Data Type**: `uint64` occupies 8 bytes.
- **Array Size**: 5 elements.
- **Storage**: Five `uint64` elements require \(5 \times 8 = 40\) bytes, which exceeds the size of a single slot. The first four elements will fit within one slot (32 bytes), and the fifth element will spill over into the next slot.
- **Slots Used**: The first four elements of `b` are stored in **slot 1**, and the fifth element is stored in **slot 2**.

### Array `c` (uint256[3])
- **Data Type**: `uint256` occupies 32 bytes.
- **Array Size**: 3 elements.
- **Storage**: Each `uint256` takes up a full slot.
- **Slots Used**: Each element of `c` occupies a separate slot due to their size. The elements are stored in **slot 3**, **slot 4**, and **slot 5** respectively.

### Array `d` (uint128[2])
- **Data Type**: `uint128` occupies 16 bytes.
- **Array Size**: 2 elements.
- **Storage**: Two `uint128` elements occupy \(2 \times 16 = 32\) bytes exactly, fitting perfectly within a single slot.
- **Slots Used**: Both elements of `d` are stored in **slot 6**.

### Summary of Slot Allocation:
- **Array `a`**: slot 0
- **Array `b`**: slots 1 and 2
- **Array `c`**: slots 3, 4, and 5
- **Array `d`**: slot 6

## Dynamic Arrays
- Dynamic arrays are stored in a separate storage area. The slot assigned to a dynamic array is a hash of the key 
and the slot where the value is stored.

## Mappings
- Mappings are stored in a separate storage area. The slot assigned to a mapping is a hash of the key and the slot 
where the value is stored.
