# ˜”*°•.˜”*°• Nois Network •°*”˜.•°*”˜



![nois a](https://user-images.githubusercontent.com/108979536/196456662-b2ca218d-e4de-4b62-9ac2-37d94c6a48ed.svg)



# Overview
Nois is a Proof of Stake blockchain protocol that allows developers to use secure, unbiased and cost efficient randomness via IBC
Inside this documentation you will find all the instructions in order to:
Setup a full node and become a Nois validator earning $NOIS while sustaining the chain.
Fix common validators/full-node problems.
Use Nois to bring randomness into your decentralised application.


# Offecial Links of Nois

Twitter : [Link](https://twitter.com/NoisNetwork?t=ifCUTzCzJjzFSqUhg1YF4w&s=33)

Discord : [Link](https://discord.gg/xEvgMqyT)

Docs : [Link](https://docs.nois.network/)


# Hardware Requirements

### Minimum Hardware Requirements

  + 4x CPUs; the faster clock speed the better
  
  + 8GB RAM
    
  + 100GB of storage (SSD or NVME)
    
### Recommended Hardware Requirements

  + 8x CPUs; the faster clock speed the better
  
  + 64GB RAM
    
  + 1TB of storage (SSD or NVME)

### For quick installation use our script (enter your wallet name)

    wget -O nois.sh https://raw.githubusercontent.com/appieasahbie/nois/main/nois.sh && chmod +x nois.sh && ./nois.sh
    
    
### Post installation

    source $HOME/.bash_profile
    
 (Check the status of your node)

    noisd status 2>&1 | jq .SyncInfo
    
 ### open ports and active the firewall
   
     sudo ufw default allow outgoing
     sudo ufw default deny incoming
     sudo ufw allow ssh/tcp
     sudo ufw limit ssh/tcp
     sudo ufw allow ${NOIS_PORT}656,${NOIS_PORT}660/tcp
     sudo ufw enable
     
### Create wallet
 + (Please save all keys on your notepad)
 
     noisd keys add $WALLET
     
 + To recover your old wallet use this command

     noisd keys add $WALLET --recover
     
### Add wallet and valoper address and load variables into the system

     NOIS_WALLET_ADDRESS=$(noisd keys show $WALLET -a)
     NOIS_VALOPER_ADDRESS=$(noisd keys show $WALLET --bech val -a)
     echo 'export NOIS_WALLET_ADDRESS='${NOIS_WALLET_ADDRESS} >> $HOME/.bash_profile
     echo 'export NOIS_VALOPER_ADDRESS='${NOIS_VALOPER_ADDRESS} >> $HOME/.bash_profile
     source $HOME/.bash_profile
     
### Fund your wallet (to create validator)
    
     curl --header "Content-Type: application/json" \
     --request POST \
     --data '{"denom":"unois","address":"'$NOIS_WALLET_ADDRESS'"}' \
     http://faucet.noislabs.com/credit
     
### Create validator

  + (first check your bank balance)
     
      noisd query bank balances $NOIS_WALLET_ADDRESS
      
 (if you recive the tokens let`s create validator)

     noisd tx staking create-validator \
     --amount 100000000unois \
     --from $WALLET \
     --commission-max-change-rate "0.01" \
     --commission-max-rate "0.2" \
     --commission-rate "0.07" \
     --min-self-delegation "1" \
     --pubkey  $(noisd tendermint show-validator) \
     --moniker $NODENAME \
     --chain-id $NOIS_CHAIN_ID
  
### Delegate to yourself
      noisd tx staking delegate $NOIS_VALOPER_ADDRESS 10000000unois --from=$WALLET --chain-id=$NOIS_CHAIN_ID --gas=auto
      
### Redelegate
      noisd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000unois --from=$WALLET --chain-id=$NOIS_CHAIN_ID --gas=auto
       
### Check your validator key
      [[ $(noisd q staking validator $NOIS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(noisd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
      
### list of validators
      noisd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
      
### Edit your Validator
    noisd tx staking edit-validator \
    --moniker=$NODENAME \
    --identity=<your_keybase_id> \
    --website="<your_website>" \
    --details="<your_validator_description>" \
    --chain-id=$NOIS_CHAIN_ID \
    --from=$WALLET
    
### Unjail validator
     noisd tx slashing unjail \
     --broadcast-mode=block \
     --from=$WALLET \
     --chain-id=$NOIS_CHAIN_ID \
     --gas=auto
     
### Delete your node
     sudo systemctl stop noisd
     sudo systemctl disable noisd
     sudo rm /etc/systemd/system/nois* -rf
     sudo rm $(which noisd) -rf
     sudo rm $HOME/.noisd* -rf
     sudo rm $HOME/nois -rf
     sed -i '/NOIS_/d' ~/.bash_profile
     
     
# Service Management

 ### Check logs
     journalctl -fu noisd -o cat
     
### Start service
     sudo systemctl start noisd

### Stop service
     sudo systemctl stop noisd
     
### Restart service
     sudo systemctl restart noisd
     
# Node info

  ### Synchronization info
        noisd status 2>&1 | jq .SyncInfo
        
  ### Validator info
        noisd status 2>&1 | jq .ValidatorInfo
        
  ### Node info
        noisd status 2>&1 | jq .NodeInfo
        
  ### Show node id
        noisd tendermint show-node-id
     
   Enjoy give me star
    
 
   
      
    
      

