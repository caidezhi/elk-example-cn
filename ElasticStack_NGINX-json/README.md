### Elastic Stack入门之处理Nginx日志(JSON格式)
本篇入门教程将会演示如何用Elastic Stack来对Nginx访问日志进行解析，分析以及可视化。我们将采用JSON格式的Nginx日志，示例数据文件可以在下面的下载章节找到。


##### 版本
本示例在以下版本测试通过

- Elasticsearch 5.0
- Logstash 5.0
- Kibana 5.0.0

### 安装与设置
* 参照 [Installation & Setup Guide](../installation_and_setup.md) 安装 Elastic Stack。

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

- `nginx_json_logs` - JSON格式的 Nginx 日志文件
- `nginx_json_logstash.conf` - Logstash 配置文件
- `nginx_json_template.json` - Nginx 日志字段映射文件
- `nginx_json_kibana.json` -  Kibana dashboard 配置文件

可以用如下命令下载文件:

```shell
mkdir  nginx_json_ElasticStack_Example
cd nginx_json_ElasticStack_Example
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_NGINX-json/nginx_json_logstash.conf
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_NGINX-json/nginx_json_kibana.json
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_NGINX-json/nginx_json_template.json
wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_NGINX-json/nginx_json_logs
```

本示例用到的JSON格式的Nginx日志可以通过在 nginx配置文件中的配置如下的`log_format`产生

```
log_format json_logstash '{ "time": "$time_local", '
                           '"remote_ip": "$remote_addr", '
                           '"remote_user": "$remote_user", '
                           '"request": "$request", '
                           '"response": "$status", '
                           '"bytes": "$body_bytes_sent", '
                           '"referrer": "$http_referer", '
                           '"agent": "$http_user_agent" }';
```

### 运行示例
##### 1. 用Logstash 推送数据到 Elasticsearch
* 运行下面的命令把示例日志文件索引到 Elasticsearch 中

```shell
cd nginx_json_ElasticStack_Example
cat nginx_json_logs | <path_to_logstash_root_dir>/bin/logstash -f nginx_json_logstash.conf
```

 * 检查数据是否被Elasticsearch索引成功

  运行 `http://localhost:9200/nginx_json_elastic_stack_example/_count` 返回的响应中应该能看到类似 `"count":51462`

 **注意:** 我们假设Elasticsearch跟Logstash运行在同一台机器，并且没有修改默认的配置。如果你修改了默认的配置，请修改配置文件`nginx_json_logstash.conf`， `output { elasticsearch { ... } }`中的   `host` 和 `cluster`

##### 2. Kibana 数据可视化

* 用浏览器访问 Kibana `http://localhost:5601`
* 用 Kibana 连接 Elasticsearch中的 `nginx_json_elastic_stack_example` 索引
    * **Management** >> **Index Patterns** >> **Add New**。指定 `nginx_json_elastic_stack_example` 作为索引模式名并点击 **Create** 定义索引模式
* 在 Kibana 中加载示例 dashboard
    * **Management** >> **Saved Objects** >> **Import**, 选择 `nginx_json_kibana.json`
* 打开 dashboard
    * 点击**Dashboard** 标签，打开 `Sample Dashboard for Nginx Logs`仪表盘

你应该能看到下面的仪表盘。
![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/ElasticStack_NGINX-json/nginx_json_dashboard.jpg?raw=true)
