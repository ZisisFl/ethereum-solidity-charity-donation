## ethereum-solidity-charity-donation
A simple smart contract that facilitates donations to different charities. When a user
wants to send some funds to a destination address, instead of sending them directly to
that address, they will use the smart contract. A part of the funds will be sent to the
charity the user specified, while the rest will go to the destination address.

## Dependencies
In order to reproduce this project you will need to clone it in your machine and install Node JS and some of its modules.
#### Clone project
```
git clone https://github.com/ZisisFl/ethereum-solidity-charity-donation
```
#### Install Node JS
```sh
install node js
sudo apt install nodejs
sudo apt install npm
nodejs -v
```
#### Install needed node modules
```sh
cd ethereum-solidity-charity-donation
```

Install solcjs solidity compiler
```sh
npm install solc
```

Install development tools
```sh
npm install ganache-cli
npm install truffle
```

For Linux machines it is recommended to add node_modules/.bin to PATH
```sh
export PATH=$PATH:`pwd`/node_modules/.bin
```

## Reproduce project
On a first terminal start ganache
```sh
ganache-cli
```

On a second one initialize truffle
```sh
truffle init
```
Then edit truffle-config.js and navigate to networks and uncomment development  


Connect to ganache network (node repl with web3)
```sh
truffle console
```

Open a third terminal  
Generate binary code of smart contract
```sh
solc --bin CharityDonation.sol
```

Get contract ABI
```sh
solc --abi CharityDonation.sol
```

Get an estimation of the Gas needed
```sh
solc --gas CharityDonation.sol
```

### Deployment
In truffle console (second terminal): 
Set a variable contractBin with binary code in the third terminal
```js
contractBin = '{bin_from_third_terminal}'
```

Do the same for the ABI  
```js
contractAbi = [{abi_from_third_terminal}]
```

Define DonationContract variable using ABI
```js
DonationContract = new web3.eth.Contract(contractAbi);
```

Set contract owner address
```js
web3.eth.getAccounts().then(a => contractOwnerAddress = a[0])
```

Set some sender addresses
```js
web3.eth.getAccounts().then(a => senderAddresses = a.slice(1,3))
```

Set some charity addresses
```js
web3.eth.getAccounts().then(a => charityAddresses = a.slice(3,6))
```

Set some destination addresses
```js
web3.eth.getAccounts().then(a => destinationAddresses = a.slice(6,10))
```

Store current gas price to a variable
```js
web3.eth.getGasPrice().then(a => currentGasPrice = a)
```

Finally deploy contract
```js
DonationContract.deploy({data: contractBin, arguments: [charityAddresses]}).send({from: contractOwnerAddress, gas: 800000, gasPrice:currentGasPrice}).then( (instance) => { console.log(instance.options.address) });
```

Store contract's address in a contractAddress variable
```js
contractAddress = '{contract_address}'
```

### Test contract methods
Init deployed contract
```js
deployedDonationContract = new web3.eth.Contract(contractAbi, contractAddress)
```

Set an amount to send
```js
amountToSend = web3.utils.toWei("4", "ether")
```

Use sendFunds variant a (public)
```js
deployedDonationContract.methods.sendFunds(destinationAddresses[0], 1).send({from: contractOwnerAddress, gasPrice:currentGasPrice, gas: 110000, value:amountToSend})
```

Use sendFunds variant b (public)
```js
deployedDonationContract.methods.sendFunds(destinationAddresses[0], (amountToSend/6).toString(), 1).send({from: contractOwnerAddress, gasPrice:currentGasPrice, gas: 110000, value:amountToSend})
```

Get overall donations info (public)
```js
deployedDonationContract.methods.getOverallDonationInfo().call()
```

Get top donation made (owner only)
```js
deployedDonationContract.methods.getTopDonation().call()
```

Destroy (owner only)
```js
deployedDonationContract.methods.destroyContract().send({from: contractOwnerAddress, gasPrice:currentGasPrice, gas: 30000})
```