# This template is used for setting up plutus

# This part can be skipped if already installed

## 1 Install Git
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git

## 2 Install cUrl
sudo apt install curl 
curl --version

## 2 Install GHC Compiler
curl -- proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh 
### Logout and login to run further commands
ghc --version

## 3 Install NMV, NODE and NPM
sudo apt-get install build-essential libssl-dev
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
### Logout and login to run further commands
nvm --version
nvm install v12.22.4
nvm use v12.22.4
node --version
npm --version

## 4 Install NixOS
curl -L https://nixos.org/nix/install | sh
source ~/.profile
###  enable IOHK's binary cache
sudo mkdir -p /etc/nix


cat <<EOF | sudo tee /etc/nix/nix.conf

substituters = https://hydra.iohk.io https://cache.nixos.org/

trusted-substituters =

trusted-public-keys = hydra.iohk.io:f/Ea+s+dFdN+3Y/G+FDgSq+a5NEWhJGzdjvKNGv0/EQ= cache.nixos.org-1:6NCHdD59X431o0gWypbMrAURkbJ16ZPMQFGspcDShjY=

EOF

## 5 Download the Plutus Pioneer source code
mkdir ~/plutus
cd ~/plutus

git clone https://github.com/input-output-hk/plutus-pioneer-program.git
cd plutus-pioneer-program/code/week01

###  Open the plutus-pioneer-program/code/week0/cabal.project and take a look at the tag for the plutus-apps.git (currently 41149926c108c71831cfe8d244c83b0ee4bf5c8a)
cd ~/plutus
git clone https://github.com/input-output-hk/plutus-apps.git

cd plutus-apps/

git checkout 41149926c108c71831cfe8d244c83b0ee4bf5c8a
### this can change based upon current version


### run nix shell (it will take a long time to compile)
nix-shell

### after this, inside the nix-shell instance itself, navigate into the plutus-pioneer-program/code/week01 folder and then run nix-shell

cd ~/plutus/plutus-pioneer-program/code/week01
cabal build

### this will take some time
### If the error " error: getting status of '/home/user/plutus/default.nix' no such file or directory" happens then browse to the plutus folder and run nix-shell


## 6 Build plutus documentation
### Navigate to the plutus-apps folder and open another nix-shell build and serve docs
### this will build the plutus documentations. Once it is running open up your browser and navigate to 
 
http://0.0.0.0:8002/haddock

## 7 Running the plutus-playground simulator

### In one terminal
cd plutus-playground-client 
plutus-playground-server
### In another terminal
npm run start

### if compilation fails you might have to run 
npm audit fix

### Open plutus Playground server on your browser
https://localhost:8009




