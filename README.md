# How to install Hadoop on Windows


Hello everyone, today we are going to to install Hadoop 3.3.0 on Windows 11.

## Prerequisites

1. Java 8 runtime environment (JRE) 
2. Apache Hadoop 3.3.0


### Step 1 - Download Hadoop binary package

The first step is to download Hadoop binaries from the official website. 
The binary package size is about 478M MB.

https://archive.apache.org/dist/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz


## Step 2 - Unpack the package
After finishing the file download, we should unpack the package using 7zip or command line.

We open our terminal, and we go to our root

cd \

we create this folder

mkdir Hadoop
Becuase I am installing Hadoop in folder Haddop  of my C drive (C:\Hadoop) 
then we copy the unziped file there

cd Downloads 

then run the following command to unzip:

tar -xvzf  hadoop-3.3.0.tar.gz
The command will take quite a few minutes as there are numerous files included and the latest version introduced many new features.

After the unzip command is completed, a new folder hadoop-3.3.0 is created under the destination folder. 


mv hadoop-3.3.0 C:\Hadoop\


## Step 3 - Install Hadoop native IO binary
Hadoop on Linux includes optional Native IO support. However Native IO is mandatory on Windows and without it you will not be able to get your installation working. The Windows native IO libraries are not included as part of Apache Hadoop release. Thus we need to build and install it.

infoThe following repository already pre-built Hadoop Windows native libraries:
https://github.com/kontext-tech/winutils 
warning These libraries are not signed and there is no guarantee that it is 100% safe. We use it purely for test&learn purpose. 
Download all the files in the following location and save them to the bin folder under Hadoop folder. 


https://github.com/ruslanmv/How-to-install-Hadoop-on-Windows/winutils/tree/master/hadoop-3.3.0/bin




## Step 4 - (Optional) Java JDK installation
Java JDK is required to run Hadoop. If you have not installed Java JDK, please install it.

You can install JDK 8 from the following page:

https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

Once you complete the installation, please run the following command in PowerShell or Git Bash to verify:


$ java -version
java version "1.8.0_161"
Java(TM) SE Runtime Environment (build 1.8.0_161-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.161-b12, mixed mode)
If you got error about 'cannot find java command or executable'. Don't worry we will resolve this in the following step.

Step 5 - Configure environment variables
Now we've downloaded and unpacked all the artefacts we need to configure two important environment variables.

Configure JAVA_HOME environment variable
As mentioned earlier, Hadoop requires Java and we need to configure JAVA_HOME environment variable (though it is not mandatory but I recommend it).

First, we need to find out the location of Java SDK. In my system, the path is: D:\Java\jdk1.8.0_161.



Your location can be different depends on where you install your JDK.

And then run the following command in the previous PowerShell window:

SETX JAVA_HOME "D:\Java\jdk1.8.0_161" 
Remember to quote the path especially if you have spaces in your JDK path.

infoYou can setup environment variable at system level by adding option /M however just in case you don't have access to change system variables, you can just set it up at user level.
The output looks like the following:



Configure HADOOP_HOME environment variable
Similarly we need to create a new environment variable for HADOOP_HOME using the following command. The path should be your extracted Hadoop folder. For my environment it is: F:\big-data\hadoop-3.3.0.

If you used PowerShell to download and if the window is still open, you can simply run the following command:

SETX HADOOP_HOME $dest_dir+"/hadoop-3.3.0"                        
The output looks like the following screenshot:



Alternatively, you can specify the full path:

SETX HADOOP_HOME "F:\big-data\hadoop-3.3.0"
Now you can also verify the two environment variables in the system:



Configure PATH environment variable
Once we finish setting up the above two environment variables, we need to add the bin folders to the PATH environment variable. 

If PATH environment exists in your system, you can also manually add the following two paths to it:

%JAVA_HOME%/bin
%HADOOP_HOME%/bin
Alternatively, you can run the following command to add them:

setx PATH "$env:PATH;$env:JAVA_HOME/bin;$env:HADOOP_HOME/bin"

If you don't have other user variables setup in the system, you can also directly add a Path environment variable that references others to make it short:



