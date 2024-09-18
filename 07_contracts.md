
# Inheritance
- We declare inheritance in Solidity with the "is" keyword - "contract B is A."
- Function that is going to be overridden by a child contract must be declared as `virtual`.
- Function that is going to override a parent function must use the keyword `override`.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

/* Graph of inheritance
    A
   / \
  B   C
 / \ /
F  D,E

*/

contract A {
    function foo() public pure virtual returns (string memory) {
        return "A";
    }
}

// Contracts inherit other contracts by using the keyword 'is'.
contract B is A {
    // Override A.foo()
    function foo() public pure virtual override returns (string memory) {
        return "B";
    }
}

contract C is A {
    // Override A.foo()
    function foo() public pure virtual override returns (string memory) {
        return "C";
    }
}

// Contracts can inherit from multiple parent contracts.
// When a function is called that is defined multiple times in
// different contracts, parent contracts are searched from
// right to left, and in depth-first manner.

contract D is B, C {
    // D.foo() returns "C"
    // since C is the right most parent contract with function foo()
    function foo() public pure override(B, C) returns (string memory) {
        return super.foo();
    }
}

contract E is C, B {
    // E.foo() returns "B"
    // since B is the right most parent contract with function foo()
    function foo() public pure override(C, B) returns (string memory) {
        return super.foo();
    }
}

// Inheritance must be ordered from “most base-like” to “most derived”.
// Swapping the order of A and B will throw a compilation error.
contract F is A, B {
    function foo() public pure override(A, B) returns (string memory) {
        return super.foo();
    }
}

```
- You can call parent contract’s function from the client contract using function superseding. This can be done using the `super` keyword.




- The order of inheritance is important. If you inherit from multiple contracts, the order in which you list them is
  important. The first contract listed is the base contract, and the last contract listed is the most derived contract.
- If a contract inherits from multiple contracts that have functions with the same name, the most derived function is
  called. If you want to call a function from a specific contract, you can use the syntax `C.functionName()`

```solidity
pragma solidity ^0.4.5;

contract C {
  uint u;
  function f() {
    u = 1;
  }
}

contract B is C {
  function f() {
    u = 2;
  }
}

contract A is B {
  function f() {  // will set u to 3
    u = 3;
  }
  function f1() { // will set u to 2
    super.f();
  }
  function f2() { // will set u to 2
    B.f();
  }
  function f3() { // will set u to 1
    C.f();
  }
}
```
- Functions with same name but different parameters are identified by different signatures, so they are actually
  different functions. If `ContractA` defines `doSomething()`, `ContractB` defines `doSomething(string myString)`,
  and `ContractB` inherits from `ContractA`, final `ContractB` will have two valid functions that user can call:
  `doSomething()` and `doSomething(string myString)`. Visibility also is part of the signature, but it can't be overloaded.

- From the outside of the contract, you cannot call a base function that has been overridden. This is because what you
  are calling from the outside is the final contract implementation, no matter how you define the interface on the client to call it.

