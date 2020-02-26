# 检索 ElasticSearch

## 下载安装

```shell
docker search elasticsearch
docker pull elasticsearch
docker images
docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name es01 elasticsearch
```

!>关于es不能启动，或者启动之后关闭，可以使用下面方法
下载：docker pull elasticsearch:6.4.3
启动：docker run -d --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  elasticsearch:6.4.3
进入容器修改配置：docker exec -it es /bin/bash
进入配置文件夹：cd config
修改配置文件：vi elasticsearch.yml
添加配置：
http.cors.enabled: true 
http.cors.allow-origin: "*"

## 访问

http://192.168.31.222:9200/

## 交互

 * SpringBoot默认支持两种技术来和ES交互；
 * 1、Jest（默认不生效）
 * 	需要导入jest的工具包（io.searchbox.client.JestClient）
 * 2、SpringData ElasticSearch【ES版本有可能不合适】
 * 		版本适配说明：https://github.com/spring-projects/spring-data-elasticsearch
 *		如果版本不适配：2.4.6
 *			1）、升级SpringBoot版本
 *			2）、安装对应版本的ES
 *
 * 		1）、Client 节点信息clusterNodes；clusterName
 * 		2）、ElasticsearchTemplate 操作es
 *		3）、编写一个 ElasticsearchRepository 的子接口来操作ES；
 *	两种用法：https://github.com/spring-projects/spring-data-elasticsearch
 *	1）、编写一个 ElasticsearchRepository

