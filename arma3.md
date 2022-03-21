# Arma 3

https://linuxgsm.com/servers/arma3server/
https://community.bistudio.com/wiki/Arma_3:_Basic_Server_Config_File
https://community.bistudio.com/wiki/Arma_3:_Startup_Parameters

## co10_Escape

https://github.com/NeoArmageddon/co10_Escape

mkdir A3-co10Escape-Server01-23120
cd A3-co10Escape-Server01-23120
git clone https://github.com/GameServerManagers/LinuxGSM-Docker.git
mkdir config-lgsm log serverfiles


docker-compose.yaml

``` yaml
version: '3.4'
services:
  linuxgsm:
    build:
      context: .
      dockerfile: ./Dockerfile
    container_name: a3co10escape
    environment:
      - GAMESERVER=arma3server
      - LGSM_GITHUBUSER=GameServerManagers
      - LGSM_GITHUBREPO=LinuxGSM
      - LGSM_GITHUBBRANCH=master
    volumes:
      - ./serverfiles:/home/linuxgsm/serverfiles
      - ./log:/home/linuxgsm/log
      - ./config-lgsm:/home/linuxgsm/lgsm/config-lgsm
    ports:
      - "23120:2302/tcp"
      - "23120:2302/udp"
      - "23121:2303/tcp"
      - "23121:2303/udp"
      - "23122:2304/tcp"
      - "23122:2304/udp"
      - "23123:2306/tcp"
      - "23123:2306/udp"
    restart: unless-stopped
```

``` shell
docker-compose up -d
cd config-lgsm/arma3server/
cat _default.cfg >> arma3server.cfg
```
Cal incloure en arma3server.cfg l'id and password steam user

Instal·lem finalment els fitxers d'arma3

``` shell
docker exec -it a3co10escape ./arma3server install
```

Entrem al container

``` shell
docker exec -it a3co10escape /bin/bash
```
