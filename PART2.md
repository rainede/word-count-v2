# README

You can browse the contents of a remote folder through the `$ hadoop fs –ls` command, which is similar to the Unix listing command.

 

You should only worry about two folders of the filesystem.

The /data folder is the destination for large datasets to be processed.  You can have a look at it by invoking the following command: (There should be some files, including a 35 GB Wikipedia dump

`$ hadoop fs -ls /data`

 
##bWORDCOUNT EXECUTION IN THE HADOOP CLUSTER
 

We will now perform the required steps to run the WordCount v1 program we created last week in our Hadoop cluster. However, we will first use the small input dataset that we used last week.

First, we need to create a folder in our HDFS personal space to store the input data. That operation can be solved with the –mkdir option:

`$ hadoop fs –mkdir input`

If you browse now the contents of your folder you should see the newly created folder.

`$ hadoop fs -ls`

Now, let’s copy the text file to the HDFS. File transfer from a local filesystem to Hadoop is perform using the –copyFromLocal command:

`$ hadoop fs -copyFromLocal input/sherlock.txt input`

If you now check the contents of the input folder in HDFS you should see the file

`$ hadoop fs –ls input`

Once your data is copied into HDFS, you can run your previously defined MapReduce job there now. Keep in mind that input and output paths will be resolved into the HDFS instead of the local file system on the computer you are using.

`$ hadoop jar dist/WordCount.jar WordCount input out`

You should see the usual Hadoop log if the paths you have provided for the input and jar file are correct. Finally, once the process is complete copy the results file back to your local filesystem by using the fs –copyToLocal option. Remember that Reducer#1 output is provided in a file called part-00000

`$ hadoop fs –copyToLocal <hdfsfile> <localdestination>`

After retrieving the output you can remove the output folder through the –rmr fs command. If the designated output folder was out, you can remove it by doing:

`$ hadoop fs –rm -r out`

How many Mappers and Reducers have been involved in the work?

You can change the number of Reducers that get involved in the job by manually configuring it as part of the job configuration (WordCount.java). Try to add this line to the runJob method of WordCount
```java
 job.setNumReduceTasks(3);
 ```

Repackage the Hadoop program using Ant, and run it again on the cluster. There should be now more part-0000x files in the output folder (one per reducer). Have a look at those files (you can either copy them to your local folder, or quickly dump their contents using the hadoop fs –cat command.  

Can you see a clear pattern on how Hadoop partitions the keys among multiple reducers? Does it make easier or harder now the problem of manually retrieving information about a specific key? E.g. How many times the word Sherlock appears in the text?

Actually Hadoop has a very useful HDFS command that retrieves all files from a remote folder and merges them into a single file in the local file system on the computer you are using.. In order to locally aggregate all three files invoke the following command:

`$ hadoop fs –getmerge out count.txt`

 

We are going to use now a larger dataset in order to take advantage of the cluster. We will use the folder /data/gutenberg where a larger dataset is already loaded. This folder contains a prepacked file with multiple books downloaded from project Gutenberg, written in different languages.

Invoke again the MapReduce job, this time setting the input path to 

/data/gutenberg

 This is going to take a bit more to execute, as you are processing a file of size 400MB. 

You can obtain realtime information about the progress of your job by going to the web ui of the hadoop cluster 

http://studoop.eecs.qmul.ac.uk:8088

You will see a list of running and complete jobs. Find your running job (the console will have displayed your job ID after the job started running) and click in the link of your specific job to access the details page with its current execution status. You can also access that link through the link that is shown in the console output when the job is accepted in the cluster.

Once the job finishes have a look at the output results provided by Hadoop (either through the job information web page, or through the output emitted by Hadoop in the console). There is a total of 24 base metrics providing details on how the parallel process went.

- How many Map tasks does your MapReduce job have? Can you explain the difference with the Sherlock job? How many are actually local mappers (i.e. they are initially collocated with the data they have to process)? How many Reduce tasks?

 

# ADDING A COMBINER
 

In this case it should be possible to significantly improve the overall MapReduce performance by adding a Combiner to the process. Add a Combiner to the Hadoop job by adding to the JobConf the following line:
```java
 job.setCombinerClass(NameOfTheCombiner.class);
```
 So we now have
```java
job.setMapperClass(TokenizerMapper.class);

job.setCombinerClass(IntSumReducer.class);

job.setReducerClass(IntSumReducer.class);
```
 

in the WordCount.java file.

Repackage the Hadoop program again (`$ ant clean dist`) and repeat the Hadoop execution over the same data.

Have you seen a noticeable improvement in performance? You can go to the web UI and retrieve the total execution time of both jobs in order to compare it.

In order to understand why it has improved, have a look at the Map output records, Combine input records, Combine output records and Reduce input records. Can you see the impact that defining a Combiner has with large jobs?

Finally, have a look at the results of your process. Can you improve the quality of your word counting program by detecting in your StringTokenizer filter some of the characters that appear at the start of the file? There are quite a few symbols which haven't been filtered out with the base filter. However, this approach needs to specify every single special character. Instead, you can run a regexp before splitting the file, in order to eliminate all non-alphabetic characters: 
```java
String line = value.toString().replaceAll("[^a-zA-Z\\s]", ""); 
```
The /data folder is the destination for large datasets to be processed.  You can have a look at it by invoking the following command: (There should be some files, including a 35 GB Wikipedia dump

`$ hadoop fs -ls /data`

 

Also, there is a folder created for personal use of each student. You can look at your personal folder (should be empty) by changing the path to:

`$ hadoop fs -ls`

Your folder is stored in the /user/<userid> folder of the HDFS, where your userid will be similar to ec09999, or abc123

The ITL Hadoop cluster will be shared by all students of Big Data Processing, so it is very important that you use it responsibly.

 YOU CANNOT WRITE IN ANY FOLDER THAT IS NOT YOUR PERSONAL FOLDER

IF YOU WANT TO COPY FILES INTO THE HDFS, COPY THEM INTO A SUBFOLDER OF YOUR USER FOLDER

 

# WORDCOUNT EXECUTION IN THE HADOOP CLUSTER
 

We will now perform the required steps to run the WordCount program we created last week in our Hadoop cluster. However, we will first use the small input dataset that we used last week.

First, we need to create a folder in our HDFS personal space to store the input data. That operation can be solved with the –mkdir option:

`$ hadoop fs –mkdir input`

If you browse now the contents of your folder you should see the newly created folder.

`$ hadoop fs -ls`

Now, let’s copy the text file to the HDFS. File transfer from a local filesystem to Hadoop is perform using the –copyFromLocal command:

`$ hadoop fs -copyFromLocal input/sherlock.txt input`

If you now check the contents of the input folder in HDFS you should see the file


`$ hadoop fs –ls input`

Once your data is copied into HDFS, you can run your previously defined MapReduce job there now. Keep in mind that input and output paths will be resolved into the HDFS instead of the local file system on the computer you are using.

`$ hadoop jar dist/WordCount.jar WordCount input out`

You should see the usual Hadoop log if the paths you have provided for the input and jar file are correct. Finally, once the process is complete copy the results file back to your local filesystem by using the fs –copyToLocal option. Remember that Reducer#1 output is provided in a file called part-00000


`$ hadoop fs –copyToLocal <hdfsfile> <localdestination>`

After retrieving the output you can remove the output folder through the –rmr fs command. If the designated output folder was out, you can remove it by doing:

`$ hadoop fs –rm -r out`

How many Mappers and Reducers have been involved in the work?

You can change the number of Reducers that get involved in the job by manually configuring it as part of the job configuration (WordCount.java). Try to add this line to the runJob method of WordCount
```java
 job.setNumReduceTasks(3);
```
Repackage the Hadoop program using Ant, and run it again on the cluster. There should be now more part-0000x files in the output folder (one per reducer). Have a look at those files (you can either copy them to your local folder, or quickly dump their contents using the `$ hadoop fs –cat` command.  

Can you see a clear pattern on how Hadoop partitions the keys among multiple reducers? Does it make easier or harder now the problem of manually retrieving information about a specific key? E.g. How many times the word Sherlock appears in the text?

Actually Hadoop has a very useful HDFS command that retrieves all files from a remote folder and merges them into a single file in the local file system on the computer you are using.. In order to locally aggregate all three files invoke the following command:

`$ hadoop fs –getmerge out count.txt`

 

We are going to use now a larger dataset in order to take advantage of the cluster. We will use the folder /data/gutenberg where a larger dataset is already loaded. This folder contains a prepacked file with multiple books downloaded from project Gutenberg, written in different languages.

Invoke again the MapReduce job, this time setting the input path to 

/data/gutenberg

 This is going to take a bit more to execute, as you are processing a file of size 400MB. 

You can obtain realtime information about the progress of your job by going to the web ui of the hadoop cluster 

http://studoop.eecs.qmul.ac.uk:8088

You will see a list of running and complete jobs. Find your running job (the console will have displayed your job ID after the job started running) and click in the link of your specific job to access the details page with its current execution status. You can also access that link through the link that is shown in the console output when the job is accepted in the cluster.

Once the job finishes have a look at the output results provided by Hadoop (either through the job information web page, or through the output emitted by Hadoop in the console). There is a total of 24 base metrics providing details on how the parallel process went.

- How many Map tasks does your MapReduce job have? Can you explain the difference with the Sherlock job? How many are actually local mappers (i.e. they are initially collocated with the data they have to process)? How many Reduce tasks?

 

ADDING A COMBINER
 

In this case it should be possible to significantly improve the overall MapReduce performance by adding a Combiner to the process. Add a Combiner to the Hadoop job by adding to the JobConf the following line:

```java
 job.setCombinerClass(NameOfTheCombiner.class);
 ```

 So we now have
```java
job.setMapperClass(TokenizerMapper.class);

job.setCombinerClass(IntSumReducer.class);

job.setReducerClass(IntSumReducer.class);
```

 

in the WordCount.java file.

Repackage the Hadoop program again (`$ ant clean dist`) and repeat the Hadoop execution over the same data.

Have you seen a noticeable improvement in performance? You can go to the web UI and retrieve the total execution time of both jobs in order to compare it.

In order to understand why it has improved, have a look at the Map output records, Combine input records, Combine output records and Reduce input records. Can you see the impact that defining a Combiner has with large jobs?

Finally, have a look at the results of your process. Can you improve the quality of your word counting program by detecting in your StringTokenizer filter some of the characters that appear at the start of the file? There are quite a few symbols which haven't been filtered out with the base filter. However, this approach needs to specify every single special character. Instead, you can run a regexp before splitting the file, in order to eliminate all non-alphabetic characters: 
```java
String line = value.toString().replaceAll("[^a-zA-Z\\s]", ""); 
```
