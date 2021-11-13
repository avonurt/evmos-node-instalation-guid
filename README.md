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

`evmosd init $NODE_NAME --chain-id $CHAIN_ID`

Copy `genesis.json` file to the `~/.evmosd/config/genesis.json`

`curl https://raw.githubusercontent.com/tharsis/testnets/main/arsia_mons/genesis.json > ~/.evmosd/config/genesis.json`

Then verify the correctness of the genesis configuration file:

`evmosd validate-genesis`

You should see following output `File at /root/.evmosd/config/genesis.json is a valid genesis file`

Add Seed Nodes

Open following file `$HOME/.evmosd/config/config.toml` and find following configuration `seeds = "<node-id>@<ip>:<p2p port>"`

You should populate `seed` with the value presented here https://github.com/tharsis/testnets/blob/main/arsia_mons/seeds.txt

Here is an example how it should look like 

`seeds = "c36cec90ded95d162b85f8ecd00ecd7c8849ca75@arsiamons.seed.evmos.org:26656"`


## Step 4 - creat Evmos key

Creat evmos `testnet-key` key and request some tokens using instruction from here https://evmos.dev/testnet/faucet.html.

We will use `testnet-key` to setup node

## Step 5 - Run a Testnet Validator

Befor creating a validator you need to sync to the network. We are going to use `tmux` as an example to run a node in separate session

`tmux new -s evmos`

`evmosd start`


When node is synced create validator:

```
evmosd tx staking create-validator \
  --amount=1000000000000aphoton \
  --pubkey=$(evmosd tendermint show-validator) \
  --moniker=$NODE_NAME \
  --chain-id=$CHAIN_ID \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --gas="auto" \
  --gas-prices="0.025aphoton" \
  --from=testnet-key

```

# Run evmosd as a service

create file `evmosd.service` with the following content:

```
echo "[Unit]
Description=Evmos Node
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root
ExecStart=/root/go/bin/evmosd start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/evmosd.service

```

Put file to the following directory: `/etc/systemd/system`

The execute following commands:

`systemctl enable evmosd`

`systemctl start evmosd`

To check logs you could run:

`journalctl -u evmosd -f`




# Additional commands

Check active validators:

`evmosd q staking validators -o json --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '.tokens + " - " + .description.moniker' | sort -gr | nl`

View account info:

`evmosd q bank balances $ACCOUNT`

Unjail command:

```
evmosd tx slashing unjail \
  --from="testnet-key" \
  --chain-id="evmos_9000-1"
  
```

Delegate command:

`evmosd tx staking delegate $ACCOUNT_TO_DELEGATE 1000200270498332004aphoton --gas auto  --from testnet-key`








