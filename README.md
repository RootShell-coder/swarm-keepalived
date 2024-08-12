# swarm keepalived

[![Docker](https://github.com/RootShell-coder/swarm-keepalived/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/RootShell-coder/swarm-keepalived/actions/workflows/docker-publish.yml)

Update VERSION 2.3.1

[Keepalived for Linux ](https://www.keepalived.org/download.html)

## keepalived configure flags

* disable-dynamic-linking
* prefix=/usr
* exec-prefix=/usr
* bindir=/usr/bin
* sbindir=/usr/sbin
* sysconfdir=/etc
* datadir=/usr/share
* localstatedir=/var
* mandir=/usr/share/man
* with-dbus-data-dir=/usr/share
* enable-bfd
* enable-dbus
* enable-regex
* enable-snmp
* enable-snmp-rfc
* enable-nftables
* disable-iptables
* enable-json

## docker

### master

* iface=eth0
* ipaddress=172.16.5.4
* vip=192.168.100.254 192.168.10.254

```bash
docker run -d --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW --net=host \
  -e KEEPALIVED_UNICAST_SRC_IP="172.16.5.4" \
  -e KEEPALIVED_UNICAST_PEER="172.16.5.5, 172.16.5.6" \
  -e KEEPALIVED_VIRTUAL_IPADDRESS="192.168.100.254, 192.168.10.254" \
  -e KEEPALIVED_PRIORITY="200" \
  -e KEEPALIVED_INTERFACE="eth0" \
  ghcr.io/rootshell-coder/swarm-keepalived:latest
```

docker master log (codespace)

```logs
@RootShell-coder ➜ /workspaces/swarm-keepalived/docker (dev) $ docker logs -f 955
Sat Sep 16 19:04:02 2023: Starting Keepalived v2.2.8 (04/04,2023), git commit v2.2.7-154-g292b299e+
Sat Sep 16 19:04:02 2023: WARNING - keepalived was built for newer Linux 6.3.0, running on Linux 5.15.0-1041-azure #48-Ubuntu SMP Tue Jun 20 20:34:08 UTC 2023
Sat Sep 16 19:04:02 2023: Command line: '/usr/sbin/keepalived' '--dont-fork' '--log-console' '--use-file'
Sat Sep 16 19:04:02 2023:               '/etc/keepalived/keepalived.conf'
Sat Sep 16 19:04:02 2023: Configuration file /etc/keepalived/keepalived.conf
Sat Sep 16 19:04:02 2023: NOTICE: setting config option max_auto_priority should result in better keepalived performance
Sat Sep 16 19:04:02 2023: Starting VRRP child process, pid=26
Sat Sep 16 19:04:02 2023: Startup complete
Sat Sep 16 19:04:02 2023: (VI_1) Entering BACKUP STATE (init)
Sat Sep 16 19:04:05 2023: (VI_1) Entering MASTER STATE

@RootShell-coder ➜ /workspaces/swarm-keepalived/docker (dev) $ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.5.4  netmask 255.255.255.0  broadcast 172.16.5.255
        inet6 fe80::20d:3aff:fe2c:8d4c  prefixlen 64  scopeid 0x20<link>
        ether 00:0d:3a:2c:8d:4c  txqueuelen 1000  (Ethernet)
        RX packets 2183040  bytes 3005950736 (3.0 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 198564  bytes 1551652595 (1.5 GB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0:0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.100.254  netmask 255.255.255.255  broadcast 0.0.0.0
        ether 00:0d:3a:2c:8d:4c  txqueuelen 1000  (Ethernet)

eth0:1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.10.254  netmask 255.255.255.255  broadcast 0.0.0.0
        ether 00:0d:3a:2c:8d:4c  txqueuelen 1000  (Ethernet)
```

### backup 0

* iface=eth0
* ipaddress=172.16.5.5
* vip=192.168.100.254 192.168.10.254

```bash
docker run -d --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW --net=host \
  -e KEEPALIVED_UNICAST_SRC_IP="172.16.5.5" \
  -e KEEPALIVED_UNICAST_PEER="172.16.5.4, 172.16.5.6" \
  -e KEEPALIVED_VIRTUAL_IPADDRESS="192.168.100.254, 192.168.10.254" \
  -e KEEPALIVED_PRIORITY="100" \
  -e KEEPALIVED_INTERFACE="eth0" \
  ghcr.io/rootshell-coder/swarm-keepalived:latest
```

### backup 1

* iface=enp3s0
* ipaddress=172.16.5.6
* vip=192.168.100.254

```bash
docker run -d --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW --net=host \
  -e KEEPALIVED_UNICAST_SRC_IP="172.16.5.6" \
  -e KEEPALIVED_UNICAST_PEER="172.16.5.4, 172.16.5.5" \
  -e KEEPALIVED_VIRTUAL_IPADDRESS="192.168.100.254" \
  -e KEEPALIVED_PRIORITY="50" \
  -e KEEPALIVED_INTERFACE="enp3s0" \
  ghcr.io/rootshell-coder/swarm-keepalived:latest
```

## Variables

| variable                     |  default   | required |
| ---------------------------- | :--------: | :------: |
| KEEPALIVED_INTERFACE         |   `eth0`   |    X     |
| KEEPALIVED_VIRTUAL_ROUTER_ID |    `51`    |    X     |
| KEEPALIVED_PRIORITY          |   `100`    |    X     |
| KEEPALIVED_AUTH_PASS         | `ETlE2RQr` |    X     |
| KEEPALIVED_UNICAST_SRC_IP    |     X      |    Y     |
| KEEPALIVED_UNICAST_PEER      |     X      |    Y     |
| KEEPALIVED_VIRTUAL_IPADDRESS |     X      |    Y     |

## Example

_docker swarm service see [keepalived-service.yml](./example/keepalived-service.yml)_

### deploy to swarm cluster

* `wget https://raw.githubusercontent.com/RootShell-coder/swarm-keepalived/master/keepalived-service.yml`
* edit variables keepalived-service.yml
* `<label>` add labels to cluster manager nodes: `keepalived_master=tue`, `keepalived_backup0=true`, `keepalived_backup1=true`
* `<node>` see cluster nodes `docker node ls`
* `docker node update --label-add <label> <node>`
* `docker stack deploy -c keepalived-service.yml keepalived`

_docker service see [docker-compose.yml](./example/docker-compose.yml)_
