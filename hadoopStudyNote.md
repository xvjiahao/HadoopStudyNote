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
<img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/hdfs%E7%BB%93%E6%9E%84.jpg" width="350" height="300" alt="图片加载失败时，显示这段字"/>    
引用地址:https://www.cnblogs.com/zhangwuji/p/7594725.html   

名词说明:   
&ensp;&ensp;&ensp;&ensp;1. NameNode: 主节点，管理HDFS的命名空间, 数据地址映射信息，处理客户端读取请求。

&ensp;&ensp;&ensp;&ensp;2. SecondaryNameNode: NameNode主节点的热备份。一方便,它定时将editlogs中的数据持久化，以减少editlogs的大小，从而减少NameNode的启动时间。另一方面，它也避免当NameNode出现意外宕机，而丢失大量的改动信息   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(a  <img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/namenode%E7%BB%93%E6%9E%84.png" width="350" height="300" alt="图片加载失败时，显示这段字"/> 
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;(b  <img src="https://github.com/Zhao233/HadoopStudyNote/blob/master/%E5%9B%BE%E7%89%87/secondaryNameNode%E7%BB%93%E6%9E%84.png" width="350" height="300" alt="图片加载失败时，显示这段字"/>
上述两张图片的引用地址:https://www.cnblogs.com/chenyaling/p/5521464.html 