# Docker WireGuard

## Контент

1. [Настройка Docker WireGuard](#настройка-docker-wireguard)
1. [Полезные ссылки](#links)

## Настройка Docker WireGuard

Конфиг для Docker compose [источник](https://github.com/linuxserver/docker-wireguard?tab=readme-ov-file#docker-compose-recommended-click-here-for-more-info)
```yml
version: '3'

services:
  wireguard:
    image: linuxserver/wireguard
    container_name: wireguard
    privileged: true
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - ./wg0.conf:/config/wg0.conf
      - /lib/modules:/lib/modules
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
      - net.ipv6.conf.all.disable_ipv6=0
    restart: unless-stopped
  cont:
    container_name: cont1
    build: .
    depends_on:
      - wireguard
    network_mode: "service:wireguard" # пропускания всего трафика контейнера cont через контейнер (wireguard)

```

запуск контейнер через Docker CLI (terminal/cmd) можно найти [тут](https://github.com/linuxserver/docker-wireguard?tab=readme-ov-file#docker-cli-click-here-for-more-info)

Пример `wg0.conf` файла

```conf
[Interface]
PrivateKey = <CLIENT-PRIVATE-KEY>
Address = 10.0.0.2/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER-PUBKEY>
AllowedIPs = 0.0.0.0/0
Endpoint = <SERVER-IP>:51830
PersistentKeepalive = 20
```

### ВАЖНО!!!

Контейнер `wireguard` не будет корректно работать на Windows так как в самом WSL WireGuard работает не корректно из за отключенного в ядре `CONNMARK`([источник](https://github.com/binhex/arch-delugevpn/issues/372#issuecomment-2022131867)). Но в этом же источнике есть [ссылка](https://www.reddit.com/r/bashonubuntuonwindows/comments/jk4x24/is_there_a_way_to_run_wireguard_within_wsl2/gah860j/) как исправить данную проблему(лично я не пробовал данный способ так как запускал Docker WireGuard на Linux).



## Links

1. [github (linuxserver/docker-wireguard)](https://github.com/linuxserver/docker-wireguard)
2. [docker hub (linuxserver/wireguard)](https://hub.docker.com/r/linuxserver/wireguard)
3. [Ссылка на issues github где говорится об отключенном `CONNMARK` в ядре WSL](https://github.com/binhex/arch-delugevpn/issues/372#issuecomment-2022131867)
4. [Ссылка на Reddit где описан способ как собрать собрать ядро WSL с включенным `CONNMARK`](https://www.reddit.com/r/bashonubuntuonwindows/comments/jk4x24/is_there_a_way_to_run_wireguard_within_wsl2/gah860j/)