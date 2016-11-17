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
---------------------------------------
  export JAVA_HOME=/home/hadoop/jdk1.8/
  export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
  export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
  --------------------------------------------
输入java -version，显示配置jdk是否成功
安装hadoop2.6.5，解压文件
修改配置文件，进入安装路径/etc/hadoop目录修改
-  ----------------------------------------------
  core-site.xml  
  添加dir和Hadoop运行端口号
    <?xml version="1.0" encoding="UTF-8"?> 
          <?xml-stylesheet type="text/xsl" href="configuration.xsl"?> 
            <configuration> 
              <property> 
                <name>fs.defaultFS</name> 
                <value>hdfs://master:9000</value> 
             </property> 
                 <property> 
                 <name>hadoop.tmp.dir</name> 
                 <value>/home/hadoop/tmp</value> 
             </property> 
            </configuration>    
-----------------------------------------------------         
  hdfs-site.xml  
  添加jab.tracker端口号
   <?xml version="1.0" encoding="UTF-8"?> 
         <?xml-stylesheet type="text/xsl" href="configuration.xsl"?> 
           <configuration> 
                <property> 
                  <name>dfs.namenode.secondary.http-address</name> 
                  <value>slave1:50090</value> 
                </property> 
               <property> 
                  <name>dfs.replication</name> 
                  <value>3</value> 
              </property> 
                 <property> 
                    <name>dfs.namenode.name.dir</name> 
                    <value>file:/home/hadoop/hadoop-2.6.5/tmp/dfs/name</value> 
                  </property> 
                 <property> 
                   <name>dfs.datanode.data.dir</name> 
                   <value>file:/home/hadoop/hadoop-2.6.5/tmp/dfs/data</value> 
                </property> 
            </configuration> 
  -----------------------------------
  hadoop-env.sh  
  新增export JAVA_HOME=/home/hadoop/jdk1.8.0_45/
  即为添加JDK安装运行路径
  修改本地hosts文件，添加master和slave的ip地址
  建立互信关系在master下输入ssh-keygen、复制master的公钥到authorized_keys文件
  slave机器下复制master公钥，并测试是否建立成功
  在Hadoop安装路径的bin目录下启动，失败则./hadoop namenode –format(初始化Hadoop)





以下统计需求
1：完成每天的UV统计
  将所有IP访问从日志文件中读取出，在MAP中通过正则表达式筛选IP
  把ip传入REDUCE，去重，计数输出
2：完成每天访问量Top10的Show统计
  将所有IP和show路径及下的操作取出，在MAP通过正则表达式取出来
  以show的访问路径为key，IP为Value传入REDUCE
  在REDUCE中统计不同的show下的ip访问数量，并去重
3：完成每天的次日留存统计
  将每天的ip信息文件取出，在MAP中对访问信息进行处理
  以IP为KEY，时间为Value传入REDUCE，统计相同ip下的访问次数，次数为2的为留存
