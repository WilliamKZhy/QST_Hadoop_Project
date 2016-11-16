# Round 1 介绍
设计方案：
1：目的
统计网站的连续一段时间内的访问数据，进行数据分类和整理，输出想要的结果。
2：概述
在大数据(hadoop/hive/hdfs环境)下，对大量的数据进行筛选和分类，然后汇总或统计出希望知道的数据内容。
3：设计
数据清洗：使用正则表达式将每条访问数据归类分出。
数据汇总：根据需求将不同类别的数据统计，排序，去重。
数据筛选：根据需求将需要的数据统计
环境搭建：
1：准备工作
几台独立的计算机，完整的Hadoop生态链(HDFS/MapReduce/HIVE/Hbase)
2:Hadoop环境搭建
配置openssh-client、lrzsz，方便在外部界面对Linux进行操作
配置jdk1.8，并在bashrc文件中添加
  export JAVA_HOME=/home/hadoop/jdk1.8/
  export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
  export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
输入java -version，显示配置jdk成功
安装hadoop2.6.5，直接执行bin路径下install命令就行
修改配置文件，进入安装路径/etc/hadoop目录修改
  core-site.xml  添加dir和Hadoop运行端口号
  hdfs-site.xml  添加jab.tracker端口号
  hadoop-env.sh  新增export JAVA_HOME=/home/hadoop/jdk1.8.0_45/,即为添加JDK安装运行路径
修改本地hosts文件，添加master和slave的ip地址
建立互信关系在master下输入ssh-keygen、复制master的公钥到authorized_keys文件
slave机器下复制master公钥，并测试是否建立成功
在Hadoop安装路径的bin目录下启动，失败则./hadoop namenode –format(初始化Hadoop)





以下统计需求
1：完成每天的UV统计
将所有IP访问通过正则表达式从日志文件中取出，然后通过去重IP，最后剩下的就是UV的总量
2：完成每天访问量Top10的Show统计
将所有IP和show路径及下的操作通过正则表达式取出来，然后去重IP，统计相同show路径的操作进行统计，最后排序输出Top10
3：完成每天的次日留存统计
将相邻两天的IP分别取出，通过去重排序，然后两天的数据进行对比，得到留存数据
