### Elastic Stack入门之处理 Twitter
本篇入门教程将会演示如何用Elastic Stack来对**Twitter stream**进行处理

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
- `twitter_logstash.conf` - Logstash 配置文件
- `twitter_template.json` - 字段映射文件
- `twitter_kibana.json` - Kibana dashboard 配置文件

```shell
mkdir twitter_elastic_example
cd twitter_elastic_example
wget https://raw.githubusercontent.com/elastic/examples/master/ELK_twitter/twitter_logstash.conf
wget https://raw.githubusercontent.com/elastic/examples/master/ELK_twitter/twitter_template.json
wget https://raw.githubusercontent.com/elastic/examples/master/ELK_twitter/twitter_kibana.json
```

### 运行示例
##### 1. 使用Twitter API keys
* 获取Twitter API keys 和 Access Tokens

  本片示例会使用API实时监控Twitter。为此，我们需要在 [创建Twitter app](https://apps.twitter.com/app/new)Twitter 获取API keys 和 Access Tokens

* 修改 Logstash 配置文件 

  修改`twitter_logstash.conf`文件中的`input { twitter { } }` 添加之前获取到的API key和 Access Tokens。如下
```
input {
  twitter {
    consumer_key       => "INSERT YOUR CONSUMER KEY"
    consumer_secret    => "INSERT YOUR CONSUMER SECRET"
    oauth_token        => "INSERT YOUR ACCESS TOKEN"
    oauth_token_secret => "INSERT YOUR ACCESS TOKEN SECRET"
    keywords           => [ "thor", "spiderman", "wolverine", "ironman", "hulk"]
    full_tweet         => true
  }
}
```

##### 2. 用Logstash推送数据到 Elasticsearch
* 执行下面的命令开始索引twitter 数据到 Elasticsearch。因为我们实时监控 Twitter, 收集的数据量取决于关键字的流行程度。
运行下面的命令，你会在控制台看到一些列的 `...`，这表示抓取到了新的数据。

  ```shell
   <path_to_logstash_root_dir>/bin/logstash -f twitter_logstash.conf
  ```

* 检查数据是否被索引到Elasticsearch

  请求 `http://localhost:9200/twitter_elastic_example/_count` 应该从响应中看到 `count` 是一个正数

   **注意:** 我们假设Elasticsearch跟Logstash运行在同一台机器，并且没有修改默认的配置。如果你修改了默认的配置，请修改配置文件`twitter_logstash.conf`， `output { elasticsearch { ... } }`中的   `host` 和 `cluster`


##### 3. Kibana 数据可视化

* 用浏览器访问 Kibana `http://localhost:5601`
* 用Kibana 连接到 Elasticsearch中的索引`twitter_elastic_example`
    * 点击 **Management**  >> **Index Patterns**  >> ** Add New. 用 `twitter_elastic_example` 作为索引模式名，点击 **Create** 定义索引模式 (不要勾选 ***Use event times to create index names**,使用@timestamp 作为时间字段)
* 加载示例仪表盘到 Kibana
    * 点击 **Management**  >> **Saved Objects**  >> **Import**, 选择 `twitter_kibana.json`
* 打开仪表盘
    * 点击 **Dashboard** ,打开仪表盘 `Sample Twitter Dashboard`。(因为我们正实时定于twitter的数据，请把自动刷新的选项勾上，以便能在仪表盘看到更新)

你应该能看到类似下面的Dashboard
![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/ElasticStack_twitter/twitter_dashboard.jpg?raw=true)
