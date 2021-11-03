# evmos-node-instalation-guid
Evmos node installation guid

Link to the official documentation https://evmos.dev/testnet/join.html

Minimum Requirements:
- 4 or more physical CPU cores
- At least 500GB of SSD disk storage
- At least 16GB of memory (RAM)
- At least 100mbps network bandwidth

The purpose of this document is to guid you through instalation procees very quickly and to test node setup happy path.

I am using `Ubunty 18.04` for the instalation.

## Step 0 - package updates and tools instalation

`sudo apt update && sudo apt upgrade -y`

`sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux vim`

## Step 1 - install `Go` language 

At the moment of writing this document Evmos is built using Go version 1.17+

download `go` archive  

`wget https://golang.org/dl/go1.17.2.linux-amd64.tar.gz`

install `go` 

`rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.2.linux-amd64.tar.gz`

Add /usr/local/go/bin to the PATH environment variable : 

`echo 'export PATH=$PATH:/usr/local/go/bin' >> $HOME/.profile`

`echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.profile`

`source $HOME/.profile`

check that `go` successfully installed:

`go version`

you should see following output `go version go1.17.2 linux/amd64`

## Step 2 - install `evmos`

`git clone https://github.com/tharsis/evmos.git`

`cd evmos`

checkout to the latest version from here: https://github.com/tharsis/evmos/releases

In this example we will checkout to version `v0.1.3`

`git checkout v0.1.3`

install evmos

`make install`

check that evmos installed

`evmosd version`

you should see following output `0.1.3`


## Step 3 - initialise Node

Configure `chain-id`

`export CHAIN_ID=evmos_9000-1`

`evmosd config chain-id $CHAIN_ID`

Lets create an environment variable with yours node name, in our example it will be `testnode999`, you could choose any name you want

`export NODE_NAME=testnode999`

`evmosd init $NODE_NAME --chain-id evmos_9000-1`

Copy `genesis.json` file to the `~/.evmosd/config/genesis.json`

`curl https://raw.githubusercontent.com/tharsis/testnets/main/arsia_mons/genesis.json > ~/.evmosd/config/genesis.json`

Then verify the correctness of the genesis configuration file:

`evmosd validate-genesis`

You should see following output `File at /root/.evmosd/config/genesis.json is a valid genesis file`

Add Seed Nodes

Open following file `$HOME/.evmosd/config/config.toml` and find following configuration `seeds = "<node-id>@<ip>:<p2p port>"`

You should populate `seed` with the value presented here https://github.com/tharsis/testnets/blob/main/arsia_mons/seeds.txt

Here is an example how it should look like 

`seeds = "c36cec90ded95d162b85f8ecd00ecd7c8849ca75@arsiamons.seed.evmos.org:26656, 3787335176bbb91bf14a67724ebe0f0940ca5afb@evmos-seed.artifact-staking.io:26656, faa31510d9280e74e7f2e767a62023bd5c896c27@evmos-testnet.mercury-nodes.net:29447"`









