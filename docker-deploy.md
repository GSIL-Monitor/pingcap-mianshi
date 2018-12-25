# 文档目的
使用docker方式部署，加深对模块的了解，为k8s方式部署做准备

# 结论
- 熟悉服务启动
- 熟悉组件间的关系
- 发现启动日志有报错：如不是ssd warning ，auth等暂未深究
- 可以通过--config pd.yaml指定配置
- 容器通过ENTRYPOINT启动的服务

# 尝试最小化安装
在一台主机上启动3个容器

# PD1

```bash
docker run -d --name pd1 \
  -p 2379:2379 \
  -p 2380:2380 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data1:/data \
  pingcap/pd:latest \
  --name="pd1" \
  --data-dir="/data/pd1" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://10.69.80.55:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://10.69.80.55:2380" \
  --initial-cluster="pd1=http://10.69.80.55:2380"
```
  
  # TiKV1
  
  ```bash
  docker run -d --name tikv1 \
  -p 20160:20160 \
  --ulimit nofile=1000000:1000000 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data2:/data \
  pingcap/tikv:latest \
  --addr="0.0.0.0:20160" \
  --advertise-addr="10.69.80.55:20160" \
  --data-dir="/data/tikv1" \
  --pd="10.69.80.55:2379"
```

# 启动TiDB

```bash
docker run -d --name tidb \
  -p 4000:4000 \
  -p 10080:10080 \
  -v /etc/localtime:/etc/localtime:ro \
  pingcap/tidb:latest \
  --store=tikv \
  --path="10.69.80.55:2379"
  ```

# 使用musql链接db

```bash
$ mysql -h 127.0.0.1 -P 4000 -u root -D test
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.10-TiDB-v2.1.0-rc.3-358-gbd69b15 MySQL Community Server (Apache License 2.0)

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [test]> show tables;
Empty set (0.00 sec)

MySQL [test]> 
```

# 下一步
- 尝试使用配置启动服务
