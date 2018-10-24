# HadoopOnWindows10

Apache Hadoop 2.7.1 installation on Windows 10 Home Machine using command line
I am writing this blog to share my experience and steps I have followed to setup Hadoop version 2.7.1 as Psudo-Distributed on a personal laptop with windows 10 Home(64 bit) operating system.It is a Single cluster with Namenode,Datanode and Yarn running on same machine.

Required software  :
 - Java version 1.8 64-bit 
 - Apache Hadoop 2.7.1 (Stable release)
 - MySql Database (Note : not required for Hadoop but required for other hadoop related tools)

Memory requirements :
 - RAM : Minimum 8 GB
 - Hard Disk : Minimum 50 GB

Installation :

Java Installation : 
 Follow java installation wizard to install JDK and JRE.Make sure your java install directory looks like as shown below.

Hadoop Installation : Go to Command prompt and type command as "bash".
 You will see Unix like console as shown below.(Yes ,no cygwin installation is required anymore as Unix like console is provided in Windows10 which is pre-installed cygwin).

 Go to the folder where Hadoop downloaded tar is available and type command "tar -xzf hadoop-2.7.1.tar.gz".This will unzip hadoop folder and place it in the same directory.

 Configuration : Systems Variables : Set JAVA_HOME,JRE_HOME and HADOOP_HOME is set as shown below :
 
 Append PATH variable with : ;C:\Hitesh\Hadoop\hadoop-2.7.1\bin;C:\java\jdk1.8.0_92;C:\java\jre1.8.0_92\bin
 Hadoop Configuration :
 Hadoop-env.cmd
 Go to HADOOP_HOME/etc/hadoop
 Open hadoop-env.cmd file into notepad
 add "set JAVA_HOME=C:\Java\jdk1.8.0_92" at Line number 26 as shown below.

 add following lines at the end of the file :
 set HADOOP_PREFIX=C:\Hitesh\Hadoop\hadoop-2.7.1
 set HADOOP_CONF_DIR=%HADOOP_PREFIX%\etc\hadoop
 set YARN_CONF_DIR=%HADOOP_CONF_DIR%
 set PATH=%PATH%;%HADOOP_PREFIX%\bin

 You can refer sample file at :hadoop-env.cmd
 Core-site.xml
 Go to HADOOP_HOME/etc/hadoop
 Open core-site.xml file into notepad
 Add property as shown below :

 What is this file :Specifies the filesystem settings for Hadoop commands.
 Property Name :fs.defaultFS
 value : hdfs://localhost:19000
 Short Description: The name of the default file system and access port.
 Property Name :hadoop.proxyuser.<<WINUSER>>.groups
 value :*
 
 Short Description:This property sets groups that <<WINUSER>> can impersonate a user belonging to the group provided in value while connecting HDFS.You can specify "*" for least security or specific hdfs user group name as shown above.

 Property Name :hadoop.proxyuser.<<WINUSER>>.hosts
 Value :127.0.0.1,192.168.55.3

 Short Description:This property lists hosts from whre <<WINUSER>> can is allowed connect to HDFS and perform tasks.This property is required to be set when you need to connect to hadoop from Hive,Pig or Sqoop like applications.You can specify "*" for least security or specific IP address as shown above.

 Property Name :hadoop.proxyuser.<<WINUSER>>.users
 value :<<WINUSER>>

 Short Description:This property lists users other then hdfs users who can connect to HDFS same as users listed in value list.This property is required when you execute hive,oozie or sqoop jobs even from same machine and user.

