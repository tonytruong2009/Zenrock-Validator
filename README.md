# Zenrock-Validator
Make validator
<div>
<h1 align="left" style="display: flex;"> Zenrock Testnet Full Validator Setup </h1>
</div>
Official documentation:
>- [Full node setup instructions]
Explorer:
>-  
### Hardware Requirements

Recomended Hardware Requirements
- CPUs: 4 core
- RAM: 8 GB
- Storage: 200GB (Nvme)

## Set up your Zenrock node

### Manual installation
Update packages and Install dependencies
~~~bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
~~~

#Install install go, if needed

~~~bash
cd $HOME
VER="1.23.2"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
~~~

#Set Vars

~~~bash
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export ZENROCK_CHAIN_ID="gardia-2"" >> $HOME/.bash_profile
echo "export ZENROCK_PORT="56"" >> $HOME/.bash_profile
source $HOME/.bash_profile
~~~

#Download Binary 

~~~bash
cd $HOME
curl -o zenrockd https://releases.gardia.zenrocklabs.io/zenrockd-latest
chmod +x $HOME/zenrockd
mv $HOME/zenrockd $HOME/go/bin/
~~~

#Config and Init app

~~~bash
zenrockd init $MONIKER --chain-id $ZENROCK_CHAIN_ID
zenrockd config set client chain-id $ZENROCK_CHAIN_ID
zenrockd config set client node tcp://localhost:${ZENROCK_PORT}657
~~~

#Download genesis and addrbook

~~~bash
wget -O $HOME/.zrchain/config/genesis.json https://server-5.itrocket.net/testnet/zenrock/genesis.json
wget -O $HOME/.zrchain/config/addrbook.json  https://server-5.itrocket.net/testnet/zenrock/addrbook.json
~~~

#Set seeds and peers

~~~bash
SEEDS="50ef4dd630025029dde4c8e709878343ba8a27fa@zenrock-testnet-seed.itrocket.net:56656"
PEERS="5458b7a316ab673afc34404e2625f73f0376d9e4@zenrock-testnet-peer.itrocket.net:11656,679e513d8f9734018d6019da66c54e19971ff1c3@65.109.22.211:26656,57367027b921e75c2f33a4f81b7273961e48ada0@65.109.115.100:27363,8eaa264c2b733165b9e9ad580c779461b93abafb@88.99.57.73:56656,c3239230e392195acf86edd6bb4811148af9f399@195.201.168.95:56656,39bf4210b1e47b9df1de0d30a6ab94e18b7c4e9f@[2a03:cfc0:8000:13::b910:277f]:14156,45cf02e0121d5066ad98803150bbd41eae56dd50@84.46.249.183:23656,12f0463250bf004107195ff2c885be9b480e70e2@54.74.239.68:26656,b2fcfb25245b7052b9e9d937ad064b422c9bac67@[2a0e:dc0:2:2f71::1]:14156,45973ef61c8a9595ad73342b40a2125dcff8c409@161.97.130.242:656,7649c8e85c4b146594d4e6fff75f0e39030f5f4f@158.220.118.197:656,a98484ac9cb8235bd6a65cdf7648107e3d14dab4@95.217.74.22:18256,cc0b7cd12a5b3b444af41d1c33f1fc0eda22fe79@89.208.6.139:26656,e1ff342fb55293384a5e92d4bd3bed82ecee4a60@65.108.234.158:26356,593a3852b160aea69dd57ab5b4a24af0c661e484@160.25.233.228:656,4a290d8e4aa113e00a6c563a19f33609004c2d1b@38.242.214.148:18256,fa8356510c907e62eed3c34e13ff26117e4fae6f@65.109.88.19:10056,63014f89cf325d3dc12cc8075c07b5f4ee666d64@54.74.239.68:26656,5d3e4d6fcb0a20bc3d627092d94bc3ab6bf64cea@109.199.109.133:56656,eea2d724e9fe7f3ec5fc61e83f85f021723e0ccc@65.108.121.227:14156,32bd8c11a2419237c1f03e40e8f066b54f913448@167.235.115.23:29556,33b51bee7940de677e26e10fc6d37cab7f5d3372@65.109.79.185:11656,d4a6b6463908bc9c095314f895dc2e3430c6756e@116.202.210.177:56656,53ce8550258ebd7b23dde782919e83c257f7c4e4@207.188.6.109:56656,6a8da9c45fef9941c6e5d87f3159e73dd0122475@162.55.97.180:22656,e11abb866f5c384853297493cb2c5299991ea5cf@14.165.245.33:18256,7f2ca4ef74d48355ab312d93dca92a72c6ce57e7@3.254.209.56:26656,637077d431f618181597706810a65c826524fd74@176.9.120.85:29556,c2c5db24bb7aeb665cbf04c298ca53578043ceed@23.88.0.170:15671,1c7b33ea95d886b057a6521068f669586bc568b0@160.25.233.208:656,a412c87699b151a02d1385cbb68b6df396a81c3f@95.217.196.224:26656,a746910fab628e98402ca4ebecd4036cf02a2851@51.83.237.195:29556"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.zrchain/config/config.toml

