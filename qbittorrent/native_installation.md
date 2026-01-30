# Установка
```bash
apt install qbittorrent-nox 
```
# Создание пользователя
```bash
useradd -m -s /bin/bash alex
```
# Запуск
```bash
systemctl enable --now qbittorrent-nox@alex
```
# Создание сервиса
```bash
nano /etc/systemd/system/qbittorrent-nox.service

[Unit]
Description=qBittorrent-nox service for user %I
Documentation=man:qbittorrent-nox(1)
Wants=network-online.target
After=local-fs.target network-online.target nss-lookup.target

[Service]
Type=simple
PrivateTmp=false
User=%i
ExecStart=@EXPAND_BINDIR@/qbittorrent-nox
TimeoutStopSec=1800

[Install]
WantedBy=multi-user.target
```
# Перезапуск Systemd 
```bash
systemctl daemon-reload 
```