Close PowerShell window and open a new one and type winutils.exe directly to verify that our above steps are completed successfully:



You should also be able to run the following command:

hadoop -version
java version "1.8.0_161"
Java(TM) SE Runtime Environment (build 1.8.0_161-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.161-b12, mixed mode)
Step 6 - Configure Hadoop
Now we are ready to configure the most important part - Hadoop configurations which involves Core, YARN, MapReduce, HDFS configurations. 

Configure core site
Edit file core-site.xml in %HADOOP_HOME%\etc\hadoop folder. For my environment, the actual path is F:\big-data\hadoop-3.3.0\etc\hadoop.

Replace configuration element with the following:

<configuration>
   <property>
     <name>fs.default.name</name>
     <value>hdfs://0.0.0.0:19000</value>
   </property>
</configuration>
Configure HDFS
Edit file hdfs-site.xml in %HADOOP_HOME%\etc\hadoop folder. 

Before editing, please correct two folders in your system: one for namenode directory and another for data directory.  For my system, I created the following two sub folders:

C:\hadoop\hadoop-3.3.0\data\datanode
C:\hadoop\hadoop-3.3.0\data\namenode


Replace configuration element with the following (remember to replace the highlighted paths accordingly):

<configuration>
   <property>
     <name>dfs.replication</name>
     <value>1</value>
   </property>
   <property>
     <name>dfs.namenode.name.dir</name>
     <value>/hadoop/hadoop-3.3.0/data/namenode</value>
   </property>
   <property>
     <name>dfs.datanode.data.dir</name>
     <value>/hadoop/hadoop-3.3.0/data/datanode</value>
   </property>
</configuration>

In Hadoop 3, the property names are slightly different from previous version. Refer to the following official documentation to learn more about the configuration properties:

Hadoop 3.3.0 hdfs_default.xml

infoFor DFS replication we configure it as one as we are configuring just one single node. By default the value is 3.
infoThe directory configuration are not mandatory and by default it will use Hadoop temporary folder. For our tutorial purpose, I would recommend customise the values. 
Configure MapReduce and YARN site
Edit file mapred-site.xml in %HADOOP_HOME%\etc\hadoop folder. 

Replace configuration element with the following:

<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property> 
        <name>mapreduce.application.classpath</name>
        <value>%HADOOP_HOME%/share/hadoop/mapreduce/*,%HADOOP_HOME%/share/hadoop/mapreduce/lib/*,%HADOOP_HOME%/share/hadoop/common/*,%HADOOP_HOME%/share/hadoop/common/lib/*,%HADOOP_HOME%/share/hadoop/yarn/*,%HADOOP_HOME%/share/hadoop/yarn/lib/*,%HADOOP_HOME%/share/hadoop/hdfs/*,%HADOOP_HOME%/share/hadoop/hdfs/lib/*</value>
    </property>
</configuration>


Edit file yarn-site.xml in %HADOOP_HOME%\etc\hadoop folder. 

<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>



Step 7 - Initialise HDFS 
Run the following command in Command Prompt 

hdfs namenode -format
The following is an example when it is formatted successfully:


Step 8 - Start HDFS daemons 
Run the following command to start HDFS daemons in Command Prompt:

%HADOOP_HOME%\sbin\start-dfs.cmd
Two Command Prompt windows will open: one for datanode and another for namenode as the following screenshot shows:

Verify HDFS web portal UI through this link: http://localhost:9870/dfshealth.html#tab-overview.


You can also navigate to a data node UI:



Step 9 - Start YARN daemons
warning You may encounter permission issues if you start YARN daemons using normal user. To ensure you don't encounter any issues. Please open a Command Prompt window using Run as administrator.
Alternatively, you can follow this comment on this page which doesn't require Administrator permission using a local Windows account:
Run the following command in an elevated Command Prompt window (Run as administrator) to start YARN daemons:

%HADOOP_HOME%\sbin\start-yarn.cmd
Similarly two Command Prompt windows will open: one for resource manager and another for node manager as the following screenshot shows:


You can verify YARN resource manager UI when all services are started successfully. 

http://localhost:8088


