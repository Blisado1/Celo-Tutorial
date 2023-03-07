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

Here's a quick overview of the vyper language syntax. [Official documentation](https://docs.vyperlang.org/en/stable/structure-of-a-contract.html)

### Data types

- Boolean -> True or False
    `b: bool`
- int128 -> -2^127 to  2^127 - 1
    `i: int128`
- uint256 -> 0 to 2^256 - 1
    `u: uint256`
- decimals -> -2^127 to  2^127 - 1. It supports up to 10 decimal places
    `d: uint256`
- address
    `a: address`
- bytes32
    `b32: bytes32`
- Bytes
    `bss: Bytes[100]` This means it can contain only 100 items
- String:
    `s: String[100]`  This means it can contain only 100 items
- Struct: This is a group of variables

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

Strings, Bytes and Arrays are set with fixed maximum lengths. To avoid infinite loops and to ensure safety of code.
Vyper also has some inbuilt constants like `ZERO_ADDRESS`, `MAX_UINT256` etc.

For more information on Vyper types check the official documentation link [here](https://docs.vyperlang.org/en/stable/types.html#types)

### Variables

- State Variables are variables that are stored on the blockchain

  - We can specify if a state variable is public by adding the key word `public`

        # public variable
        name: public(uint256)

        # private variable
        password: String[20]

- to access state variables, we use `self.<variableName>`

        self.name

- Enviromental variables in Vyper
  - `self.balance`: returns the contract balance
  - `msg.sender`: returns the sender of a transaction
  - `msg.value`: returns the value attached to a transaction
  - `block.number`: current block number of the transaction
  - `block.timestamp`: timestamp when transaction was made
  - `tx-origin`: address of the original caller

To learn more about variables follow link [here](https://docs.vyperlang.org/en/stable/constants-and-vars.html)

### Functions

Functions in vyper as in solidity can also be called based on their visibility and can also be characterized according to their mutability.

    <mutability>
    <visibility>
    def <functionName>(<type>: <param>) -> (<returnType>):
        # code block

- mutability: This basically means ability to write to the the blockchain. There are four mutability decorators in vyper:

  - `@pure`: does not read from the contract state or any environment variables.
  - `@view`: may read from the contract state, but does not alter it.
  - `nonpayable`: may read from and write to the contract state, but cannot receive Ether. Functions are set to `nonpayable` when no mutability decorator is used.
  - `@payable`: may read from and write to the contract state, and can receive Ether.

- visibility: functions with the `@external` decorator can only be called from transactions and other smart contracts, while the functions with the `@internal`  decorator can only be called from inside this contract.

       @external
        def sum(x: uint256, y: uint256) -> (uint256):
            return x + y

For more information about functions in vypers check link [here](https://docs.vyperlang.org/en/stable/control-structures.html#functions)

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

To learn more about events and other possible uses of events check the vyper doc link [here](https://docs.vyperlang.org/en/stable/event-logging.html)

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

## Writing the Contract in Vyper

### Setting up Environment

Vyper requires the Python 3.6 or higher installed on your machine, you can check if python is installed or not by entering command in your terminal.

```bash
python --version
```

if python is not installed or you don't have the specified version, you can download and install it from [Python's official website](https://www.python.org/downloads/)

From the Vyper documentation, we're advised to install vyper is a virtual environment so as not to allow the vyper dependencies mess with our local machine.

So the first thing to do is to create a new directory `Marketplace`, and in that directory install the virtualenv package from pip using this command

```bash
python -m pip install --user virtualenv
```

Next create the virtualenv

```bash
virtualenv -p python3 venv
```

Activate the virtualenv

```bash
source venv/bin/activate
```

Now install the vyper package

``` bash
(venv) $ pip install vyper
```

You can check if Vyper is installed completely or not by typing the following in your terminal/cmd:

```bash
vyper --version
```

### Contract

Now that you're done setting up, let's move on to the contract. We're going to be rewriting the [Celo 101 solidity contract](https://github.com/dacadeorg/celo-development-101/blob/main/code/celo101-code-chapter_2/2-7-transactions-and-erc20-interface/marketplace.sol) which is in solidity to vyper.

To get started, create a file Marketplace.vy in the directory. Vyper files are created with the `.vy` extension.

Vyper supports a version pragma to ensure that a contract is only compiled by the intended compiler version, or range of versions. Version strings use NPM style syntax.

Now let's set the first basic parameters of the contract

```vyper
#@ version ^0.2.0
```

This is pragma representation in vyper that ensures that  that a contract is only compiled by the intended compiler version, or range of versions. The style used by vyper is the NPM style syntax.

In Vyper there is no need to name the contract as Vyper contracts are contained within files. Each file contains exactly one contract. So let's move on to define the variables storing states of the contract.

```vyper
#@ version ^0.2.0

productsLength: uint256

struct Product:
    owner: address
    name: String[100]
    image: String[100]
    description: String[1000]
    location: String[100]
    price: uint256
    sold: uint256

products: HashMap[uint256, Product]

```

The first variable is the productsLength which stores the total number of products. The next one is the struct product containing the owner address, name of the product, the image, description, location, the price and the number of times the product has been sold. If you can recall we said Vyper does not allow unbounded types, so the String types are set with fixed maximum lengths. The last state variable is a HashMap mapping the product index to the product struct. Allowing you to access the struct through that index.

Another thing to note is that when defining the state variables we didn't specify the visibility decorator, which means the variables are all private.

Next we are going to define the read and write functions of the contract:

```vyper
#@ version ^0.2.0

productsLength: uint256

struct Product:
    owner: address
    name: String[100]
    image: String[100]
    description: String[1000]
    location: String[100]
    price: uint256
    sold: uint256

products: HashMap[uint256, Product]

# writeProduct function

# readProduct function

# buyProduct function

# getProductsLength function

```

Let's define the `writeProduct` function

```vyper
@external
def writeProduct(_name: String[100], _image: String[100], _description: String[1000], _location: String[100], _price: uint256):
    _sold: uint256 = 0

    _productLength: uint256 = self.productsLength

    self.products[_productLength] = Product({
        owner: msg.sender,
        name: _name,
        image: _image,
        description: _description,
        location: _location,
        price: _price,
        sold: _sold
    })
    self.productsLength += 1
```

From the vyper syntax overview we should be able to understand how this function is defined. Let's go over it again.

- First the function is defined with the `@external` visibility decorator i.e. can be called from transactions and other smartcontracts but no mutability decorator which means it's a non-payable function i.e. **allows read and write but does not receive ether**.
- Next it has 6 parameters being passed into the function. No return value so it returns void.
- Inside the function, a uint256 variable is initialized as 0, and the `productslength` state variable is accessed using `self`. Next a new entry is created in the products mapping also accessed using `self`. and lastly the productslength is updated.

Let's define the `readProduct` function

```vyper
@view
@external
def readProduct(_index: uint256)->(address, String[100], String[100], String[1000], String[100], uint256, uint256):
    return(
        self.products[_index].owner,
        self.products[_index].name,
        self.products[_index].image,
        self.products[_index].description,
        self.products[_index].location,
        self.products[_index].price,
        self. products[_index].sold
    )
```

- The `readProduct` function is a read only function as it is defined with the `@view` mutability decorator, it also has the `@external` visibility decorator.
- The function takes in the index which is used to query the mapping and returns the product values.

Let's define the `buyProduct` function

```vyper
@payable
@external
def buyProduct(_index: uint256):
    assert self.products[_index].price == msg.value, "Invalid amount"
    
    send(self.products[_index].owner, self.products[_index].price)
    # (bool success, ) = payable(owner).call{value: msg.value}("");
    self.products[_index].sold += 1
```

- The `buyProduct` function is defined with the `@payable` mutability decorator and also has the `@external` visibility decorator this basically means it can receive ether or in our case CELO.
- Inside the function, a check is run to see if the amount sent corresponds with the price of the item, and I opted for `assert` but you can try to run it with the `raise` syntax as specified in the overview. It'll work just the same. Next the inbuilt function send is used to send the payment to the product owner, and lastly the products sold index is updated.

Let's define the `getProductsLength` function

```vyper
@view
@external
def getProductsLength()->(uint256):
    return self.productsLength

```

This function is a simple view function that returns the total number of products existing on the contract.

Now the final contract `marketplace.vy` looks like this

```vyper
#@ version ^0.2.0

productsLength: uint256

struct Product:
    owner: address
    name: String[100]
    image: String[100]
    description: String[1000]
    location: String[100]
    price: uint256
    sold: uint256

products: HashMap[uint256, Product]

@external
def writeProduct(_name: String[100], _image: String[100], _description: String[1000], _location: String[100], _price: uint256):
    _sold: uint256 = 0

    _productLength: uint256 = self.productsLength

    self.products[_productLength] = Product({
        owner: msg.sender,
        name: _name,
        image: _image,
        description: _description,
        location: _location,
        price: _price,
        sold: _sold
    })
    self.productsLength += 1

@view
@external
def readProduct(_index: uint256)->(address, String[100], String[100], String[1000], String[100], uint256, uint256):
    return(
        self.products[_index].owner,
        self.products[_index].name,
        self.products[_index].image,
        self.products[_index].description,
        self.products[_index].location,
        self.products[_index].price,
        self. products[_index].sold
    )

@payable
@external
def buyProduct(_index: uint256):
    assert self.products[_index].price == msg.value, "Invalid amount"
    
    send(self.products[_index].owner, self.products[_index].price)
    # (bool success, ) = payable(owner).call{value: msg.value}("");
    self.products[_index].sold += 1

@view
@external
def getProductsLength()->(uint256):
    return self.productsLength
```

Great you've written you first contract in Vyper. Cheers 🎉.

## Compiling And Deploying the Contract

Compiling the contract is pretty straightforward once you have successfully installed vyper on your machine. All you just need to do is to run the command `vyper <pathToFile>`. The output you get is the bytecode of the contract.

![bycode](bytcode.png)

Now let's go to [remix](remix.ethereum.org), create a new file, name it `marketplace.vy` and open it.

Copy the code of your marketplace.vy from your machine and paste it inside the file on remix.

Install the Vyper remix plugin, this allows us to compile the vyper contract, it outputs the bytecode as well as the abi for the smart contract.

![compile](assets/compile.gif)

Now you might wonder, why do I have to install vyper on my machine when all it does is return the bytecode, while remix can give me both the abi and the bytecode?

1. The Vyper plugin on remix does not show a proper representation of the error messages if the code fails to compile unlike the local compiler on your machine.
2. There have been cases of downtime for the remix compiler.
3. Even remix warns us from using the remote compiler for production purposes, it clearly states it should be for testing purposes.

![warning](warning.png)

Instead Remix suggests that we use our local compiler to compile our contracts instead, so we can still link our local machine to remix, meaning we can compile the contract with the version of vyper that exists on our compiler. This part would be the last bit of our tutorial. Think of it as the "freebie" for following the tutorial up to this point.

So lets deploy to the celo alfajores network using the compiled data from the remote compiler, as this is only for "testing purposes".

![deploy](assets/deploy.gif)

### Testing SmartContract

- Testing the `writeProduct` function

![write](assets/writeProduct.gif)

- Testing the `readProduct` function

![read](assets/readProduct.gif)

- Testing the `buyProduct` function

![buy](assets/buyProduct.gif)

## (FREEBIE) Setting up the local compiler for remix

**WIP**
