# The setup was completed thanks to user feamcor, thanks again to him.

https://gist.github.com/feamcor/4f88f23e1fa2521679428558f7603af3

I'm copying it here in case it is lost


# Steps for setup of Cardano Node and Cardano CLI
> This is a summary of https://iohk.zendesk.com/hc/en-us/articles/900001951646-Building-a-node-from-source
```bash
sudo apt-get update -y
sudo apt-get install automake build-essential curl pkg-config libffi-dev libgmp-dev libssl-dev libtinfo-dev libsystemd-dev zlib1g-dev make g++ tmux git jq wget libncursesw5 libtool autoconf
```
## Install GHCup
```bash
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```
> Use `ghcup tui` to select the recommended version of all components.  
> For the compiler (i.e. `ghc`) use version 8 (e.g. `8.10.7`) instead of 9.  
## Let's build...
```bash
mkdir ~/cardano-src
cd ~/cardano-src
```
### libsodium
```bash
git clone https://github.com/input-output-hk/libsodium 
cd libsodium 
git checkout 66f017f1 
./autogen.sh
./configure 
make 
sudo make install
```
> Append these two exports at the end of your `~/.bashrc` file.  
```bash
export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH"
```
> Logout and login again.  
### Cardano Node
```bash
cd ~/cardano-src
git clone https://github.com/input-output-hk/cardano-node.git
cd cardano-node

git fetch --all --recurse-submodules --tags
### From the list provided by the command above,
### Select the latest stable (e.g. 1.34.1 - don't use release candidate versions) version (tag).
git checkout tags/1.34.1

### Set the version of the `ghc` compiler that was installed by `ghcup`.
cabal configure --with-compiler=ghc-8.10.7

### Enable the built libsodium as a project dependency.
echo "package cardano-crypto-praos" >> cabal.project.local
echo "  flags: -external-libsodium-vrf" >> cabal.project.local

cabal build all
```
## Enable tools to run from anywhere
```bash
mkdir -p ~/.local/bin
cp -p "$(./scripts/bin-path.sh cardano-node)" ~/.local/bin
cp -p "$(./scripts/bin-path.sh cardano-cli)" ~/.local/bin
cardano-cli --version
cardano-node --version
```
> Versions should be equal to the version/tag (e.g. 1.35.2) that was built previously.  
## Run node on testnet
> Append these exports at the end of your `~/.bashrc` file.  
```bash
export CARDANO_HOME="${HOME}/cardano-src/cardano-node"
export CARDANO_NODE_SOCKET_PATH="${CARDANO_HOME}/db-test/node.socket"
export CARDANO_TESTNET='--testnet-magic=1097911063'
```
> Logout and login again.  
```bash
cd ~/cardano-src/cardano-node
cardano-node run --topology ./configuration/cardano/testnet-topology.json --database-path ./db-test --socket-path ./db-test/node.socket --port 3001 --config ./configuration/cardano/testnet-config.json
```
> Sync with testnet will take hours!  
> Use `ctrl-c` to stop it.  
> Next time you run this command, it will continue syncing, from where you left before.  
## To check the progress of the sync process
> While sync is running, run this command on another terminal window.  
```bash
cardano-cli query tip --testnet-magic 1097911063
```
> It returns a JSON object.  
> Pay attention to the `syncProgress` attribute.  
> It indicates the percentage of the synchronization process.  
> Only when `100.00` it means that your copy of the testnet blockchain is up-to-date.  
## Some aliases to save time
> Append these two exports at the end of your `~/.bash_aliases` file (check that `.bashrc` loads it).  
```bash
alias cardanorun='cardano-node run --topology ${HOME}/cardano-src/cardano-node/configuration/cardano/testnet-topology.json --database-path ${HOME}/cardano-src/cardano-node/db-test --socket-path ${HOME}/cardano-src/cardano-node/db-test/node.socket --port 3001 --config ${HOME}/cardano-src/cardano-node/configuration/cardano/testnet-config.json'
alias cardanotip='cardano-cli query tip --testnet-magic 1097911063'
```
> Append these two bash-completion commands at the end of your `~/.bashrc` file.
```bash
source <(cardano-node --bash-completion-script `which cardano-node`)
source <(cardano-cli  --bash-completion-script `which cardano-cli`)
```
> Logout and login again.  