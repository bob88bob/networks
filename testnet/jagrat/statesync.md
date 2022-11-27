## Stop Neutron service :
```
systemctl stop neutrond
```
## Clear database:
```
neutrond tendermint unsafe-reset-all --home $HOME/.neutrond
```
## Paste variables for State Sync :
```
PEER="333bbc2c2cf329d702d44861a0a8c8535c42721b@49.12.203.233:26656"
SNAP="http://49.12.203.233:26657"
LATEST_HEIGHT=$(curl -s $SNAP/block | jq -r .result.block.header.height)
TRUST_HEIGHT=$((LATEST_HEIGHT - 1000))
TRUST_HASH=$(curl -s "$SNAP/block?height=$TRUST_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP,$SNAP\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$TRUST_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.neutrond/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEER\"/" $HOME/.neutrond/config/config.toml
```
## Start Neutron service :
```
sudo systemctl start neutrond
```
