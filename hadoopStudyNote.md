# Hadoop 学习笔记

## hadoop结构()
(20190405)  
+ 分布式储存系统(HDFS[hadoop distributed file system]):   
</br>
(1)核心思想: 分布式系统的文件存储模块,将一个大文件拆分为小文件，并存储在不同的数据节点中(datanode), 这样的话读写的速度都会比单文件存储在单节点的速度快。文件一旦写入不能修改删除，只能续写。并且数据节点(datanode)也会有一个备份，防止节点宕机而导致数据丢失。     
</br>
(2)适用场景:    
&ensp;&ensp;&ensp;&ensp;1.高容错率 2.批处理 3.大数据处理    
</br>
(3)不适用场景:  
&ensp;&ensp;&ensp;&ensp;1.低延时访问 2.小文件存储( 低于单数据节点block大小64M，原因:寻道时间会超过读取时间 )    
</br>
(4)结构 
<img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/hdfs%E7%BB%93%E6%9E%84.png" width="350" height="300" alt="hdfs结构"/>    
引用地址:https://www.cnblogs.com/zhangwuji/p/7594725.html   

名词说明:   
&ensp;&ensp;&ensp;&ensp;1. NameNode: 主节点，管理HDFS的命名空间, 数据地址映射信息，处理客户端读取请求。

&ensp;&ensp;&ensp;&ensp;2. SecondaryNameNode: NameNode主节点的热备份。一方便,它定时将editlogs中的数据持久化，以减少editlogs的大小，从而减少NameNode的启动时间。另一方面，它也避免当NameNode出现意外宕机，而丢失大量的改动信息   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(a <img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/namenode%E7%BB%93%E6%9E%84.png" width="350" height="300" alt="namenode结构"/> 
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(b <img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/secondaryNameNode%E7%BB%93%E6%9E%84.png" width="350" height="300" alt="secondaryNameNode结构"/>  
上述两张图片的引用地址:https://www.cnblogs.com/chenyaling/p/5521464.html 

(20190406)
+ 分布式计算框架(MapReduce)  
    (1) 核心思想: 采取类似于分而治之的思想，将一个任务分解为小任务，并将小任务分配给各个datanode,然后再将各个已完成的小任务重新归总。并且，mapreduce主要是以离线处理为主，对磁盘进行操作。  
    (2) 适用场景: 离线的批处理数据(排序，简历数据检索等)  
    (3) 不适用场景: 对延迟要求高的请求服务处理(搜索，服务响应)  
    (4) 结构  
        （a mapreduce架构  
        <img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/MapReduce%E7%BB%93%E6%9E%84.png" width="350" height="300" alt="MapReduce结构"/>  
         名词解释:  
            (1) jobTraker: NameNode上的任务调度中心，负责将任务分配给各个DataNode，并且通过taskTracker检查各个task是否正常运行。    
            (2) taskTraker: task与jobTraker的桥梁，也是task的管理者，负责task的启动关闭等服务。  
        （b mapreduce原理  
        <img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/mapreduce%E5%8E%9F%E7%90%86.png" width="350" height="300" alt="MapReduce原理"/>    
            上述两张图片的地址:https://www.cnblogs.com/ahu-lichang/p/6645074.html  
            (1) Map: 将任务标注为<key,value>的形式，分给各个DataNode  
            (2) Group(Shuffle): 将完成的相同的key的任务整理在一起，然后再标注为<key,{v1,v2}>的形式传送给Reduce。    
            注: shuffle作用为将处理目标相同的数据做一个汇总传递给Reduce，减轻Reduce的处理压力。  
            (3) Reduce: 对Group处理的结果进性合并。