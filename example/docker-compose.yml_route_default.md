# Default route

Default Gateway is a device on the network (usually a router) that serves as an output point for data packets addressed to other networks. It is used when a computer or other network device cannot find a more specific route for data transmission. In this case, the devices send data via the default gateway.

Enable default gateway

If you transfer the `DEFAULT_GATEWAY` variable to docker-compose.yml this will add a default route to the routing table

```json
virtual_routes {
  0.0.0.0/0 via {{DEFAULT_GATEWAY}} dev {{KEEPALIVED_INTERFACE}} onlink
}
```

Interface enp3s0

```bash
ip a s enp3s0
2: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:13:5a:a2 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.2/24 brd 192.168.10.255 scope global enp3s0
       valid_lft forever preferred_lft forever
    inet 108.177.16.24/27 scope global enp3s0:0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fe13:5aa2/64 scope link
       valid_lft forever preferred_lft forever
```

route

```bash
routel
Dst             Gateway         Prefsrc         Protocol Scope   Dev              Table
default         108.177.16.1                    keepalived       enp3s0
108.177.16.0/27                 108.177.16.24   kernel   link    enp3s0
192.168.10.0/24                 192.168.10.2    kernel   link    enp3s0
108.177.16.24                   108.177.16.24   kernel   host    enp3s0           local
108.177.16.31                   108.177.16.24   kernel   link    enp3s0           local
127.0.0.0/8                     127.0.0.1       kernel   host    lo               local
127.0.0.1                       127.0.0.1       kernel   host    lo               local
127.255.255.255                 127.0.0.1       kernel   link    lo               local
192.168.10.2                    192.168.10.2    kernel   host    enp3s0           local
192.168.10.255                  192.168.10.2    kernel   link    enp3s0           local
```

docker-compose.yml

```yml
---
services:
  master:
    image: keepalived:latest
    environment:
      DAFAULT_GATEWAY: "108.177.16.1"
      KEEPALIVED_UNICAST_SRC_IP: "192.168.10.2"
      KEEPALIVED_UNICAST_PEER: "192.168.10.3"
      KEEPALIVED_VIRTUAL_IPADDRESS: "108.177.16.24/27"
      KEEPALIVED_PRIORITY: "200"
      KEEPALIVED_INTERFACE: "enp3s0"
    network_mode: host
    restart: always
    cap_add:
      - NET_ADMIN
      - NET_BROADCAST
      - NET_RAW
```


```asci
                                         .-~~~-.
                                 .- ~ ~-(       )_ _
                                /                     ~ -.
                               |                          ',
                               \                         .'
                                 ~- ._ ,. ,.,.,.,., .-~
                                           |
                                    ----------------
                                    | 108.177.16.1 |
                                    ----------------
                          master    /   gateway    \   slave
                          enp3s0   /                \  eth0
                    ----------------------     -----------------------
                    | vrrp 108.177.16.24 |     | vrrp ---.---.---.--- |
                    | IP 192.168.10.2    |     | IP 192.168.10.3      |
                    ----------------------     ------------------------
                    docker      |                         |      docker
                                ----------unicast----------

```
