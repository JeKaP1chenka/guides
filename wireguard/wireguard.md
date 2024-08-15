# Wireguard

## Контент

1. [Настройка wireguard на сервере](#настройка-wireguard-на-сервере)
2. [Создание клиента](#создание-клиента)

## Настройка wireguard на сервере

- Обновляем сервер:

```bash
apt update && apt upgrade -y
```

- Ставим wireguard:

```bash
apt install -y wireguard
```

- Генерим ключи сервера:

```bash
wg genkey | tee /etc/wireguard/privatekey | wg pubkey | tee /etc/wireguard/publickey
```

- Проставляем права на приватный ключ:

```bash
chmod 600 /etc/wireguard/privatekey
```

- Проверим, как у вас называется сетевой интерфейс:

```bash
ip a
```

> Скорее всего у вас сетевой интерфейс eth0, но возможно и другой, например, ens3 или как-то иначе. Это название интерфейса используется далее в конфиге /etc/wireguard/wg0.conf, который мы сейчас создадим:

```bash
nano /etc/wireguard/wg0.conf
```

> можно использовать любой удобный текстовый редактор

- вставляем данный кофиг

```conf
[Interface]
PrivateKey = <privatekey>
Address = 10.0.0.1/24
ListenPort = 51830
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```

### ВАЖНО

**Обратите внимание — в строках PostUp и PostDown использован как раз сетевой интерфейс eth0. Если у вас другой — замените eth0 на ваш.**

Вставляем вместо `<privatekey>` содержимое файла /etc/wireguard/privatekey

`ListenPort = 51830` по дефолту должно стоять 51820**

- Настраиваем IP форвардинг:

```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
```

- проверяем что все установилось правильно

```bash
sysctl -p
```

- Включаем systemd демон с wireguard:

```bash
systemctl enable <wg-quick@wg0.service>
systemctl start <wg-quick@wg0.service>
systemctl status <wg-quick@wg0.service>
```

---

## Создание клиента

-Создаём ключи клиента:

```bash
wg genkey | tee /etc/wireguard/<client_name>_privatekey | wg pubkey | tee /etc/wireguard/<client_name>_publickey
```

вставляем вместо `<client_name>` имя клиента или что угодно

- Добавляем в конфиг сервера клиента:

```bash
nano /etc/wireguard/wg0.conf
```

```conf
[Peer]
PublicKey = <<client_name>_publickey>
AllowedIPs = 10.0.0.2/32
```

Вместо `<<client_name>_publickey>`  — заменяем на содержимое файла `/etc/wireguard/<client_name>_publickey`

- Перезагружаем systemd сервис с wireguard:

```bash
systemctl restart wg-quick@wg0
systemctl status wg-quick@wg0
```

- На локальной машине (например, на ноутбуке) создаём текстовый файл с конфигом клиента:

```bash
nano WireGuard.conf
```

```conf
[Interface]
PrivateKey = <CLIENT-PRIVATE-KEY>
Address = 10.0.0.2/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER-PUBKEY>
Endpoint = <SERVER-IP>:51830
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 20
```

Здесь `<CLIENT-PRIVATE-KEY>` заменяем на приватный ключ клиента, то есть содержимое файла `/etc/wireguard/<client_name>_privatekey` на сервере.  `<SERVER-PUBKEY>` заменяем на публичный ключ сервера, то есть на содержимое файла `/etc/wireguard/publickey` на сервере. `<SERVER-IP>` заменяем на IP сервера.

Этот файл открываем в Wireguard клиенте (есть для всех операционных систем, в том числе мобильных) — и жмем в клиенте кнопку подключения.

### ВАЖНО Добавление второго и более пользователя

- Если вы хотите добавить более одного пользователя то надо при добавлении `[Peer]` в серверный конфиг надо изменить `AllowedIPs`, чтоб он не совпадал с другими `[Peer]`

- Пример

`wg0.conf`

```conf
[Interface]
PrivateKey = <privatekey>
Address = 10.0.0.1/24
ListenPort = 51830
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = <<client_name>_publickey>
AllowedIPs = 10.0.0.2/32

[Peer]
PublicKey = <<client_name2>_publickey>
AllowedIPs = 10.0.0.3/32
```

конфиг для первого клиента

```conf
[Interface]
PrivateKey = <CLIENT-PRIVATE-KEY>
Address = 10.0.0.2/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER-PUBKEY>
Endpoint = <SERVER-IP>:51830
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 20
```

конфиг для второго клиента

```conf
[Interface]
PrivateKey = <CLIENT-PRIVATE-KEY-2>
Address = 10.0.0.3/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER-PUBKEY>
Endpoint = <SERVER-IP>:51830
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 20
```
