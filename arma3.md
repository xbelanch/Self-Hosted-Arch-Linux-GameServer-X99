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
    container_name: a3co10escape00
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
      - "2302:2302/udp"
      - "2303:2303/udp"
      - "2304:2304/udp"
      - "2305:2305/udp"
      - "2306:2306/udp"
    restart: unless-stopped
```

``` shell
docker-compose up -d
cd config-lgsm/arma3server/
cat _default.cfg >> arma3server.cfg
```
Cal incloure en arma3server.cfg l'id and password steam user i el port que es farà servir.

Sobre el port: https://opendayz.net/threads/running-multiple-servers-on-a-single-machine-including-firewall-setup.22337/

I recorda que s'han de fer port forwarding al router (únicament UDP).

Instal·lem finalment els fitxers d'arma3

``` shell
docker exec -it a3co10escape ./arma3server install
```

Per iniciar el server:

```shell
docker exec -it a3co10escape00 ./arma3server start
```

Per veure els detalls:

```shell
docker exec -it a3co10escape00 ./arma3server details
```

Per veure la consola (real time)

```shell
docker exec -it a3co10escape00 ./arma3server console
```

```
Si volem entrar al container

``` shell
docker exec -it a3co10escape /bin/bash
```

Cal editar els fitxers de configuració, però això ja cal continuar amb el que demana els tutorials de Bohemia :)
