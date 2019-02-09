# Spark_JSON

=========================================================================================================================================================================
										JSON DATA Analysis:
==========================================================================================================================================================================


1.Open the terminal and create the person data in the JSON format:
$ mkdir jsondata
$ vi jsondata/person.json
{"first_name" : "Barack", "last_name" : "Obama", "age" : 53}
{"first_name" : "George", "last_name" : "Bush", "age" : 68 }
{"first_name" : "Bill", "last_name" : "Clinton", "age" : 69 }
{"first_name" : "Donald", "last_name" : "Trump", "age" : 65 }
{"first_name" : "Rod", "last_name" : "Tucker", "age" : 58 }
{"first_name" : "Hillary", "last_name" : "Clinton", "age" : 65 }
{"first_name" : "Jeff", "last_name" : "Thompson", "age" : 62 }


2.Upload the jsondata directory to HDFS:
$ hdfs dfs -put jsondata /user/sid/jsondata1

3.Start the Spark shell and give it some extra memory:
$ spark-shell --driver-memory 1G

4.Create an instance of SQLContext :
scala> val sqlContext = new org.apache.spark.sql.SQLContext(sc)

5.Import for the implicit conversion:
scala> import sqlContext.implicits._

6.Load the jsondata directory from HDFS:
scala> val person = sqlContext.jsonFile("hdfs://localhost:8020/user/sid/jsondata")
scala> val person = sqlContext.read.json ("hdfs://localhost:8020/user/sid/jsondata1")


7.Register the person DF as a temp table so that the SQL queries can be run against it:
scala> person.registerTempTable("person")

8.Select all the persons with age over 60 years:
scala> val sixtyPlus = sqlContext.sql("select * from person where age > 60")

9.Print values:
scala> sixtyPlus.collect.foreach(println)

10.Let's save this sixtyPlus DF in the JSON format
scala> sixtyPlus.toJSON.saveAsTextFile("hdfs://localhost:8020/user/sid/jsonsp")

11.Last step created a directory called sp in the HDFS root. You can run the hdfs dfs -ls command in another shell to make sure it's created:
$ hdfs dfs -ls sp

12. open hive terminal

13. ADD JAR /home/sid/work/apache-hive-1.2.1-bin/lib/hive-serdes-1.0-SNAPSHOT.jar;

13. create table if not exists jsondata1 (first_name STRING, last_name STRING, age INT) row format SERDE 'com.cloudera.hive.serde.JSONSerDe' Location 'hdfs://localhost:8020/user/sid/jsondata1';

14. load data inpath '/user/sid/jsondata/person.json' overwrite into table jsondata;

15. Insert Overwrite Local Directory '/home/sid/json/hive_inputs/jsondata.txt' ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' Select * from jsondata;

16. mysql -u root -p --local-infile

17. Create Table jsondata_export (first_name VARCHAR(20), last_name VARCHAR(20), age INT(20));

18. Load Data Local Infile '/home/sid/json/hive_inputs/jasondata.txt' INTO TABLE jsondata_export;
