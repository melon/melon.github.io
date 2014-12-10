title: elasticsearch的单机模式
date: 2014-12-10 14:25:21
categories:
- IT
tags:
- elasticsearch
- distributed computing
---
默认情况下，elasticsearch采用的是分布式(distributed)模式，就是说elasticsearch的一个节点(node)在启动时，它会默认自己是属于一个集群(cluster)中的一个节点，从而会尝试去寻找局域网中的其他节点。

但是在有些情况下，比如你正处于开发状态，你不希望将开发时使用的节点连到已有的集群中去的时候，或者比如说你在尝试一个新的elasticsearch版本的时候，你可能会有不希望本机节点去加入已有集群的这样一种需求。

为了实现这样的需求，你可以把elasticsearch设置成单机模式。具体来说你需要编辑$ES_HOME/config/elasticsearch.yml文件

{% code lang:yml %}
cluster.name: melon-local-cluster # 给你的本地测试集群起个名字
node.local: true # 将节点设置成本地模式
{% endcode %}

然后重启服务即可。