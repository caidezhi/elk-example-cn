### 安装 Elasticsearch, Logstash & Kibana

**版本**
所有例子都在Elastic Stack 5.0 下测试通过.

**java 版本**

Elasticsearch和Logstash需要java 8以上版本。请根据需要安装或升级 - 可以使用 [Oracle官方发行版](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或使用开源发行版，例如 [OpenJDK](http://openjdk.java.net/)。请运行 `java -version` 检查java版本。


**安装 Elasticsearch**
*	下载 [Elasticsearch 二进制包](https://www.elastic.co/downloads/elasticsearch)。
*	解压`.zip` 或 `tar.gz` 文件

(参考[这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/_installation.html)，有更多安装细节)

**安装 Logstash**
* 下载 [Logstash 二进制包](https://www.elastic.co/downloads/logstash)。
* 解压`.zip` 或 `tar.gz` 文件

(参考 [这里](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html) )

**安装  Kibana**
-	下载  [Kibana 5 二进制包](https://www.elastic.co/downloads/kibana) 。
-	解压`.zip` 或 `tar.gz` 文件

(参考 [这里](https://www.elastic.co/guide/en/kibana/current/setup.html))

### 测试安装

**Elasticsearch**

打开一个新的命令行窗口运行Elasticsearch. 
```Shell
<path_to_elasticsearch_root_dir>/bin/elasticsearch 
```

Elasticsearch应该在9200 运行了。请用浏览器访问 `http://localhost:9200`, 应该能看到返回的状态是 200


```
{
  "status" : 200,
  "name" : "James Howlett",
  "cluster_name" : "elasticsearch",
     ... truncated output 
}
```

**Logstash**

测试Logstash , 请在命令行运行下面的命令:

```shell
<path_to_logstash_root_dir>/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

在命令行窗口输入 `checking logstash!` . 如果 Logstash 安装成功, 你应该能看到:

```shell
checking logstash!
2015-06-21T01:22:14.405+0000 0.0.0.0 checking logstash!
```
用`CTRL-D` 退出.

**Kibana**

打开一个新的命令行窗口运行Kibana. 
```Shell
<path_to_kibana_root_dir>/bin/kibana 
```
Kibana 应该在 5601运行了. 请用浏览器访问 `localhost:5601`)。你应该能看到Kibana的界面。