### Elastic Stack入门之 Apache 日志

本篇入门教程将会演示如何用Elastic Stack来对 Apache 访问日志进行解析，分析以及可视化。示例数据文件是默认的apache combined log。

##### 版本
本示例在以下版本测试通过

- Elasticsearch 5.0
- Logstash 5.0
- Kibana 5.0.0

### 安装与设置
* 参照 [Installation & Setup Guide]() 安装 Elastic Stack。

* 运行 Elasticsearch & Kibana
  ```
  <elasticsearch根目录>/bin/elasticsearch
  <kibana根目录>/bin/kibana
  ```

* 检查Elasticsearch 和 Kibana 是否正常运行
  - 用浏览器访问 `localhost:9200` -- 返回的状态码应该是 200 
  - 用浏览器访问 `localhost:5601`--应该能看到 Kibana 页面

  **Note:** 默认情况下Elasticsearch 运行在9200端口, Kibana 运行在5601端口。 如果你更改了默认设置, 请修改为对应的端口号。

### 下载示例文件

下载下面的文件到你的本地目录 :
- `apache_logs` - 示例数据 (apache combined log格式)
- `apache_logstash.conf` - Logstash 配置文件
- `apache_template.json` - apache 日志字段映射文件
- `apache_kibana.json` - Kibana dashboard 配置文件


可以用如下命令下载文件:
```shell
mkdir  Apache_ELK_Example
cd Apache_ELK_Example
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_apache/apache_logstash.conf
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_apache/apache_template.json
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_apache/apache_kibana.json
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_apache/apache_logs
```

### 运行示例
##### 1. 用Logstash 推送数据到 Elasticsearch
* 运行下面的命令把示例日志文件索引到 Elasticsearch 中。[解析整个文件并索引到Elasticsearch可能会花费数分钟]

```shell
cat apache_logs | <path_to_logstash_root_dir>/bin/logstash -f apache_logstash.conf
```

 * 检查数据是否被Elasticsearch索引成功

  运行 `http://localhost:9200/apache_elastic_example/_count` 返回的响应中应该能看到类似 `"count":10000`

 **注意:** 我们假设Elasticsearch跟Logstash运行在同一台机器，并且没有修改默认的配置。如果你修改了默认的配置，请修改配置文件
 `apache_logstash.conf，`output { elasticsearch { ... } }`  中的   `host` 和 `cluster`

##### 2. Kibana 数据可视化

* 用浏览器访问 Kibana `http://localhost:5601`
* 用 Kibana 连接 Elasticsearch中的 `apache_elastic_example` 索引
    * **Management** >> **Index Patterns** >> **Add New**。指定 `apache_elastic_example` 作为索引模式名并点击 **Create** 定义索引模式。(不要勾选 ***Use event times to create index names**,使用@timestamp 作为时间字段)
* 在 Kibana 中加载示例 dashboard
    * **Management** >> **Saved Objects** >> **Import**, 选择 `apache_kibana.json`
* 打开 dashboard
    * 点击**Dashboard** 标签，打开 `Sample Dashboard for Apache Logs`仪表盘

你应该能看到下面的仪表盘。

![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/ElasticStack_apache/apache_dashboard.jpg?raw=true)
