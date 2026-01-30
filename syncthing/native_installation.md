### Установка
```bash
apt install syncthing 
```
### Создание пользователя
```bash
useradd -m -s /bin/bash alex
```
### Запуск
```bash
system enable --now syncthing@alex
```
### Конфигурирование
```bash
nano /home/alex/.comfig/syncthing/config.xml

10.0.0.104:8384
```
### Перезапуск
```bash
systemctl restart syncthing@alex
```
