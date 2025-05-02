## jellyfin 部署及使用文档

### 部署
目前使用wsl2(ubuntu22.04) + docker compose部署
wsl2使用镜像网络
docker-compose.yaml
```yaml
services:
  jellyfin:
    image: jellyfin/jellyfin
    runtime: nvidia
    container_name: jellyfin
    network_mode: 'host'
    volumes:
      - jellyfin-config:/config
      - jellyfin-cache:/cache
      - type: bind
        source: /mnt/f/movie
        target: /media
      - type: bind
        source: /mnt/f/fonts
        target: /usr/local/share/fonts/custom
        read_only: true
    restart: 'unless-stopped'
    #ports:
      #- "8096:8096"
    environment:
      - NVIDIA_VISIBLE_DEVICES=all 
    command: nvidia-smi
volumes:
  jellyfin-config:
    external: true
  jellyfin-cache:
    external: true
```

启动命令
```shell
# docker-compose.yml目录下运行
docker compose up -d
```

删除命令
```shell
docker compose down
```
