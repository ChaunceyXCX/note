# docker 常用应用启动脚本

## N1 --> openwrt --> docker

- docker-home: ``vim /etc/docker/daemon.json -> /opt/docker/``

- ``ddns-go``: docker run -d --name ddns-go --restart=always --net=host -v /opt/ddns-go:/root jeessy/ddns-go -l :9876 -f 600

- ``portainer``：docker run --restart=always -d --name portainer -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /mnt/mmcblk1p3/portainer/data:/data -v /mnt/mmcblk1p3/portainer/public:/public portainer/portainer
- ``nps``：docker run -d --name nps --net=host --restart=always -v /opt/nps/conf:/conf ffdfgdfg/nps
- ```bash
  docker run -d \
  --name=aria2 \
  --net=host \
  -e PUID=0 \
  -e PGID=0 \
  -e TZ=Asia/Shanghai \
  -e SECRET=@Xcx1995 \
  -e CACHE=512M \
  -e PORT=6800 \
  -e UT=true \
  -e RUT=true \
  -e FA=none \
  -e QUIET=true \
  -e SMD=true \
  -p 6881:6881 \
  -p 6881:6881/udp \
  -p 6800:6800 \
  -v /opt/aria2/config:/config \
  -v /opt/download:/downloads \
  --restart unless-stopped \
  superng6/aria2
  ```


- 百度客户端
```
docker run -d --name baidupcs -p 5299:5299 -v /opt/download:/downloads -v /opt/baidupcs/config:/root/.config/BaiduPCS-Go oldiy/baidupcs:latest
```
