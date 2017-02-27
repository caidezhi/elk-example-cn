## Elastic Stack案例分析之纽约交通事故数据分析

本示例将演示如何用 Elastic Stack 分析并可以画纽约市交通事故数据。示例用到的数据 [NYPD Motor Vehicle Collision data](https://data.cityofnewyork.us/Public-Safety/NYPD-Motor-Vehicle-Collisions/h9gi-nx95?) 来源于 [NYC Open Data](https://data.cityofnewyork.us/)。

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

### 下载示例文件

-  **下载数据:**

    从 [NYC Open Data Portal](https://data.cityofnewyork.us/Public-Safety/NYPD-Motor-Vehicle-Collisions/h9gi-nx95?)下载纽约交通事故数据，并将文件重命名为 `nyc_collision_data.csv`。
  ```
  mkdir nyc_collision
  cd nyc_collision
  wget https://data.cityofnewyork.us/api/views/h9gi-nx95/rows.csv?accessType=DOWNLOAD -O nyc_collision_data.csv
  ```

* **下载配置文件:**

  下载下面的配置文件，并放在与 `nyc_collision_data.csv` 相同目录:
  - `nyc_collision_logstash.conf` - Logstash 配置文件
  - `nyc_collision_template.json` - 字段映射文件
  - `nyc_collision_kibana.json` - Kibana dashboard配置文件

  ```shell
  wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_nyc_traffic_accidents/nyc_collision_logstash.conf
  wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_nyc_traffic_accidents/nyc_collision_template.json
  wget https://raw.githubusercontent.com/elastic/examples/master/ElasticStack_nyc_traffic_accidents/nyc_collision_kibana.json
  ```

### 运行示例
##### 1. 用Logstash推送数据到 Elasticsearch 
* 运行下面的命令将 `nyc_collision_data.csv` 索引到 Elasticsearch.

    ```shell
    cat nyc_collision/nyc_collision_data.csv | <path_to_logstash_root_dir>/bin/logstash -f nyc_collision_logstash.conf
    ```

* 检查数据是否成功索引到 Elasticsearch

  访问 `http://localhost:9200/nyc_visionzero/_count` 返回的响应应该包含的 `count`值，应该是整数。

**注意:** 我们假设Elasticsearch跟Logstash运行在同一台机器，并且没有修改默认的配置。如果你修改了默认的配置，请修改配置文件`nginx_json_logstash.conf`， `output { elasticsearch { ... } }`中的   `host` 和 `cluster`

##### 2. 在 Kibana中可视化数据

* 用浏览器访问 `http://localhost:5601` 
* 用Kibana 连接到 Elasticsearch中的 `nyc_visionzero` 索引
    * 点击 **Management**  >> **Index Patterns**  >> **Create New**. 指定 `nyc_visionzero` 为索引模式名 **Create** 并点击 **Create** 定义索引模式.(不要勾选 ***Use event times to create index names**,使用@timestamp 作为时间字段)
* 加载示例仪表盘到 Kibana
    * 点击 **Management**  >> **Saved Objects**  >> **Import**, 选择 `nyc_collision_kibana.json`
* 打开仪表盘
    * 点击 **Dashboard** ,打开仪表盘 `NYC Motor Vehicles Collision`

你应该能看到类似下面的Dashboard
![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/ElasticStack_nyc_traffic_accidents/nyc_collision_dashboard.jpg?raw=true)
