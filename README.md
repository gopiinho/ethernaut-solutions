# Ethernaut Solutions

Solutions for the Ethernaut challenges with their explanations.

## 1. Fallback

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
