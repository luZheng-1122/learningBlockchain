# HyperledgerBurrow

* https://github.com/hyperledger/burrow
* https://medium.com/coinmonks/hyperledger-burrow-v0-19-0-633a143a5047
* https://www.hyperledger.org/blog/2018/07/25/3-things-ethereum-users-should-know-about-hyperledger-burrow

## Introduction

## Setup Env
* https://github.com/hyperledger/burrow

### Go
* https://medium.com/rungo/working-in-go-workspace-3b0576e0534a

There are two kinds of directory for `Go`:
1. Installation Directory: When you install Go on your system, it creates a directory /usr/local/go in UNIX. Then it copies all necessary code and binaries needed for Go to function in this directory. This is where Go’s command line tools, standard library and compiler lives. Whenever you import a package from Go’s standard library, Go looks for package in this directory. By default, Go assumes your installation directory is under /usr/local/go, so the variable `$GOROOT=/usr/local/go`. If you don't want to use the default directory, you can customise it to another one. Only in this case, you need to change GOROOT to `$GOROOT=/usr/local/custom-go`.
2. Workspace Directory: This is where the Go projects live and you can create the workspace anywhere. `GOPATH` is the path to Go workspace directory and Go looks for packages in `$GOPATH/src`. `$GOPATH` by default points to `$HOME/go` directory in UNIX.

environment variables
```
export GOPATH=$HOME/go //$HOME=/Users/luzheng
export GOBIN=$GOPATH/bin

export PATH=$PATH:$GOPATH:$GOBIN
```

* Burrow needs specific go version, currently is 1.11.x (Mar/2019). Upgrading your Go: https://gist.github.com/nikhita/432436d570b89cab172dcf2894465753

### Setup
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

## Advantage and Disadvantage

## Application and use case