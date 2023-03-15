# README
安装docker-compose
```
curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```
- 导入镜像：docker load -i ./monitor.tar
- 导入镜像：docker load -i ./monitorCommon.tar
- 服务grpc端口9060
- data目录为数据存储 需要递归777权限
- log目录为日志，包含错误日志和运行日志
- monitor配置中需要redis配置
- 执行docker-compose up -d 命令启动
- 确认编排的服务启动状态
- influxdb 需要设置数据库prometheus默认保存时间7天
  - 进入influx容器，执行influx命令
  - 执行use prometheus
  - 创建默认实效规则执行：create retention policy prometheus_7d on prometheus duration 7d replication 1 shard duration 1h default

- 依赖vmcs服务，获取配置信息，配置信息样例如下：
// 模型数据
```json
[
  {
    "instance.identifier": "3220ee79e9f247e3",
    "instance.isa": [
      "type"
    ],
    "type.identifier": "Metrics",
    "type.status": 1,
    "type.property": [
      {
        "instance.identifier": "0e97f92061254ca2",
        "instance.isa": [
          "property"
        ],
        "instance.status": 1,
        "property.type": "string",
        "property.identifier": "name",
        "property.index": "@index(term)",
        "property.name": "名称",
        "property.status": 1
      },
      {
        "instance.identifier": "7a6cedbc155e42a5",
        "instance.isa": [
          "property"
        ],
        "instance.status": 1,
        "property.type": "int",
        "property.identifier": "persistence",
        "property.name": "是否持久化",
        "property.status": 1
      },
      {
        "instance.identifier": "Metrics10e97f92061254ca2",
        "instance.isa": [
          "property"
        ],
        "instance.status": 1,
        "property.type": "int",
        "property.identifier": "count",
        "property.index": "",
        "property.name": "是否计数",
        "property.status": 1
      },
      {
        "instance.identifier": "Metrics20e97f92061254ca2",
        "instance.isa": [
          "property"
        ],
        "instance.status": 1,
        "property.type": "int",
        "property.identifier": "size",
        "property.index": "",
        "property.name": "计数大小",
        "property.status": 1
      },
      {
        "instance.identifier": "d8b3d52cb2504732",
        "instance.isa": [
          "property"
        ],
        "instance.status": 1,
        "property.type": "string",
        "property.identifier": "desc",
        "property.name": "描述",
        "property.status": 1
      },
      {
        "instance.isa": [
          "property"
        ],
        "instance.status": 1,
        "property.type": "string",
        "property.identifier": "tags",
        "property.name": "标识",
        "property.status": 1
      }
    ]
  },
  {
    "instance.identifier": "04f8a4a9e17f486b",
    "instance.isa": [
      "type"
    ],
    "type.identifier": "MetricsValue",
    "type.status": 1,
    "type.property": [
      {
        "instance.identifier": "d19fd820c3ff4024",
        "instance.isa": [
          "property"
        ],
        "property.type": "int",
        "property.identifier": "min",
        "property.name": "最小值",
        "property.status": 1
      },
      {
        "instance.identifier": "2165aa57b222446f",
        "instance.isa": [
          "property"
        ],
        "property.type": "string",
        "property.identifier": "name",
        "property.index": "@index(term)",
        "property.name": "名称",
        "property.status": 1
      },
      {
        "instance.identifier": "93f7904888694569",
        "instance.isa": [
          "property"
        ],
        "property.type": "string",
        "property.identifier": "defult",
        "property.name": "默认值",
        "property.status": 1
      },
      {
        "instance.identifier": "1a23ee2285ed433c",
        "instance.isa": [
          "property"
        ],
        "property.type": "int",
        "property.identifier": "max",
        "property.name": "最大值",
        "property.status": 1
      },
      {
        "instance.identifier": "0432ffad710b43e4",
        "instance.isa": [
          "property"
        ],
        "property.type": "string",
        "property.identifier": "regex",
        "property.name": "正则验证规则",
        "property.status": 1
      },
      {
        "instance.identifier": "b279ea49acd041d2",
        "instance.isa": [
          "property"
        ],
        "property.type": "int",
        "property.identifier": "len",
        "property.name": "长度",
        "property.status": 1
      },
      {
        "instance.identifier": "ae078f3c3c0341d9",
        "instance.isa": [
          "property"
        ],
        "property.type": "string",
        "property.identifier": "type",
        "property.name": "类型",
        "property.status": 1
      }
    ]
  },
  {
    "instance.identifier": "ceb5863d611b4fdd",
    "instance.isa": [
      "relation"
    ],
    "relation.identifier": "Metrics.MetricsValue",
    "relation.createTime": 1667367546494,
    "relation.level": "aggregation",
    "relation.sourceType": "Metrics",
    "relation.targetType": "MetricsValue",
    "relation.updateTime": 1667367546494,
    "relation.status": 1
  }
]

```

// 实例数据
```json
{
  "instance.identifier": "Metricsf9fcbfa8cf684a36",
  "instance.isa": [
    "Metrics"
  ],
  //指标描述
  "Metrics.desc": "cpu占比",
  //是否持久化
  "Metrics.persistence": 1,
  "Metrics.count": 1,// 预留
  "Metrics.size": 1,// 预留
  //指标名
  "Metrics.name": "cpu_ps",
  //tag
  "Metrics.tags": "mac",
  "Metrics.status": 1,
  "Metrics.MetricsValue": [{
    "instance.identifier": "MetricsValuef9fcbfa8cf684a36",
    "instance.isa": [
      "MetricsValue"
    ],
    "MetricsValue.type": "int",
    "MetricsValue.defult": "0",
    "MetricsValue.len": 3,
    "MetricsValue.min": 0,
    "MetricsValue.regex": "",
    "MetricsValue.status": 1
  }]
}
```