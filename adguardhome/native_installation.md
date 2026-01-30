##### Установка
```bash
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v
```
##### Удаление
```bash
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v -u
```
##### Проверка 53 порта
```bash
sudo lsof -i :53
```
##### Настройка DNS в Systemd-resolved 
```bash
sudo mkdir -p /etc/systemd/resolved.conf.d
sudo nano /etc/systemd/resolved.conf.d/adguardhome.conf

[Resolve]
DNS=127.0.0.1
DNSStubListener=no

sudo mv /etc/resolv.conf /etc/resolv.conf.backup

sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

sudo systemctl restart systemd-resolved
```
##### Изменение потра веб-панели
```bash
sudo nano /opt/AdGuardHome/AdGuardHome.yaml

'http.address'
#для прослушивания на всех сетевых интерфейсах
0.0.0.0:8081
#для прослушивания только локального loopback-интерфейса
127.0.0.1:8081
```
