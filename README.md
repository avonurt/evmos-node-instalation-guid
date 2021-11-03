# evmos-node-instalation-guid
Evmos node installation guid

Link to the official documentation https://evmos.dev/testnet/join.html

The purpose of this document is to guid you through instalation procees very quickly and to test node setup happy path.

I am using `Ubunty 18.04` for the instalation.

## Step 0 - package updates and tools instalation

`sudo apt update && sudo apt upgrade -y`

`sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux vim`

## Step 1 - install evmosd

### Step 1.1 - install `Go` language 

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

-----TO BE UPDATED------




