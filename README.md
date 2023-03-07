# Writing your first Smart contract in Vyper

## Introduction

In this tutorial we will be learning about the Vyper programming language

- Learn about the vyper syntaxes
- Write a simple smart contract in vyper
- Learn how to compile and deploy vyper contracts

## Tech Stack

We will use the following tools and languages in this tutorial

1. Remix IDE
2. VSCode
3. A web browser
4. Vyper

## Prerequisites

- Basic knowledge of programming with Solidity
- Basic knowledge of using Remix

## What is Vyper?

Vyper is a Pythonic smart contract programming language created in 2017 as a readable and secure alternative to Solidity for writing EVM smart contracts. It was designed to improve upon Solidity, by limiting unsafe practices and enhancing readability; Vyper seeks to optimize the security and auditability of smart contract and also to make it virtually impossible for developers to code misleading programs.

Following are the features of Vyper:

- Bounds and overflow checking: On array accesses and arithmetic.
- Support for signed integers and decimal fixed-point numbers
- Decidability: It is possible to compute a precise upper bound for the gas consumption of any Vyper function call.
- Strong typing
- Small and understandable compiler code
- Limited support for pure functions: Anything marked constant is not allowed to change the state.

Vyper vs Solidity:
In it's aim to provide a much more secure contract writing expere here are some of the Solidity features which Vyper omits:

- Overflow
- Unbounded arrays
- Infinite Loops
- Modifiers
- Inheritance
- Assembly support

## Vyper Language Overview

Here's a quick overview of the vyper language syntax.

### Data types

- Boolean -> True or False
    `b: bool`
- int128 -> -2**127 to  2**127 - 1
    `i: int128`
- uint256 -> 0 to 2 **256 - 1
    `u: uint256`
- decimals -> -2**127 to  2**127 - 1. It supports up to 10 decimal places
    `d: uint256`
- address
    `a: address`
- bytes32
    `b32: bytes32`
- Bytes
    `bss: Bytes[100]` This means it can contain only 100 items
- String
    `s: String[100]`  This means it can contain only 100 items
- Struct

```
struct Person:
    name: String[100]
    age: uint256
```

- Arrays

```
<arrName>: <dataType>[<maxNumberofElements>]
# For example
myNums: uint256[100]
```

- Mapping

```
<mapName>: HashMap[<keyType>, <valueType>]
# For example
myMap: HashMap[address, uint256]
```

- Constants: once set It can never be change

```
<constantName>: Constant(<type>) = <value>
# For example
maxTickets: Constanct(uint256) = 100
```

Vyper also has some inbuilt constants like `ZERO_ADDRESS`, `MAX_UINT256` etc.

### Variables

- State Variables are variables that are stored on the blockchain

  - We can specify if a state variable is public by adding the key word `public`

        # public variable
        name: public(uint256)

        # private variable
        password: String[20]

- to access state variables, we use `self.<variableName>`

        self.name

- Local variables are the ones we can find in functions

- Enviromental variables in Vyper
  - `self.balance`: returns the contract balance
  - `msg.sender`: returns the sender of a transaction
  - `msg.value`: returns the value attached to a transaction
  - `block.number`: current block number of the transaction
  - `block.timestamp`: timestamp when transaction was made
  - `tx-origin`: address of the original caller

### Functions

    <mutability>
    <visibility>
    def <functionName>(<type>: <param>) -> (<returnType>):
        # code block

- mutability: ability to write to the the blockchain -> `@pure, @view, @payable` and if not specified it writes to the blockchain. To learn more about readabilty....

- visibility: who can call the contract -> `@external, @internal`. It does not have the public or private visibility. internal only from inside this contract.

### Constructor

For constructors in vyper we use the `__init__` function to set the intial state of a contract on deploy

    maxNoOfPlayers: public(uint256)

    @external
    def __init__(maxNo: uint256):
        self.maxNoOfPlayers = maxNo

### Events

One common use of events is to send notifications to our user interfaces. Here is the implemenation in vyper

    # for example
    event Transfer:
        sender: indexed(address)
        receiver: indexed(address)
        value: uint256

The indexed keyword allows us to be able to filter out the event by a partiucular value and in this case we can filter by the sender address or the receiver address

- To call events

    ```python
    # Log the sell event.
    log Transfer(msg.sender, to, sell_order)
    ```

To learn more about events and other possible uses of events check the vyper doc link [here]("")

### Error handling

Say we wanted to set a condition for a transaction to be valid in solidity, we'd use the require statement. In vyper we have many ways of handling this action. The most common are `assert` and `raise`

- assert

```
# check if owner is msg.sender
assert self.owner == msg.sender, "!owner"
```

- raise error

```
# check if owner is msg.sender
if self.owner == msg.sender:
    raise "!owner"
```

Both options do the same thing, but raise might be a better choice if the condition you are trying to evaluate is much more complex

To learn more about error handling check the doc [here]("")

## Writing A Marketplace contract in Vyper

