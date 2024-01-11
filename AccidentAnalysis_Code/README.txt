============
Inroduction
============
This Project is delivered to determine the answers for the following questions proposed for the given data set (UK Traffic Accident Data set between the year 2005 -2014) . 
********************************************************************************************************

1. To understand Which time dimension (day / time) has the highest accidents 
2. To explore what is the age group with least accidents 
3. To distinguish what is the overall fatality rate by each location and rank the same 
4. To understand the detail fatality / casualty dimension such as identifying the density of gender wise / age wise victims. 5. On the positive side, we could also look at the number of survivors and less injured

********************************************************************************************************

Please follow the following steps to Run the code provided ,
1) Step 1 : Create host environment for installing the Hadoop , it can be using EC2 VMs built on ubuntu from AWS and enabling the ssh , after logging into the VM and creating a hadoop user please follow the below steps for Hadoop Installation .
	-----------------------------------------------------------------
			Hadoop-2 Single Node Cluster Creation Installation
	-----------------------------------------------------------------
1. Download Hadoop and Java
download the Latest hadoop file using the below 
curl -O https://archive.apache.org/dist/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz

go the location where the file is downloaded ,

#Extract the file
tar -zxvf hadoop-2.9.1.tar.gz (Extract the tar file)

#Install update and Default Java
sudo apt update 
sudo apt install default-jre 
sudo apt install default-jdk

#Install VIM editor using the below ,
sudo apt-get install vim (Install USER Friendly Editer)

#Identify the java path installed using
whereis Java
#Edit Bash source file with the installed path 
vi .bashrc (Set the java Path in your Home Path)

export JAVA_HOME=<path obtiained from whereis java>
export PATH=HOME/bin:JAVA_HOME/bin:PATH
#apply changes using the below ,
source .bashrc (Execute the bashrc file)
echo JAVA_HOME (Check the java path)

===============================================================================================================================


2. Modify Hadoop Configuration Files
NAMENODE ----> core-site.xml
RESOURCE MANGER ----> mapperd-site.xml
SECONDARYNAMENODE ---->
DATANODE ----> slaves
NODEMANGER ----> slaves & yarn-site.xml


 vi etc/hadoop/core-site.xml

<property>
<name>fs.default.name</name>
<value>hdfs://localhost:50000</value>
</property>

 vi etc/hadoop/yarn-site.xml

<property>
<name>yarn.nodemanager.aux-services</name> <value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name> <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
<description>The hostname of the RM.</description>
<name>yarn.resourcemanager.hostname</name>
<value>localhost</value>
</property>
<property>
<description>The address of the applications manager interface in the RM.</description>
<name>yarn.resourcemanager.address</name>
<value>localhost:8032</value>
</property>

 vi etc/hadoop/hdfs-site.xml

<property>
<name>dfs.namenode.name.dir</name>
<value>/home/<username>/hadoop-3.3.4/namenode-dir</value>
</property>
<property>
<name>dfs.datanode.data.dir</name>
<value>/home/<username>/hadoop-3.3.4/datanode-dir</value>
</property>

 vi etc/hadoop/mapred-site.xml

<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
<property>
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=/home/<username>/hadoop-3.3.4</value>
</property>
<property>
<name>mapreduce.map.env</name>
<value>HADOOP_MAPRED_HOME=/home/<username>/hadoop-3.3.4</value>
</property>
<property>
<name>mapreduce.reduce.env</name>
<value>HADOOP_MAPRED_HOME=/home/<username>/hadoop-3.3.4</value>
</property>

 vi etc/hadoop/hadoop-env.sh
export JAVA_HOME==<path obtiained from whereis java>

 vi etc/hadoop/mapred-env.sh
export JAVA_HOME==<path obtiained from whereis java>

 vi etc/hadoop/yarn-env.sh
export JAVA_HOME==<path obtiained from whereis java>

 vi etc/hadoop/slaves
localhost

Install the ssh key
(Generates, Manages and Converts Authentication keys)
 sudo apt-get install openssh-server
 ssh-keygen -t rsa
(Setup passwordless ssh to localhost and to slaves )
 cd .ssh
 ls
 cat id_rsa.pub >> authorized_keys (copy the .pub)
(Copy the id_rsa.pub from NameNode to authorized_keys in all machines)
 ssh localhost
(Asking No Password )

===============================================================================================================================


3. Format NameNode
 cd hadoop-3.3.4
 bin/hadoop namenode -format (Your Hadoop File System Ready)

===============================================================================================================================

4. Start All Hadoop Related Services
 sbin/start-all.sh
(Starting Daemon’s For DFS & YARN)
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager


(check the Browser Web GUI )
NameNode - http://localhost:50070/
Resource Manager - http://localhost:8088/

===============================================================================================================================
For running the Q1-5 please follow the followings steps ,
****Please note that you need to create a hadoop inbound and outbound hadoop directory****
bin/hadoop fs -mkdir /uk_accident_1/

#Copy the data set to your local machine or ec2 VM and then copy to the hadoop file system using the below command
bin/hadoop fs -put <path of data set file including file name> /uk_accident_1/

#Output Dir will be created automatically by hadoop node on execution

Q1.1- Yearly - Accident Count 
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/accidentyearWise.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000
Q1.2 - Monthly - Accident Count
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/accidentMonthWise.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000
Q1.3 - WeekDay - Accident Count
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/topaccidentweekday.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000
Q1.4 - Hour - Highest Accident Count
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/accidentHourWise.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000

Q2 - Accident Distribution by Age Group
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/agegroupaccident.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000

Q3 - Fatality Rate Distribution 
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/Q3_accidenttype_severity.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000

Q4 - Gender Wise Fatality
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/Q4_accidentfatalitygenderwise.jar /uk_accident_1/ /<customName>/1
#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000

Q5 - Severity - High/Low Count - topten
#Run the respective jar file using the following command 
hadoop jar <Jar file path>/Q5_topten_highest_casualty_modified.jar /uk_accident_1/ /<customName>/1

#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000
hadoop jar <Jar file path>/Q5_bottomten_04122022.jar /uk_accident_1/ /<customName>/1

#Check the output file using the below command
bin/hadoop fs -cat <customName>/1/part-r-00000

5.Stop All Hadoop and Yarn Related Services
 sbin/stop-all.sh