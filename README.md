# Tangle-Network

![image](https://github.com/molla202/Tangle-Network/assets/91562185/65a58a93-a5a4-4507-b789-7adeee085311)



### Update
```
apt update && apt upgrade -y
```
### Gereklilikler
```
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
```
### Dosyaları çekiyoruz
```
mkdir -p $HOME/.tangle && cd $HOME/.tangle
wget -O tangle https://github.com/webb-tools/tangle/releases/download/v0.4.7/tangle-standalone-linux-amd64
chmod 744 tangle
mv tangle /usr/bin/
tangle --version
```

### Ağ dosyasını çekiyoruz
```
wget -O $HOME/.tangle/tangle-standalone.json "https://raw.githubusercontent.com/webb-tools/tangle/main/chainspecs/testnet/tangle-standalone.json"
chmod 744 ~/.tangle/tangle-standalone.json
```

### Servis oluşturuyoruz
Not: yourname=<name> kısmında <name> değiştiriyoruz adımızı yazıyoruz.
```
yourname=<name>
```
```
tee /etc/systemd/system/tangle.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/bin/tangle \
  --base-path $HOME/.tangle/data/ \
  --name '$yourname' \
  --chain $HOME/.tangle/tangle-standalone.json \
  --node-key-file "$HOME/.tangle/node-key" \
  --port 30333 \
  --rpc-port 9933 \
  --prometheus-port 9615 \
  --auto-insert-keys \
  --validator \
  --telemetry-url "wss://telemetry.polkadot.io/submit 0" \
  --no-mdns
[Install]
WantedBy=multi-user.target
EOF
```
### Servisi başlatıryoruz
```
systemctl daemon-reload
systemctl enable tangle
systemctl restart tangle && journalctl -u tangle -f -o cat
```
### Log komutu
```
journalctl -u tangle -f -o cat
```
