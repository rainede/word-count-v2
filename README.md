# word-count
Map Reduce Wordcount
IntWritable and Text are the Hadoop equivalents to int and String Java types. They follow their own hierarchy of classes so that they can be moved over the network during the shuffle and partition steps.
Identify the code for splitting the text into separate words is slightly different, but the
The emit command is equivalent to  write in real MapReduce code.
Look at the signature of TokenizerMapper, and try to understand how it relates to pairs <k1,v1>, <k2,v2>

## Setting up mapreduce
Create an input folder
`$ mkdir input`
WordCount.java a MapReduce program that orchestrates the two classes we have just defined.

In order to create a package ready to be executed by Hadoop go to the project root folder and invoke the ant command

`$ ant clean dist`

If the code compiles correctly a compressed jar file named HadoopTest.jar will appear in a newly created dist/ folder

Execute the job from the base folder of your project running the following line:

`$ hadoop-local  jar dist/WordCount.jar WordCount input out`
