# Hadoop学习之HDFS篇（一）

<font size="3dp" color="pink">&emsp;&emsp;首先得知道如何在win10操作系统上直接运行代码操作虚拟机上的Hadoop集群，此次我采用的编译软件是 IntelliJ IDEA，直接在win10系统上的 IDEA上运行代码来实现对虚拟机上Hadoop集群的HDFS简单操作。（注意不需要打jar包）</font>

<br>

<font size="3dp">&emsp;&emsp;使用Eclipse编译器的话可以直接通过安装eclipse-hadoop插件完成远程的连接，省去了生成jar包再发送到虚拟机执行这些步骤。IDEA没有此插件所以需要在win10上进行一些环境配置的操作如下：

- 下载对应的Hadoop包（注意不需要从虚拟机中拷贝出已经配置完成的Hadoop包，这里仅仅只是需要 /hadoop/share 包中的一些jar）,之后在win10环境中添加Hadoop包所在位置（注意在hadoop/bin/目录下一定要有 winutils.exe 和 hadoop.dll 没有的话网上多的是）

![](http://m.qpic.cn/psb?/V12dDOqO2iwHZM/8AR7N23227nUhcN9E0o1npXa4DOtTX4je0JjwII4E4U!/b/dLYAAAAAAAAA&bo=RwPZAEcD2QADByI!&rf=viewer_4)

![](http://m.qpic.cn/psb?/V12dDOqO2iwHZM/rhJwOOdJBDJSRWdVm8WQJoP*Vr.25ZfplrVVtD5eARc!/b/dD4BAAAAAAAA&bo=pQKYAqUCmAIDFzI!&rf=viewer_4)

- 然后把你 hadoop/bin/目录下的 hadoop.dll文件复制到 C:\Windows\System32 目录下（需要管理员权限），之后重启电脑，win10这边的环境就配置好了
<br>

<font size="3dp" color="pink">&emsp;&emsp;配置好win10这边的环境之后就可以开始工程了。打开IDEA编译器创建Maven工程（具体操作不作演示），之后打开项目中的 pom.xml 文件，向里面填写配置信息添加依赖jar，如下：</font>

```
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.7.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.7.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.5</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-yarn</artifactId>
            <version>2.7.5</version>
        </dependency>
    </dependencies>
```

之后在 idea 工程包中的 resource 包中添加配置文件log4j.properties，主要是用于打印日志文件，具体配置信息如下：
```
###\u8BBE\u7F6E ###
log4j.rootLogger = info,stdout
###\u8F93\u51FA\u4FE1\u606F\u5230\u63A7\u5236\u62AC ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n
```
>上述配置操作引用 <https://blog.csdn.net/lovely_J/article/details/82656884>

<br>

<font size="3dp" color="pink">**&emsp;&emsp;注意：有的同学在完成上述操作之后运行时还是报以下错误**</font>
```
java.lang.UnsatisfiedLinkError: org.apache.hadoop.util.NativeCrc32.nativeComputeChunkedSumsByteArray(II[BI[BIILjava/lang/String;JZ).........
...
...
```

&emsp;&emsp;这是因为在$HADOOP_HOME/lib/native源码二进制包下，文件默认是32位，而在windows下运行需要64位格式，因此需要在网上找已经编译好的对应版本的64位native包，或者自己自行编译Hadoop源码，也可以得到64位native包。<br>
&emsp;&emsp;总之可以通过在程序运行前 添加 -Djava.library.path=$HADOOP_HOME/lib/native 参数到 VM_options 达到目的
![](http://m.qpic.cn/psb?/V12dDOqO2iwHZM/Paj.amgJlPah2wTYkJelbHxUKv1FrzHx7dmLpUbrREs!/b/dMUAAAAAAAAA&bo=QQVQA0EFUAMDByI!&rf=viewer_4)
该窗口打开方式：Run->Edit Configurations..
<br>
>关于异常的解决方法引用：<https://blog.csdn.net/Vector97/article/details/99695838>


<font size="3dp" color="pink">&emsp;&emsp;一切准备工作都做好之后就开始来对HDFS做一些简单的操作进行实验吧！</font><br>

直接上代码，首先创建一个类 HDFS_CRUD.java
```
package demo1;

import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.junit.Test;
import org.apache.hadoop.conf.Configuration;

import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

public class HDFS_CRUD {
    @Test
    /**
     * 初始化HDFS 配置信息
     */
    public void initHDFS() throws IOException {
        Configuration conf = new Configuration();   //创建Configuration对象
        FileSystem fs = FileSystem.get(conf);   //获取文件系统
        System.out.println(fs.toString());  //打印文件系统
    }
```
@Test是 junit中的工具，其实就是标有@Test的方法都会被执行类似于 main()方法，使用@Test 其实就是省去了创建test类的操作。initHDFS()方法是对HDFS操作前需要做的一些初始化工作

下面是 testCopyFromLocalFile()方法，HDFS上传文件操作
```
 @Test
    /**
     * HDFS 文件上传（测试参数优先级）
     */
    public void testCopyFromLocalFile() throws IOException, URISyntaxException, InterruptedException {
        //获取文件系统
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(new URI("hdfs://192.168.10.136:9000"), conf, "root");
        //上传文件
        fs.copyFromLocalFile(new Path("D:/test.txt"), new Path("/idea.txt"));
        //关闭资源
        fs.close();
        System.out.println("over");
    }
```
运行成功之后，到虚拟机中进行查看，根目录下已经有了 idea.txt文件，并且内容与本机中的文件 test.txt 相同<br>
![](http://m.qpic.cn/psb?/V12dDOqO2iwHZM/esgROSX.x5VIR4q0PS4A4na8TFgc1W8Rc7YV1mPXX30!/b/dMMAAAAAAAAA&bo=pQJrAaUCawEDByI!&rf=viewer_4)<br>

这是我们D盘中的文件 test.txt<br>
![](http://m.qpic.cn/psb?/V12dDOqO2iwHZM/MW5PTddiQScwJuvYnO4Ss3IrF0iTq4k4MYn0uThurU4!/b/dEkBAAAAAAAA&bo=BgN9AgYDfQIDFzI!&rf=viewer_4)<br>

这样我们也就成功地完成了在win10系统上远程对虚拟机的Hadoop集群进行简单的HDFS操作。<br>

接下来就看一下关于我们刚刚的操作在日志中是怎样呈现的，HDFS相关操作的日志在 "dfs.hadoop.dir" 目录下，我的目录是在/home/hadoop/dfs/，主机的话进入到 dfs.hadoop.dir/name/current中<br>
![](http://a4.qpic.cn/psb?/V12dDOqO2iwHZM/QXoYtDOnB78zV92wkhEdLMzvS2vP*4wynzYHzsUwacc!/b/dMMAAAAAAAAA&ek=1&kp=1&pt=0&bo=9AQmAfQEJgEDFzI!&tl=1&vuin=635661878&tm=1571302800&sce=60-2-2&rf=viewer_4)<br>

由于这个文件是二进制文件，我们无法进行查看，所以需要以下操作对其转换为xml文件，并存储在 ~/ 目录下，记为a.xml<br>
![](http://m.qpic.cn/psb?/V12dDOqO2iwHZM/zR7iKz.FZlKSvh7ms5yLbE.27*82ZQCOtDwyxFN55Bg!/b/dLgAAAAAAAAA&bo=HwOTAB8DkwADFzI!&rf=viewer_4)

这样我们就可以查看日志文件了，
```
<EDITS>
  <EDITS_VERSION>-63</EDITS_VERSION>
  <RECORD>
    <OPCODE>OP_START_LOG_SEGMENT</OPCODE>
    <DATA>
      <TXID>407</TXID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_ADD</OPCODE>
    <DATA>
      <TXID>408</TXID>
      <LENGTH>0</LENGTH>
      <INODEID>16472</INODEID>
      <PATH>/idea.txt</PATH>
      <REPLICATION>3</REPLICATION>
      <MTIME>1571298225677</MTIME>
      <ATIME>1571298225677</ATIME>
      <BLOCKSIZE>134217728</BLOCKSIZE>
      <CLIENT_NAME>DFSClient_NONMAPREDUCE_1219465374_1</CLIENT_NAME>
      <CLIENT_MACHINE>192.168.10.1</CLIENT_MACHINE>
      <OVERWRITE>true</OVERWRITE>
      <PERMISSION_STATUS>
        <USERNAME>root</USERNAME>
        <GROUPNAME>supergroup</GROUPNAME>
        <MODE>420</MODE>
      </PERMISSION_STATUS>
      <RPC_CLIENTID>782a2f7e-3af5-41d5-926e-c083827173d8</RPC_CLIENTID>
      <RPC_CALLID>1</RPC_CALLID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_ADD</OPCODE>
    <DATA>
      <TXID>409</TXID>
      <LENGTH>0</LENGTH>
      <INODEID>16473</INODEID>
      <PATH>/idea.txt</PATH>
      <REPLICATION>3</REPLICATION>
      <MTIME>1571298423240</MTIME>
      <ATIME>1571298423240</ATIME>
      <BLOCKSIZE>134217728</BLOCKSIZE>
      <CLIENT_NAME>DFSClient_NONMAPREDUCE_2056027464_1</CLIENT_NAME>
      <CLIENT_MACHINE>192.168.10.1</CLIENT_MACHINE>
      <OVERWRITE>true</OVERWRITE>
      <PERMISSION_STATUS>
        <USERNAME>root</USERNAME>
        <GROUPNAME>supergroup</GROUPNAME>
        <MODE>420</MODE>
      </PERMISSION_STATUS>
      <RPC_CLIENTID>ee078fde-a771-47ac-a70e-7cb85e4260cb</RPC_CLIENTID>
      <RPC_CALLID>2</RPC_CALLID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_ADD</OPCODE>
    <DATA>
      <TXID>410</TXID>
      <LENGTH>0</LENGTH>
      <INODEID>16474</INODEID>
      <PATH>/idea.txt</PATH>
      <REPLICATION>3</REPLICATION>
      <MTIME>1571298532809</MTIME>
      <ATIME>1571298532809</ATIME>
      <BLOCKSIZE>134217728</BLOCKSIZE>
      <CLIENT_NAME>DFSClient_NONMAPREDUCE_13868159_1</CLIENT_NAME>
      <CLIENT_MACHINE>192.168.10.1</CLIENT_MACHINE>
      <OVERWRITE>true</OVERWRITE>
      <PERMISSION_STATUS>
        <USERNAME>root</USERNAME>
        <GROUPNAME>supergroup</GROUPNAME>
        <MODE>420</MODE>
      </PERMISSION_STATUS>
      <RPC_CLIENTID>8916926d-393c-485d-ac98-e2e17752950b</RPC_CLIENTID>
      <RPC_CALLID>2</RPC_CALLID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_ADD</OPCODE>
    <DATA>
      <TXID>411</TXID>
      <LENGTH>0</LENGTH>
      <INODEID>16475</INODEID>
      <PATH>/idea.txt</PATH>
      <REPLICATION>3</REPLICATION>
      <MTIME>1571298659215</MTIME>
      <ATIME>1571298659215</ATIME>
      <BLOCKSIZE>134217728</BLOCKSIZE>
      <CLIENT_NAME>DFSClient_NONMAPREDUCE_2087480298_1</CLIENT_NAME>
      <CLIENT_MACHINE>192.168.10.1</CLIENT_MACHINE>
      <OVERWRITE>true</OVERWRITE>
      <PERMISSION_STATUS>
        <USERNAME>root</USERNAME>
        <GROUPNAME>supergroup</GROUPNAME>
        <MODE>420</MODE>
      </PERMISSION_STATUS>
      <RPC_CLIENTID>c230c617-1d35-4851-8e91-9df269860f00</RPC_CLIENTID>
      <RPC_CALLID>2</RPC_CALLID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_ALLOCATE_BLOCK_ID</OPCODE>
    <DATA>
      <TXID>412</TXID>
      <BLOCK_ID>1073741872</BLOCK_ID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_SET_GENSTAMP_V2</OPCODE>
    <DATA>
      <TXID>413</TXID>
      <GENSTAMPV2>1048</GENSTAMPV2>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_ADD_BLOCK</OPCODE>
    <DATA>
      <TXID>414</TXID>
      <PATH>/idea.txt</PATH>
      <BLOCK>
        <BLOCK_ID>1073741872</BLOCK_ID>
        <NUM_BYTES>0</NUM_BYTES>
        <GENSTAMP>1048</GENSTAMP>
      </BLOCK>
      <RPC_CLIENTID></RPC_CLIENTID>
      <RPC_CALLID>-2</RPC_CALLID>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_CLOSE</OPCODE>
    <DATA>
      <TXID>415</TXID>
      <LENGTH>0</LENGTH>
      <INODEID>0</INODEID>
      <PATH>/idea.txt</PATH>
      <REPLICATION>3</REPLICATION>
      <MTIME>1571298659371</MTIME>
      <ATIME>1571298659215</ATIME>
      <BLOCKSIZE>134217728</BLOCKSIZE>
      <CLIENT_NAME></CLIENT_NAME>
      <CLIENT_MACHINE></CLIENT_MACHINE>
      <OVERWRITE>false</OVERWRITE>
      <BLOCK>
        <BLOCK_ID>1073741872</BLOCK_ID>
        <NUM_BYTES>84</NUM_BYTES>
        <GENSTAMP>1048</GENSTAMP>
      </BLOCK>
      <PERMISSION_STATUS>
        <USERNAME>root</USERNAME>
        <GROUPNAME>supergroup</GROUPNAME>
        <MODE>420</MODE>
      </PERMISSION_STATUS>
    </DATA>
  </RECORD>
  <RECORD>
    <OPCODE>OP_MKDIR</OPCODE>
    <DATA>
      <TXID>416</TXID>
      <LENGTH>0</LENGTH>
      <INODEID>16476</INODEID>
      <PATH>/mytest1</PATH>
      <TIMESTAMP>1571299107446</TIMESTAMP>
      <PERMISSION_STATUS>
        <USERNAME>root</USERNAME>
        <GROUPNAME>supergroup</GROUPNAME>
        <MODE>493</MODE>
      </PERMISSION_STATUS>
    </DATA>
  </RECORD>
</EDITS>
```
从日志文件中可以看出标签<EDITS>中是日志所有的内容，<RECODE>标签是每一项记录


&emsp;&emsp;这就是一个如何在win10系统上利用IDEA编译器进行远程操作虚拟机Hadoop集群的HDFS的简单案例
