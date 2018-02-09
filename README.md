# ANLY502 - Massive Data Fundamentals<br>Lab: Hadoop, HDFS, Amazon Elastic MapReduce<br>February 5, 2018

## Start an Amazon EMR Cluster

1. Start an Amazon Elastic MapReduce (EMR) Cluster using Quickstart with the follwing setup (as shown in the picture):
	*  Give the cluster a name that is meaningful to you
	*  Use Release `emr-5.11.1`
	*  Select the first option under Applications
	*  Select 1 master and 2 core nodes, using `m4.large` instance types
	*  Select your correct EC2 keypair or you will not be able to connect to the cluster
	*  Click **Create Cluster**

	<img src='images/emr-02-create-cluster-quick-options.png'><br>
	As soon as you create the cluster, you will see the cluster in "Starting" mode:
	
	<img src='images/emr-03-cluster-starting.png'>	

2. **You only need to do this once:** Once the cluster is in "Waiting" mode, click on the **Security Groups for Master** which will take you to the Security Group configuration page, and add port 22 like you did in the homework Tutorial (Follow next three screenshots)

	<img src='images/emr-04-cluster-waiting.png'><br>
	<img src='images/emr-05-edit-master-security-group.png'><br>
	<img src='images/emr-06-add-ssh.png'><br>
	
	
## SSH Into the Cluster

```
ssh hadoop@[[master-node-dns-name]]
```
Note the usernanme is **`hadoop`**. Get your cluster's master node IP address from the Cluster console.

You will see something like this:

```
morocoto ➜  ~  ssh hadoop@ec2-34-230-63-109.compute-1.amazonaws.com
Last login: Mon Feb  5 22:35:02 2018

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2017.09-release-notes/
8 package(s) needed for security, out of 9 available
Run "sudo yum update" to apply all updates.

EEEEEEEEEEEEEEEEEEEE MMMMMMMM           MMMMMMMM RRRRRRRRRRRRRRR
E::::::::::::::::::E M:::::::M         M:::::::M R::::::::::::::R
EE:::::EEEEEEEEE:::E M::::::::M       M::::::::M R:::::RRRRRR:::::R
  E::::E       EEEEE M:::::::::M     M:::::::::M RR::::R      R::::R
  E::::E             M::::::M:::M   M:::M::::::M   R:::R      R::::R
  E:::::EEEEEEEEEE   M:::::M M:::M M:::M M:::::M   R:::RRRRRR:::::R
  E::::::::::::::E   M:::::M  M:::M:::M  M:::::M   R:::::::::::RR
  E:::::EEEEEEEEEE   M:::::M   M:::::M   M:::::M   R:::RRRRRR::::R
  E::::E             M:::::M    M:::M    M:::::M   R:::R      R::::R
  E::::E       EEEEE M:::::M     MMM     M:::::M   R:::R      R::::R
EE:::::EEEEEEEE::::E M:::::M             M:::::M   R:::R      R::::R
E::::::::::::::::::E M:::::M             M:::::M RR::::R      R::::R
EEEEEEEEEEEEEEEEEEEE MMMMMMM             MMMMMMM RRRRRRR      RRRRRR

[hadoop@ip-172-31-25-210 ~]$
```

## Create your own S3 Bucket

You will create an S3 bucket of your own. EMR clusters can read/write data from the cluster's HDFS as well as S3 buckets. Remember, your HDFS storage gets deleted when you shut down a cluster. If you want to keep data files, you can save them to S3.

**Make sure your region is N. Virgina**

1. Go to the S3 Console
	<img src='images/s3-01-service.png'><br>
	
2. Click "Create Bucket"	
	<img src='images/s3-02-create-bucket.png'><br>

3. Enter a **globally unique bucket name.** Use a combination of letters, dashes and numbers. This name must me unique across all S3 buckets on Amazon. After the first screen, you can keep the defaults. Click Next 3 times and then "Create Bucket".
	<img src='images/s3-03-name-bucket.png'><br>


## Run some HDFS commands

