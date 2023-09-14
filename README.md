# swarm keepalived

docker

`echo "modprobe ip_vs" >> /etc/modules`

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
