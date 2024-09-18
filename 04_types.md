# Reference Types
- Reference types comprise structs, arrays and mappings
- Assignments between storage and memory (or from calldata) always create an independent copy
- Assignments from memory to memory only create references. This means that changes to one memory variable are also
  visible in all other memory variables that refer to the same data.
- Assignments from storage to a local storage variable also only assign a reference
- All other assignments to storage always copy


- `function addAnimal(Animal storage animal) public returns(string memory){` is not possible because: Data location
  must be "memory" or "calldata" for parameter in function, but "storage" was given.`
- `function incrementIdAndAdd(Animal calldata animal) public returns(string memory){` is not possible because: Calldata structs are read-only.
-

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.15;

contract ReferenceTypes {
    struct Animal {
        uint256 id;
        string name;
    }

    event AnimalUdatedByInternal(uint indexed id, string name);
    event AnimalUdatedByPublic(uint indexed id, string name);

    mapping (uint => Animal) public animals;

    Animal public dog;
    Animal public cat;

    // function addAnimal(Animal storage animal) public returns(string memory){ - Data location must be "memory" or "calldata" for parameter in function, but "storage" was given.
    function addAnimal(Animal memory animal) public returns(string memory){
        animals[animal.id] = animal;
        return "Aminal added successfully"; 
    }

    // function incrementIdAndAdd(Animal calldata animal) public returns(string memory){ - This doesn't compile because we modify the animal's id in the function
    function incrementIdAndAdd(Animal memory animal) public returns(string memory){
        animal.id = animal.id + 1; // animal.id += 1;
        addAnimal(animal); 
        return "Aminal added successfully"; 
    }

    function updateAnimalMemory(Animal memory animal) public returns(string memory){ // when passing [2, dog]
        string memory desc = "Updated in public function ";
        animal.name = string.concat(desc, animal.name);
        _updateAnimal(animal);
        // This event is called with [2. Updated in internal function Updated in public function dog] - So the memory passed to a merory function is modified
        emit AnimalUdatedByPublic(animal.id, animal.name); 
        return "Aminal updated successfully"; 
    }

    function updateAnimalCalldata(Animal calldata animal) public returns(string memory){ // when passing [2, cat]
        _updateAnimal(animal);
        // This event is called with [2, cat] - So the original remains the same
        emit AnimalUdatedByPublic(animal.id, animal.name); 
        return "Aminal updated successfully"; 
    }


    function _updateAnimal(Animal memory animal) internal returns(string memory){
        string memory desc = "Updated in internal function ";
        animal.name = string.concat(desc, animal.name);
        emit AnimalUdatedByInternal(animal.id, animal.name); // this is to check if 
        return "Aminal updated successfully"; 
    }
}
```

# Arrays
- Arrays can have a compile-time fixed size, or they can have a dynamic size.
- The type of an array of fixed size k and element type T is written as T[k], and an array of dynamic size as T[].
- It is possible to mark state variable arrays public and have Solidity create a getter. The numeric index becomes a
  required parameter for the getter.
- bytes is similar to byte1[], but it is packed tightly in calldata and memory.
- You should use bytes over bytes1[] because it is cheaper, since using bytes1[] in memory adds 31 padding bytes between the elements
- string is equal to bytes but does not allow length or index access.
- Memory arrays with dynamic length can be created using the new operator. As opposed to storage arrays, it is not
  possible to resize memory arrays (e.g. the .push member functions are not available). You either have to calculate
  the required size in advance or create a new memory array and copy every element.
- In array literals(example [1, a, f(3)]), base type of the array is the type of the first expression on the list such
  that all other expressions can be implicitly converted to it. It is a type error if this is not
  possible. `["dd", 2, "eee"];` produces - Unable to deduce common type for array elements.
- Fixed size memory arrays cannot be assigned to dynamically-sized memory arrays
- Array Members
    - length
    - push() - you can use to append a zero-initialised element at the end of the array
    - push(value) - Dynamic storage arrays and bytes (not string) have a member function called push(x) that you can use to append a given element at the end of the array.
    - pop - Dynamic storage arrays and bytes (not string) have a member function called pop()
- Increasing the length of a storage array by calling push() has constant gas costs because storage is zero-initialised
- while decreasing the length by calling pop() has a cost that depends on the “size” of the element being removed
- In EVM versions before Byzantium, it was not possible to access dynamic arrays returned from function calls.
- access to a non-existing index will throw an exception
- using push and pop is the only way to change the length of an array
- `delete a` clear the arrays completely
- Dynamic memory arrays are created using `new`: `uint[] memory a = new uint[](7);`
- Dangling References to Storage Array Elements
    - A dangling reference can for example occur, if you store a reference to an array element in a local variable and then .pop() from the containing array:

### Array Slices
Array slices are a view on a contiguous portion of an array. They are written as `x[start:end]`, where start and end are
expressions resulting in a `uint256` type (or implicitly convertible to it). The first element of the slice is `x[start]`
and the last element is `x[end - 1]`.

If start is greater than end or if end is greater than the length of the array, an exception is thrown.

Both start and end are optional: start defaults to `0` and end defaults to the length of the array.
```solidity
function forward(bytes calldata payload) external {
        bytes4 sig = bytes4(payload[:4]);
}
```


# Strings
- string is equal to bytes but does not allow length or index access.
- You can also compare two strings by their keccak256-hash using keccak256(abi.encodePacked(s1)) == keccak256(abi.encodePacked(s2)) and concatenate two strings using string.concat(s1, s2).
- you can use string.concat(s1, s2) to concatenate two strings. The function returns a single string memory array that contains the concatenated strings.
-

# Structs
- Struct types can be used inside mappings and arrays and they can themselves contain mappings and arrays.
- It is not possible for a struct to contain a member of its own type, although the struct itself can be the value type
  of a mapping member or it can contain a dynamically-sized array of its type. This restriction is necessary, as the size
  of the struct has to be finite.

# Mapping Types
- Mapping types use the syntax `mapping(KeyType KeyName? => ValueType ValueName?)` and variables of mapping type are
  declared using the syntax `mapping(KeyType KeyName? => ValueType ValueName?)` VariableName
- The KeyType can be any built-in value type, bytes, string, or any contract or enum type. Other user-defined or
  complex types, such as mappings, structs or array types are not allowed.
- You can think of mappings as hash tables, which are virtually initialised such that every possible key exists and is
  mapped to a value whose byte-representation is all zeros, a type’s default value.
- Mappings can only have a data location of storage and thus are allowed for state variables, as storage reference
  types in functions, or as parameters for library functions. They cannot be used as parameters or return parameters of
  contract functions that are publicly visible. These restrictions are also true for arrays and structs that contain mappings.
- You can mark state variables of mapping type as public and Solidity creates a getter for you
- You cannot iterate over mappings, i.e. you cannot enumerate their keys. It is possible, though, to implement a data structure on top of them and iterate over that

# Operators
- Arithmetic operators: +, -, *, /, %, ** (exponentiation)
- In case one of the operands is a literal number it is first converted to its “mobile type”, which is the smallest
  type that can hold the value (unsigned types of the same bit-width are considered “smaller” than the signed types)

# delete
- `delete a` assigns the initial value for the type to a
- It assigns a dynamic array of length zero or a static array of the same length with all elements set to their initial value
- delete a[x] deletes the item at index x of the array and leaves all other elements and the length of the array untouched
- For structs, it assigns a struct with all members reset.
- delete has no effect on mappings
- delete a really behaves like an assignment to a, i.e. it stores a new object in a. This distinction is visible when a
  is reference variable: It will only reset a itself, not the value it referred to previously.

# Conversions between Elementary Types
- In general, an implicit conversion between value-types is possible if it makes sense semantically and no information is lost.
- For example, uint8 is convertible to uint16 and int128 to int256, but int8 is not convertible to uint256, because uint256 cannot hold values such as -1.
- If an operator is applied to different types, the compiler tries to implicitly convert one of the operands to the type 
of the other (the same is true for assignments). This means that operations are always performed in the type of one of the operands.
- In the example below, y and z, the operands of the addition, do not have the same type, but uint8 can be implicitly 
converted to uint16 and not vice-versa. Because of that, y is converted to the type of z before the addition is 
performed in the uint16 type. The resulting type of the expression y + z is uint16. Because it is assigned to a 
variable of type uint32 another implicit conversion is performed after the addition.
```solidity
uint8 y;
uint16 z;
uint32 x = y + z;
```