The reference guide for all the HDFS Shell commands is here: [https://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/FileSystemShell.html)

1. List files in your newly created cluster's HDFS (will be empty):
	`hdfs dfs -ls` or `hadoop fs -ls`
	
2. 	List files in a public S3 bucket owned by Prof. Vaisman, created for this course: 	`hdfs dfs -ls s3://bigdatateaching/`

	```
	[hadoop@ip-172-31-25-210 ~]$ hadoop fs -ls s3://bigdatateaching/
	Found 11 items
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/criteo
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/forensicswiki
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/graphx
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/ham
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/internet-census-2012
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/meta
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/ncdc
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/nyctaxi
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/quazyilx
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/shakespeare
	drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://bigdatateaching/spam
	[hadoop@ip-172-31-25-210 ~]$
	```
	
3. Copy a file from the course's S3 public bucket **to your personal S3 bucket**
	`hadoop fs -cp s3://bigdatateaching/shakespeare/100-0.txt s3://[[your-bucket-name]]/`

	**Note: this file exists until you delete it**

	Now you can see list the contents of **your** S3 bucket: `hadoop fs -ls s3://[[your-bucket-name]]`

4. Copy a file from the course's S3 public bucket **the cluster's HDFS**

	`hadoop fs -cp s3://bigdatateaching/shakespeare/100-0.txt ./`
	
	In the previous command, you did almost the same as in step 3. However, the destination is different. Notice that the destination **does not** have the `s3://`prefix. Anytime you use the HDFS shell commands, and the source/location do not have `s3://` you are working with the cluster's HDFS.
	
	You can now list the contents of your cluster's HDFS:
	`hadoop fs -ls`

	```
	Found 1 items
	-rw-r--r--   1 hadoop hadoop    5856576 2018-02-09 00:57 100-0.txt
	```

## Run a Hadoop job

You will run a Hadoop job using a built-in example that comes with Hadoop. It is the Word Count. You will issue a command that will start the job with a specified `.jar` (Java Archive) file and pass in some parameters. You will use the `100-0.txt` file in HDFS as the input, and you will specify the output to your S3 bucket.

`hadoop jar /usr/lib/hadoop-mapreduce/hadoop-mapreduce-examples.jar wordcount 100-0.txt s3://[[your-s3-bucket]]/shakespearewordcount`

You should see something like this:

```
18/02/09 01:03:02 INFO impl.TimelineClientImpl: Timeline service address: http://ip-172-31-15-90.ec2.internal:8188/ws/v1/timeline/
18/02/09 01:03:02 INFO client.RMProxy: Connecting to ResourceManager at ip-172-31-15-90.ec2.internal/172.31.15.90:8032
18/02/09 01:03:03 INFO input.FileInputFormat: Total input paths to process : 1
18/02/09 01:03:03 INFO lzo.GPLNativeCodeLoader: Loaded native gpl library
18/02/09 01:03:03 INFO lzo.LzoCodec: Successfully loaded & initialized native-lzo library [hadoop-lzo rev fc548a0642e795113789414490c9e59e6a8b91e4]
18/02/09 01:03:03 INFO mapreduce.JobSubmitter: number of splits:1
18/02/09 01:03:03 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1518130868569_0001
18/02/09 01:03:04 INFO impl.YarnClientImpl: Submitted application application_1518130868569_0001
18/02/09 01:03:04 INFO mapreduce.Job: The url to track the job: http://ip-172-31-15-90.ec2.internal:20888/proxy/application_1518130868569_0001/
18/02/09 01:03:04 INFO mapreduce.Job: Running job: job_1518130868569_0001
18/02/09 01:03:15 INFO mapreduce.Job: Job job_1518130868569_0001 running in uber mode : false
18/02/09 01:03:15 INFO mapreduce.Job:  map 0% reduce 0%
18/02/09 01:03:27 INFO mapreduce.Job:  map 100% reduce 0%
18/02/09 01:03:36 INFO mapreduce.Job:  map 100% reduce 33%
18/02/09 01:03:40 INFO mapreduce.Job:  map 100% reduce 67%
18/02/09 01:03:41 INFO mapreduce.Job:  map 100% reduce 100%
18/02/09 01:03:42 INFO mapreduce.Job: Job job_1518130868569_0001 completed successfully
18/02/09 01:03:43 INFO mapreduce.Job: Counters: 55
	File System Counters
		FILE: Number of bytes read=599117
		FILE: Number of bytes written=1706110
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=5856703
		HDFS: Number of bytes written=0
		HDFS: Number of read operations=2
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=0
		S3: Number of bytes read=0
		S3: Number of bytes written=817344
		S3: Number of read operations=0
		S3: Number of large read operations=0
		S3: Number of write operations=0
	Job Counters
		Killed reduce tasks=1
		Launched map tasks=1
		Launched reduce tasks=3
		Data-local map tasks=1
		Total time spent by all maps in occupied slots (ms)=427584
		Total time spent by all reduces in occupied slots (ms)=2740224
		Total time spent by all map tasks (ms)=8908
		Total time spent by all reduce tasks (ms)=28544
		Total vcore-milliseconds taken by all map tasks=8908
		Total vcore-milliseconds taken by all reduce tasks=28544
		Total megabyte-milliseconds taken by all map tasks=13682688
		Total megabyte-milliseconds taken by all reduce tasks=87687168
	Map-Reduce Framework
		Map input records=147838
		Map output records=959301
		Map output bytes=9117121
		Map output materialized bytes=599130
		Input split bytes=127
		Combine input records=959301
		Combine output records=76169
		Reduce input groups=76169
		Reduce shuffle bytes=599130
		Reduce input records=76169
		Reduce output records=76169
		Spilled Records=152338
		Shuffled Maps =3
		Failed Shuffles=0
		Merged Map outputs=3
		GC time elapsed (ms)=1227
		CPU time spent (ms)=8900
		Physical memory (bytes) snapshot=1666109440
		Virtual memory (bytes) snapshot=17266147328
		Total committed heap usage (bytes)=1656225792
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters
		Bytes Read=5856576
	File Output Format Counters
		Bytes Written=817344
```

After the job is complete, you can see the results files in your S3 bucket:

`hadoop fs -ls s3://[[your-s3-bucket]]/`

```
Found 2 items
-rw-rw-rw-   1 hadoop hadoop    5856576 2018-02-05 22:55 s3://marckvaisman-anly502/100-0.txt
drwxrwxrwx   - hadoop hadoop          0 1970-01-01 00:00 s3://marckvaisman-anly502/shakespearewordcount
```
You'll see the text file you copied earlier, and the directory created by the Hadoop job.

You can see the contents inside that directory:

```
Found 4 items
-rw-rw-rw-   1 hadoop hadoop          0 2018-02-09 01:03 s3://marckvaisman-anly502/shakespearewordcount/_SUCCESS
-rw-rw-rw-   1 hadoop hadoop     273328 2018-02-09 01:03 s3://marckvaisman-anly502/shakespearewordcount/part-r-00000
-rw-rw-rw-   1 hadoop hadoop     270439 2018-02-09 01:03 s3://marckvaisman-anly502/shakespearewordcount/part-r-00001
-rw-rw-rw-   1 hadoop hadoop     273577 2018-02-09 01:03 s3://marckvaisman-anly502/shakespearewordcount/part-r-00002
```

Each `part-r-xxxxx` file is the result of a reduce process. 

To see the contents **inside** a file (the first 5 lines), you can use another command:

`hadoop fs -cat s3://[[your-s3-bucket]]/shakespearewordcount/part-r-00000 | head`

```
18/02/09 01:09:41 INFO s3n.S3NativeFileSystem: Opening 's3://marckvaisman-anly502/shakespearewordcount/part-r-00000' for reading
"'Tis	1
"Amen,"	1
"Brutus"	1
"Caesar"?	1
"Come	1
"Fear	2
"Fly,	1
"Gentle	1
"Here	1
"I	4
18/02/09 01:09:41 WARN internal.S3AbortableInputStream: Not all bytes were read from the S3ObjectInputStream, aborting HTTP connection. This is likely an error and may result in sub-optimal behavior. Request only the bytes you need via a ranged GET or drain the input stream after use.
cat: Unable to write to output stream.
```






