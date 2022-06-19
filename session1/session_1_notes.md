# The templates of the notes if copied from mallapurbharat, thanks again for your classes https://github.com/mallapurbharat/cardano-tx-sample/blob/main/exercise1-simpletxfer.md

# Simple Sample - Value Transfer to another address

## Part 1 Create a new set of keys and address 

Set up the node socket path (if required)

    export TESTNET="--testnet-TESTNET 1097911063"


    cardano-cli address key-gen \
    --verification-key-file payment.vkey \
    --signing-key-file payment.skey

    cardano-cli stake-address key-gen \
    --verification-key-file stake1.vkey \
    --signing-key-file stake1.skey

    cardano-cli address build \
    --payment-verification-key-file payment.vkey \
    --stake-verification-key-file stake1.vkey \
    --out-file payment.addr \
    $TESTNET


Generate necessary keys for the second account

    cardano-cli address key-gen \
    --verification-key-file addr9.vkey \
    --signing-key-file addr9.skey

    cardano-cli stake-address key-gen \
    --verification-key-file stake2.vkey \
    --signing-key-file stake2.skey

    cardano-cli address build \
    --payment-verification-key-file addr9.vkey \
    --stake-verification-key-file stake2.vkey \
    --out-file addr9.addr \
    $TESTNET

Open the file addr9.addr and copy the value    
    
 **Fund the payment address with 1000 Test Ada from the Faucet (https://testnets.cardano.org/en/testnets/cardano/tools/faucet/)**


And check the UTxO for the payment address 
    
    cardano-cli query utxo $TESTNET --address $(cat addr9.addr)

                               TxHash                                 TxIx        Amount
    --------------------------------------------------------------------------------------
   8dc86446448b4db4f2f290a3e116cd5d8e07c4139daed552998dedc43298ba4b     0        1000000000 lovelace + TxOutDatumNone



Build the transaction using `transaction build` (recommended)
    
    UTXO1=8dc86446448b4db4f2f290a3e116cd5d8e07c4139daed552998dedc43298ba4b#0

    cardano-cli transaction build \
    --alonzo-era \
    --tx-in $UTXO1 \
    --tx-out $(cat payment.addr)+25000000000 \
    --change-address $(cat addr9.addr) \
    ${TESTNET} \
    --out-file tx.raw

Sign the transaction

    cardano-cli transaction sign \
    ${TESTNET} \
    --signing-key-file addr9.skey \
    --tx-body-file tx.raw \
    --out-file tx.signed

And submit it to the Testnet

    cardano-cli transaction submit ${TESTNET}  --tx-file tx.signed


Check that the result is what you expect

    cardano-cli query utxo ${TESTNET}  --address $(cat addr9.addr)

                           TxHash                                 TxIx        Amount
--------------------------------------------------------------------------------------
bc9f9db6e610b75463dd18b5728cacbe505c3c1fb9bee48a05bc2d023ec970a0     0        749831815 lovelace + TxOutDatumNone

 cardano-cli query utxo ${TESTNET}  --address $(cat payment.addr)
                            TxHash                                 TxIx        Amount
--------------------------------------------------------------------------------------
bc9f9db6e610b75463dd18b5728cacbe505c3c1fb9bee48a05bc2d023ec970a0     1        250000000 lovelace + TxOutDatumNone