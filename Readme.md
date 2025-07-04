# Скрипт для лёгкой установки и настройки ядра X-ray без графического интерфейса + AdGuard Home DNS + Iptables-Persistent

Вы все знакомы с такими панелями управления, как 3x-ui, Marzban и другими. Все эти панели являются всего лишь графическими надстройками над ядром X-ray и служат для удобного управления им, а также для создания подключений и настроек. Ядро же может работать без всяких панелей и управляться полностью через терминал. Основное преимущество использования «голого» ядра заключается в том, что вам не нужно заморачиваться с доменами и TLS-сертификатами. Само ядро можно установить и администрировать вручную с помощью официальной документации. Этот скрипт предназначен для упрощения этой задачи: он автоматически установит ядро на сервер, создаст конфигурационные файлы и несколько исполняемых файлов для удобного управления пользователями.

## VPS для панели

Для установки панели нам понадобится VPS-сервер. Приобрести его можно в [ishosting](https://bit.ly/3rOqvPE).  
В сервисе доступны более 36 локаций. Если вам не нужна какая-то конкретная страна, выбирайте ту, что ближе к вам.

## Системные требования

- 1 CPU  
- 1 GB RAM  
- 10 GB диска  
- ОС Ubuntu 22 x64 или Ubuntu 24 x64

## Как пользоваться скриптом

Скрипт создавался и тестировался под ОС Ubuntu 22 x64 и Ubuntu 24 x64. На других ОС может работать некорректно. Чтобы скачать и запустить скрипт, используйте эту команду:

```sh
wget -qO- https://raw.githubusercontent.com/ServerTechnologies/simple-xray-core/refs/heads/main/xray-install | bash
```

## Команды для управления пользователями

**Вывести список всех клиентов:**

```sh
userlist
```

**Вывести ссылку и QR-код для подключения основного пользователя:**

```sh
mainuser
```

**Создать нового пользователя:**

```sh
newuser
```

**Удалить пользователя:**

```sh
rmuser
```

**Создать ссылку для подключения:**

```sh
sharelink
```

В домашней папке пользователя будет создан файл `help` — в нём содержатся подсказки с описанием команд. Посмотреть его можно с помощью команды (нужно находиться в домашней папке пользователя):

```sh
cat help
```

## Отключаем systemd-resolved
```sh
sudo mkdir -p /etc/systemd/resolved.conf.d
vi /etc/systemd/resolved.conf.d/adguardhome.conf

[Resolve]
DNS=127.0.0.1
DNSStubListener=no

sudo mv /etc/resolv.conf /etc/resolv.conf.backup
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

sudo systemctl reload-or-restart systemd-resolved
```

## Устанавливаем AdGuard Home 

curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v

or

wget --no-verbose -O - https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v

or

fetch -o - https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v

## Устанавливаем Iptables-persistent
```sh
   sudo apt install iptables-persistent

cохранить правила:
   sudo iptables-save | sudo tee /etc/iptables/rules.v4
   sudo ip6tables-save | sudo tee /etc/iptables/rules.v6
восстановить правила:
iptables-restore < /etc/iptables/rules.v4

vi /etc/iptables/rules.v4

# Generated by iptables-save v1.8.10 (nf_tables) on Tue Jun 24 13:48:44 2025
*filter
:INPUT DROP [3548:624998]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [234420:154419002]
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -m conntrack --ctstate INVALID -j DROP
-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
-A INPUT -s 127.0.0.1/32 -p udp -m udp --dport 53 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -s 127.0.0.1/32 -p tcp -m tcp --dport 53 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -s {Your home address}/32 -p tcp -m tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -s {Your home address}/32 -p tcp -m tcp --dport 3000 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -s {Your home address}/32 -p tcp -m tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
-A FORWARD -i {server interface} -j ACCEPT
-A FORWARD -d {server ip}/32 -p udp -m udp --dport 53 -j ACCEPT
-A FORWARD -d {server ip}/32 -p tcp -m tcp --dport 53 -j ACCEPT
COMMIT
# Completed on Tue Jun 24 13:48:44 2025
# Generated by iptables-save v1.8.10 (nf_tables) on Tue Jun 24 13:48:44 2025
*nat
:PREROUTING ACCEPT [6744:885293]
:INPUT ACCEPT [2640:151648]
:OUTPUT ACCEPT [8162:652148]
:POSTROUTING ACCEPT [66:3960]
-A PREROUTING -p udp -m udp --dport 53 -j DNAT --to-destination {server ip}:53
-A PREROUTING -p tcp -m tcp --dport 53 -j DNAT --to-destination {server ip}:53
-A POSTROUTING -o {server interface} -j MASQUERADE
COMMIT
# Completed on Tue Jun 24 13:48:44 2025



```

## Полезные ссылки

- [GitHub проекта X-ray Core](https://github.com/XTLS/Xray-core)
- [Официальная документация на русском](https://xtls.github.io/ru/)

## Клиенты для подключения

**Windows**

- [v2rayN](https://github.com/2dust/v2rayN)  
- [Furious](https://github.com/LorenEteval/Furious)  
- [Invisible Man - Xray](https://github.com/InvisibleManVPN/InvisibleMan-XRayClient)  

**Android**

- [v2rayNG](https://github.com/2dust/v2rayNG)  
- [X-flutter](https://github.com/XTLS/X-flutter)  
- [SaeedDev94/Xray](https://github.com/SaeedDev94/Xray)  

**iOS & macOS arm64**

- [Streisand](https://apps.apple.com/app/streisand/id6450534064)  
- [Happ](https://apps.apple.com/app/happ-proxy-utility/id6504287215)  
- [OneXray](https://github.com/OneXray/OneXray)  

**macOS arm64 & x64**

- [V2rayU](https://github.com/yanue/V2rayU)  
- [V2RayXS](https://github.com/tzmax/V2RayXS)  
- [Furious](https://github.com/LorenEteval/Furious)  
- [OneXray](https://github.com/OneXray/OneXray)  

**Linux**

- [Nekoray](https://github.com/MatsuriDayo/nekoray)  
- [v2rayA](https://github.com/v2rayA/v2rayA)  
- [Furious](https://github.com/LorenEteval/Furious)  
