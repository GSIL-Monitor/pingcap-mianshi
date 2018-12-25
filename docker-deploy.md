# 文档目的
使用docker方式部署，加深对模块的了解，为k8s方式部署做准备

# 尝试3个主机部署服务
文档中是6台部署多节点集群，觉得单节点应该也能跑起来

|主机名|IP|部署服务|数据盘挂载|
| :----: | :----: | :----: | :----: |
|IP1|IP2|IP3|

# 启动PD1
```bash
docker run -d --name pd1 \
  -p 2379:2379 \
  -p 2380:2380 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/pd:latest \
  --name="pd1" \
  --data-dir="/data/pd1" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://192.168.1.101:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://192.168.1.101:2380" \
  --initial-cluster="pd1=http://192.168.1.101:2380"
  ```
  
  
  