~~~

#Set custom ports in app.toml

~~~bash
sed -i.bak -e "s%:1317%:${ZENROCK_PORT}317%g;
s%:8080%:${ZENROCK_PORT}080%g;
s%:9090%:${ZENROCK_PORT}090%g;
s%:9091%:${ZENROCK_PORT}091%g;
s%:8545%:${ZENROCK_PORT}545%g;
s%:8546%:${ZENROCK_PORT}546%g;
s%:6065%:${ZENROCK_PORT}065%g" $HOME/.zrchain/config/app.toml
~~~

~~~bash
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.zrchain/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.zrchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.zrchain/config/app.toml
~~~

#Set custom ports in config.toml file

~~~bash
sed -i.bak -e "s%:26658%:${ZENROCK_PORT}658%g;
s%:26657%:${ZENROCK_PORT}657%g;
s%:6060%:${ZENROCK_PORT}060%g;
s%:26656%:${ZENROCK_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${ZENROCK_PORT}656\"%;
s%:26660%:${ZENROCK_PORT}660%g" $HOME/.zrchain/config/config.toml
~~~

#Set minimum gas price, enable prometheus and disable indexing

~~~bash
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0urock"|g' $HOME/.zrchain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.zrchain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.zrchain/config/config.toml
~~~

# create service file

~~~bash
sudo tee /etc/systemd/system/zenrockd.service > /dev/null <<EOF
[Unit]
Description=Zenrock node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.zrchain
ExecStart=$(which zenrockd) start --home $HOME/.zrchain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
~~~

# Reset and download Snapshot

~~~bash
zenrockd tendermint unsafe-reset-all --home $HOME/.zrchain
if curl -s --head curl https://server-5.itrocket.net/testnet/zenrock/zenrock_2024-11-26_1079280_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://server-5.itrocket.net/testnet/zenrock/zenrock_2024-11-26_1079280_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.zrchain
    else
  echo "no snapshot found"
fi
~~~

# Enable and start service

``` bash
sudo systemctl daemon-reload
sudo systemctl enable zenrockd
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f
```

# Peers, Seeds, Addrbook

```bash
wget -O $HOME/.zrchain/config/addrbook.json https://server-5.itrocket.net/testnet/zenrock/addrbook.json

```

```bash
wget -O $HOME/.zrchain/config/genesis.json https://server-5.itrocket.net/testnet/zenrock/genesis.json

```

```bash
PEERS="400b7677af8494ea58d722a4f40c54de98b2ed22@8.52.201.252:36656,32bd8c11a2419237c1f03e40e8f066b54f913448@167.235.115.23:29556,5458b7a316ab673afc34404e2625f73f0376d9e4@zenrock-testnet-rpc.itrocket.net:11656,b2fcfb25245b7052b9e9d937ad064b422c9bac67@zenrock-testnet-rpc.stakerhouse.com:14156,eea2d724e9fe7f3ec5fc61e83f85f021723e0ccc@65.108.121.227:14156,7f2ca4ef74d48355ab312d93dca92a72c6ce57e7@3.254.209.56:26656,ee7d09ac08dc61548d0e744b23e57436b8c477fc@zenrock.rpc.t.anode.team:26906,fa8356510c907e62eed3c34e13ff26117e4fae6f@65.109.88.19:10056,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@zenrock-testnet.rpc.kjnodes.com:18256,33b51bee7940de677e26e10fc6d37cab7f5d3372@65.109.79.185:11656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.zrchain/config/config.toml

```


###### Update Version V5.3.4 date 25/11/2024

```bash
cd $HOME
wget https://github.com/zenrocklabs/zrchain/releases/download/v5.3.4/zenrockd.zip
unzip zenrockd.zip
rm zenrockd.zip
chmod +x $HOME/zenrockd
sudo mv $HOME/zenrockd $(which zenrockd)
sudo systemctl restart zenrockd && sudo journalctl -u zenrockd -f

```

### Create validator

```bash
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(zenrockd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000urock\",
    \"moniker\": \"test\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love blockchain ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json

```

# Create a validator using the JSON configuration

```bash
zenrockd tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id gardia-2 \
	--fees 30urock

```

###### Key management

```bash
zenrockd keys add $WALLET

```

```bash
zenrockd keys add $WALLET --recover

```

```bash
zenrockd q bank balances $WALLET_ADDRESS 

```

```bash
zenrockd tx staking delegate $(zenrockd keys show $WALLET --bech val -a) 1000000urock --from $WALLET --chain-id gardia-2 --fees 30urock -y 

```

```bash
zenrockd tx staking create-validator \
--amount 1000000urock \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(zenrockd tendermint show-validator) \
--moniker "$MONIKER" \
--identity "" \
--details "I love blockchain ❤️" \
--chain-id gardia-2 \
--fees 30urock \
-y 

```

```bash
zenrockd tx slashing unjail --from $WALLET --chain-id gardia-2 --fees 30urock -y 

```

