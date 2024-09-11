# Ethernaut Solutions

Solutions for [The Ethernaut](https://ethernaut.openzeppelin.com/) CTF challenges with their explanations.

## Contents

0.  [Hello Ethernaut](#00---hello-ethernaut)
1.  [Fallback](#01---fallback)
2.  [Fallout](#02---fallout)

## 00 - Hello Ethernaut

This is the first challenge, and obviously the simplest, call the `info` function and it will show you the further instructions which are basically calling other functions.

## 01 - Fallback

This is a simple challenege where the goal is to change the `owner` of the contract (deployer) to your own address which would give you access to the `withdraw` function.

The vulnerability in this contract arises from the function `contribute` and the `receive`

```javascript
function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }

     receive() external payable {
        require(msg.value > 0 && contributions[msg.sender] > 0);
        owner = msg.sender;
    }
```

So in order to break into this contract we pass in any msg.value to this contract, even 1 wei would work here. This would give ability to use the receive function when we send another msg.value to the contract itself.

Resulting in `owner` updating to the one who sent value to both `contribute` and `receive` function and finally giving access to `withdraw` function to finish the challenge.

## 02 - Fallout

First thing worth noting in this contract is its compiler version is ^0.60, compared to todays ^0.8.0 so there are few differences. Specific to our case here is how `constructor` difference.

In the newer versions of solidity ^0.8.0 we specifically call the `constructor` function execute on contract deployment.

```javascript

pragma solidity ^0.8.0;

contract ConstructorDemo {
    string public name;

    constructor() {
        name = 'I love solidity!';
    }
}
```

As we know upon deployment our string `name` will be set. But in case of older versions we had to name the function as same as the contract for it to be the constructor.

```javascript

pragma solidity ^0.6.0;

contract ConstructorDemo {
    string public name;

    function ConstructorDemo() public {
        name = 'I love solidity!';
    }
}
```

So, in our Fallout contract, the `constructor` function is named incorrectly, `Fal1out`, so in order to complete our objective to change owner of the contract, we just need to call this constructor function and the challenge will be completed.
