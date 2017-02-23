## Elastic Stack 案例分析之美国联邦选举委员竞选捐款数据分析

本篇示例将会演示如何用Elastic Stack来分析美国联邦选举委员会公布的2013-2014大选期间的竞选捐款数据。示例用到[数据](http://www.fec.gov/finance/disclosure/ftpdet.shtml#a2013_2014) 来自 [联邦选举委员会](http://www.fec.gov/finance/disclosure/ftpdet.shtml)网站。

你也可以通过阅读下面这篇博客来了解一些背景知识
[Kibana 4 for investigating PACs, Super PACs, and who your neighbor might be voting for](http://www.elasticsearch.org/blog/kibana-4-for-investigating-pacs-super-pacs-and-your-neighbors/)

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


### 下载和索引数据

你有两种方法把示例数据索引到 Elasticsearch。你可以使用 [快照和还原](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) API 来直接还原 `usfec` 索引。或者，你可以从 USFEC 网站下载原始数据，用[Scripts-Python+Logstash](https://github.com/elastic/examples/tree/master/ElasticStack_usfec/Scripts-Python+Logstash) 这个目录中的脚本来将原始数据索引到Elasticsearch 中。


#### 选项 1. 用快照还原数据
(了解更多 快照/还原 [这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html))

采用这个选项包含4个简单的步骤:

  * 下载并解压索引快照压缩包到本地目录
  
  ```shell
  # Create snapshots directory
  mkdir ./elastic_usfec
  cd elastic_usfec
  # Download index snapshot to your new snapshots directory
  wget http://download.elasticsearch.org/demos/usfec/snapshot_demo_usfec_5_0.tar.gz .
  # Uncompress snapshot file (uncompressed to usfec subfolder)
  tar -xf snapshot_demo_usfec_5_0.tar.gz
  ```
  * 将 `elasticsearch.yml` 配置文件中的 `path.repo`指向快照解压目录。参考 [这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html#_shared_file_system_repository) 。你需要重启来让设置生效。

  * 为快照注册一个文件系统仓库
  ```shell
  curl -XPUT 'http://localhost:9200/_snapshot/usfec' -d '{
      "type": "fs",
      "settings": {
          "location": "<path_to_uncompressed_folder>",
          "compress": true,
          "max_snapshot_bytes_per_sec": "1000mb",
          "max_restore_bytes_per_sec": "1000mb"
      }
  }'
  ```

  * 还原索引数据到Elasticsearch:
    ```shell
    curl -XPOST "localhost:9200/_snapshot/usfec/snapshot_1/_restore"
    ```


#### 选项 2: 用Python处理原始数据

原始的 FEC 数据包含7个独立的文件。为了能在Elasticsearch这种搜索引擎/NoSQL数据库做一些有用查询，通常需要进行数据建模，在多个表之间做数据连接。`Scripts-Python+Logstash`目录包含的脚本，演示了如何使用原始数据文件,进行处理，建模，以及将索引数据到Elasticsearch 中

#### 检查数据可用性

所以一旦创建好，你便能检查数据是否可用。如果一切顺利，运行下面的命令 你可能可以在返回的响应中看到 `count` 值 为4398435 

  ```shell
  curl -XGET localhost:9200/usfec*/_count -d '{
  	"query": {
  		"match_all": {}
  	}
  }'
  ```

####  Kibana 数据可视化
* 用浏览器访问 Kibana `http://localhost:5601`
* 下载 `usfec_kibana.json` 
* 使Kibana 连接到 Elasticsearch 中的 `usfec*` 索引
    * 点击 **Management**  >> **Index Patterns**  >> **Create New**. 使用 `usfec*` 作为索引模式名, 点击**Create**定义索引模式，并使用 @timestamp 作为时间序列字段.
* 加载示例dashboard 到Kibana
    * 点击 **Management**  >> **Saved Objects**  >> **Import**, 选择`usfec_kibana.json`
* 打开dashboard
    * 点击 **Dashboard** 并打开`USFEC: Overview` dashboard

    你应该能看到下面的仪表盘

    ![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/ElasticStack_usfec/usfec_dashboard.jpg?raw=true)