You can refer sample file at :core-site.xml
<<WINUSER>> is windows user(s) from which you wants to connect and execute hadoop/hdfs or mapreduce commands.In my case it was same user.

 Mapred-site.xml
 Go to HADOOP_HOME/etc/hadoop
 Open mapred-site.xml file into notepad
 Add following lines :

 What is this file :This file is required to run mapreduce applications on hadoop.This file contains configuration information that overrides the default values for MapReduce parameters. 

 Property Name :mapreduce.framework.name 
 Value : yarn
 Short Description:This property decides where to execute mapreduce application.valid values are yarn,local or classic.
 Property Name :mapreduce.jobtracker.address
 Value : local
 Short Description:The host and port that the MapReduce job tracker runs at."local" is a default value and all jobs are run in-process as a single map and reduce task.
 Property Name :mapreduce.map.memory.mb
 Value : 512
 Description:This property is used to define upper limit in MB that Haddop allocates to maper while executing any mapreduce application out of total RAM.With Total RAM of 8 GB keep this value equal to 512 or less.
 Property Name :mapreduce.reduce.memory.mb
 Value : 512
 Description:This property is used to define upper limit in MB that Haddop allocates to reducer while executing any mapreduce application out of total RAM.With Total RAM of 8 GB keep this value equal to 512 or less.
 Property Name :mapreduce.map.java.opts
 Value : -Xmx256m
 Description : This property is used to define upper limit for maper java process java heap size.Value of this parameter should be less than memory allocated in mapreduce.map.memory.mb so that java code can execute without outofmemory error.Atleast 20% to 30% less.
 Property Name :mapreduce.reduce.java.opts
 Value : -Xmx256m
 Description :Same as mapreduce.map.java.opts this property is used to define upper limit for reducer java process java heap size.Value of this parameter should be less than memory allocated in mapreduce.reduce.memory.mb so that java code can execute without out-of-memory error.Atleast 20% to 30% less.
 You can refer sample file at :mapred-site.xml
 Slaves File
 Go to HADOOP_HOME/etc/hadoop
 Edit or create Slaves file and write localhost in it.

 You can refer sample file at :slaves
 Yarn-site.xml
 Go to HADOOP_HOME/etc/hadoop
 Open Yarn-site.xml into notepad
 Add Following lines:

 What is this file : This file contains yarn configuration.
 Property Name :yarn.nodemanager.aux-services
 Value : mapreduce_shuffle
 Short Description: This property is for Nodemanager do see which aux-services needs to be used."mapreduce_shuffle" is the default value.
 Property Name :yarn.nodemanager.aux-services.mapreduce.shuffle.class
 Value : org.apache.hadoop.mapred.ShuffleHandler
 Short Description:This property is for Nodemanager to identify which class to use for shuffling when aux-services are set to "mapreduce_shuffle".
 Property Name :yarn.nodemanager.resource.memory-mb
 Value : 10000
 Short Description:This property is used to set Maximum Amount of physical memory, in MB, that can be allocated for containers.This is the maximum memory in MB which can be used by yarn to perform Hadoop operations.
 Property Name :yarn.scheduler.minimum-allocation-mb
 Value : 500
 Short Description:This property is used to set Minimum Amount of physical memory, in MB allocated to yarn.
 Property Name :yarn.application.classpath
 Value:%HADOOP_CONF_DIR%,%HADOOP_COMMON_HOME%/share/hadoop/common/*,%HADOOP_COMMON_HOME%/share/hadoop/common/lib/*,%HADOOP_HDFS_HOME%/share/hadoop/hdfs/*,%HADOOP_HDFS_HOME%/share/hadoop/hdfs/lib/*,%HADOOP_MAPRED_HOME%/share/hadoop/mapreduce/*,%HADOOP_MAPRED_HOME%/share/hadoop/mapreduce/lib/*,%HADOOP_YARN_HOME%/share/hadoop/yarn/*,%HADOOP_YARN_HOME%/share/hadoop/yarn/lib/*
 Short Description:Classpath for yarn.
 You can refer sample file at :yarn-site.xml
 Hdfs-site.xml
 Go to HADOOP_HOME/etc/hadoop
 Open hdfs-site.xml file into notepad
 Add following lines :

 What is this file :
 You can refer sample file at :hdfs-site.xml
 Other Files required for windows 10 :
 Hadoop needs windows OS specific files which does not come with default download of hadoop. You can build those files using instructions given in Building.txt or you can download from my git-hub repository location.
 Take backup of original BIN folder present in HADOOP_HOME and replace it with downloaded BIN folder.
 Starting Hadoop :
 Make sure that user account you have logged in with is having administrator privileges.
 Go to Run and type CMD to open command prompt.
 Right Click on Command Prompt Icon and select Run as administrator as shown below.It is very important to run command prompt as administrator since hadoop requires admin privileges for various tasks.

 Go to HADOOP_HOME/bin
 Execute command "hdfs namenode -format" as shown below.

 After above commands executes successfully,go to HADOOP_HOME/sbin folder
 Execute command start-dfs.cmd to start namenode and datanode. 
 This command will open two news windows to start namenode and datanode.

 Make sure namenode and datanode starts without any errors.
 Execute command start-yarn.cmd to start yarn (resource manager and node manager).
 Make sure yarn starts without any errors.

 Hadoop User Interface :
 Hadoop provides userinterface to check details about HDFS (namenode and datanode).
 By-default it runs on http://localhost:50070/
 This user-interface is useful to get information memory allocation for datanode,namenode overview as shown in below screenshots.


 This user-interface is also useful to explore HDFS file system to explore directories and files present HDFS.

 Yarn also provides different interface to access information about resource manager,current map-reduce jobs in process,faild or successfull jobs completed till date.
 By default it runs on http://localhost:8088/cluster/apps

 First Hadoop Operation :
 To make sure hadoop is configured as expected lets run some basic hdfs commands.
 Create one dummy text file "hadooptest.txt" and write some random lines into it.
 Open Command prompt  
 Type command hadoop "fs -mkdir hadooptest" to create HDFS directory.
 Make sure directory is created successfully by : "hadoop fs -ls"

 Copy hadooptest.txt file to newly created HDFS directory : "hadoop fs -copyFromLocal <<src>> hadooptest/"
 Make sure file is copied to hadoop : "hadoop fs -ls hadooptest/"

 Go to Hadoop user-interface ,new directory and file should come up into search results.

 Note that by default hadoop creates all the directories to /user/<<WINUSER>>/hadooptest hdfs directory.
