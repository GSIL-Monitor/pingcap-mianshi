# 文档目的
在docker环境下，使用配置启动pd  tikv tidb

# 步骤摘要
- 配置pd tikv tidb的配置文件
- 重新docker build做成镜像
- 启动的时候使用配置启动服务

# pd

```bash
$cat pd/Dockerfie
#FROM的镜像是pingcap/pd:latest
FROM registry.xiaojukeji.com/didionline/pingcap-pd:20181225
COPY ./config.toml /pd.toml
ENTRYPOINT ["/pd-server", "--config=/pd.toml"]

$cat config.toml
# PD Configuration.

name = "pd"
#data-dir = "default.pd"
data-dir = "/data/pd1"

client-urls = "http://0.0.0.0:2379"
# if not set, use ${client-urls}
advertise-client-urls = "http://10.69.80.55:2379"

peer-urls = "http://0.0.0.0:2380"
# if not set, use ${peer-urls}
advertise-peer-urls = "http://10.69.80.55:2380"

initial-cluster = "pd=http://10.69.80.55:2380"

下面是服务的参数和调优
....

```

# tikv

```bash
$cat Dockerfile 
FROM registry.xiaojukeji.com/didionline/pingcap-tikv:20181225
COPY ./config.toml /tikv.toml

ENTRYPOINT ["/tikv-server","--config=/tikv.toml"]

$cat config.toml
# TiKV Configuration.
[server]
addr = "0.0.0.0:20160"

# tikv server advertise IP.
advertise-addr = "100.69.80.55:20160"

[storage]
data-dir = "/data/tikv1"

[pd]
endpoints = ["100.69.80.55:2379"]
```

# tidb

```bash
$cat Dockerfile
FROM registry.xiaojukeji.com/didionline/pingcap-tidb:20181225

COPY ./config.toml /tidb.toml

ENTRYPOINT ["/tidb-server","--config=/tidb.toml"]

$cat config.toml
# TiDB Configuration.

# TiDB server host.
host = "0.0.0.0"

# tidb server advertise IP.
advertise-address = ""

# TiDB server port.
port = 4000

# Registered store name, [tikv, mocktikv]
#store = "mocktikv"
store = "tikv"

# TiDB storage path.
#path = "/tmp/tidb"
path = "100.69.80.55:2379"

....参数调优

```
