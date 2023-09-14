# swarm keepalived

docker

```bash
echo "modprobe ip_vs" >> /etc/modules
modprobe ip_vs
```

```bash
docker run -d --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW \
  --net=host \
  -e KEEPALIVED_UNICAST_SRC_IP="172.16.5.4" \
  -e KEEPALIVED_UNICAST_PEER="172.16.5.5" \
  -e KEEPALIVED_VIRTUAL_IPADDRESS=192.168.100.254/32 \
  -e KEEPALIVED_PRIORITY=100 \
  -e KEEPALIVED_INTERFACE="eth0" \
  rootshellcoder/swarm-keepalived:latest
```

[docker-swarm](./keepalived-service.yml)

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
