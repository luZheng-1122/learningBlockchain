# HyperledgerBurrow

* https://github.com/hyperledger/burrow
* https://medium.com/coinmonks/hyperledger-burrow-v0-19-0-633a143a5047
* https://www.hyperledger.org/blog/2018/07/25/3-things-ethereum-users-should-know-about-hyperledger-burrow

## Introduction

### validator

## Setup Env

* https://github.com/hyperledger/burrow

### Go

* [go workspace introduction](https://medium.com/rungo/working-in-go-workspace-3b0576e0534a)

There are two kinds of directory for `Go`:

1. Installation Directory: When you install Go on your system, it creates a directory /usr/local/go in UNIX. Then it copies all necessary code and binaries needed for Go to function in this directory. This is where Go’s command line tools, standard library and compiler lives. Whenever you import a package from Go’s standard library, Go looks for package in this directory. By default, Go assumes your installation directory is under /usr/local/go, so the variable `$GOROOT=/usr/local/go`. If you don't want to use the default directory, you can customise it to another one. Only in this case, you need to change GOROOT to `$GOROOT=/usr/local/custom-go`.
2. Workspace Directory: This is where the Go projects live and you can create the workspace anywhere. `GOPATH` is the path to Go workspace directory and Go looks for packages in `$GOPATH/src`. `$GOPATH` by default points to `$HOME/go` directory in UNIX.

environment variables

```bash
export GOPATH=$HOME/go //$HOME=/Users/luzheng
export GOBIN=$GOPATH/bin

export PATH=$PATH:$GOPATH:$GOBIN
```

* Burrow needs specific go version, currently is 1.11.x (Mar/2019). Upgrading your Go: https://gist.github.com/nikhita/432436d570b89cab172dcf2894465753

### Install Solidity

install `solc` is different from install `solcjs` with `npm`. 
You can install `solc` with 'brew`:

``` bash
brew install https://raw.githubusercontent.com/ethereum/homebrew-ethereum/master/solidity@4.rb
```

source from: https://medium.com/@zulhhandyplast/how-to-install-solidity-0-4-x-on-mac-using-homebrew-8dfadb244f5d

### Setup Burrow server

1. go get

```
go get github.com/hyperledger/burrow
cd $GOPATH/src/github.com/hyperledger/burrow
```
This will pull this repository under $GOPATH/src/github.com/hyperledger/burrow

2. build

```
make build

// if you meet error when running the above command, run this instead:
export PATH=$PATH:$HOME/go/bin/burrow

cd $HOME/go && mkdir bin && cd bin && mkdir burrow
cd $HOME/go/src/github.com/hyperledger/burrow
make install_burrow
```
source: https://github.com/hyperledger/burrow/issues/1041#issuecomment-468054496
This will generate the `burrow` binary under /bin directory.

3. Set up a single full account node
```
burrow spec -p1 -f1 | burrow configure -s- > burrow.toml
```
This will generate `burrow.toml` file.

4. Run burrow
Once the `burrow.toml` has been created, we run:
```
# To select our validator address by index in the GenesisDoc
burrow start --validator-index=0
# Or to select based on address directly (substituting the example address below with your validator's):
burrow start --validator-address=BE584820DC904A55449D7EB0C97607B40224B96E
```
and the logs will start streaming through.

If you would like to reset your node, you can just delete its working directory with rm -rf .burrow. In the context of a multi-node chain it will resync with peers, otherwise it will restart from height 0.

Log:
```
{"caller":"app.go:360","component":"ABCI_App","height":6051,"log_channel":"Info","message":"Committed block","node_info":"Burrow_0.24.6_BurrowChain_FAB3C1-A9C536_ValidatorID:18E0FDB1CBFFDF3107D64120F53FB8CA23ACBFFD","run_id":"50fe1de6-4f5e-11e9-8314-d110c9372e1c","scope":"abci.NewApp","time":"2019-03-26T00:54:05.773656Z"}
```

5. Deploy contracts
* `deploy.yaml`: used to deploy contracts and communicate with Burrwo chain.
* `storage.sol`: smart contracts.
Both files (deploy.yaml & storage.sol) should be in the same directory with no other yaml or sol files.
From inside that directory, we are ready to deploy.
```
burrow deploy --address F71831847564B7008AD30DD56336D9C42787CF63
```
where you should replace the --address field with the ValidatorAddress at the top of your burrow.toml.

## Communicate with Burrow chain via Burrow.js

[Burrow.js](https://github.com/monax/bosmarmot/tree/develop/burrow.js)

### Connect with Burrow chain

``` Javascript
const monax = require('@monax/burrow');
var burrowURL = "<IP address>:<PORT>"; // localhost:10997 if running locally on default port
var account = 'ABCDEF01234567890123'; // address of the account to use for signing, hex string representation 
var options = {objectReturn: true};
var burrow = monax.createInstance(burrowURL, account, options);
```

In Burrow an account must already exist in order to be used as a input account (the sender of a transaction). 

### Create account and send funds

#### create key

``` bash
# Create a new key against the key store of a locally running Burrow (or burrow keys standalone server):
$ address=$(burrow keys gen -n --name NewKey)

# The address will be printed to stdout so the above captures it in $address, you can also list named keys:
$ burrow keys list
Address:"6075EADD0C7A33EE6153F3FA1B21E4D80045FCE2" KeyName:"NewKey"
```

#### create account and send funds

``` Javascript
// Using account and burrow defined in snippet from [Usage](#usage)

// Address we want to create
var addressToCreate = "6075EADD0C7A33EE6153F3FA1B21E4D80045FCE2"

// The amount we send is arbitrary
var amount = 20

burrow.transact.SendTxSync(
    {
        Inputs: [{
            Address: Buffer.from(account, 'hex'),
            Amount: amount
        }],
        Outputs: [{
            Address: Buffer.from(addressToCreate, 'hex'),
            Amount: amount
        }]
    })
    .then(txe => console.log(txe))
    .catch(err => console.error(err))
```

#### call contract function

``` Javascript
const success = await burrow.transferItem(userId_new, itemToken)
console.log("TCL: success", success.raw[0])
```

## Deploying Ethereum Smart Contract on Hyperledger Fabric Blockchain
* https://medium.com/coinmonks/ethereum-and-fabric-crossover-deploying-ethereum-smart-contract-on-hyperledger-fabric-blockchain-fe937e2e3680
* https://github.com/christo4ferris/votevm
* https://github.com/hyperledger/fabric-chaincode-evm: This is the project for the Hyperledger Fabric chaincode, integrating the Burrow EVM. At its essence, this project enables one to use the Hyperledger Fabric permissioned blockchain platform to interact with Ethereum smart contracts written in an EVM compatible language such as Solidity or Vyper.

## Advantage and Disadvantage

## Application and use case