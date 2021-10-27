# Kinesis overview
- A family of services which enables you to collect, process, and analyze streaming data in real-time 
- Allows you to build custom applications for your own business needs
- Deals with data that is in motion, or streaming data
## Streaming data
- Data generated continously by thousands of data sources, which typically send in the data records simultaneously and in small size (kb)
	- Financial transactions
	- Stock prices
	- Game data (as the game plays)
	- Social media feeds
	- Location tracking data
	- IoT sensors
	- Log files
	- Clickstream data
## Four core services
 ### Kinesis streams 
 - Stream data and video to allow you to build custom applications that process data in realtime
	 - Kinesis data stream: deals with data
		 -  Kinesis shard: 
			- each shard is a sequence of one or more data records and provided a fixed unit of capacity 
			- Five reads per second
			- The max total read rate is 2MB per second
			- 1k writes per second
			- The max total write rate is 1MB per second
		=> The data capacity of the stream is determined by the number of shards
		=> If the data increases, you can increase capacity on your stream by increasing the number of shards
	 - Kinesis video stream: deals with only video data. Securely stream video from connected devices to AWS
		 - Video can be used for analytics and machine learning
#### Example
- Producers, which are devices like EC2, mobiles, laptop, iot sensors, send data over to Kinesis data streams
- Kinesis streams retains the data by default for 24 hour with a maximum of 7 days retention
- Data is stored in these things called shard. Shard is basically a sequence of data records
-  Each data record has a unique numbers
-  Kinesis stream is made up of one or more shards
-  Consumers, which are some EC2 and lambda functions, take the dat from the shard and process it then save that data to permanent storage
-  Re-sharding enables you to increase or decrease the number of shards in a stream in order to adapt to changes in the rate of data flowing through the stream. 
-  You should ensure that **the number of instances does not exceed the number of shards (except for failure standby purposes).** 
-  **Each shard is processed by exactly one KCL worker and has exactly one corresponding record processor, so you never need multiple instances to process one shard**. 
-  However, one worker can process any number of shards, so it's fine if the number of shards exceeds the number of instances. 
-  When re-sharding increases the number of shards in the stream, the corresponding increase in the number of record processors increases the load on the EC2 instances that are hosting them. 
-  If the instances are part of an Auto Scaling group, and the load increases sufficiently, the Auto Scaling group adds more instances to handle the increased load. Kinesis Data Streams Terminology Resharding, Scaling, and Parallel Processing
### Kinesis data firehose
- Capture, transform, load data stream into AWS data stores to enable near-real-time analytics with BI tools
=> Can transform raw data into apache parquet format and then load it into S3, Redshift, Elasticsearch
#### Example
- Producers, which are devices like EC2, mobiles, laptop, iot sensors, send data over to Kinesis data firehose
- Kinesis firehose have no data retention, no shards or streams,  all the capacity and sizing is automated
- Don't neeed consumers to consumer data
- Optionally use a Lambda function to process or analyze data in real-time
- When data is received by firehose it's either picked up by lambda or saved directly to AWS storage
### Kinesis data analytics
- Analyze, query, transform streamed data in real-time using standard SQL. 
- Store the results in an AWS data store (S3, Redshift, Amazon Elasticsearch)
#### Example
- Producers, which are devices like EC2, mobiles, laptop, iot sensors, send data over to Kinesis streams and Kinesis firehose
- Kinesis data analytics allows you to run sequel queries on data as it comes in from kinesis firehose and kinesis streams
- The results of the SQL query can be stored in S3, Redshift, Elasticsearch

# Setting up Kinesis data stream
## Architecture overview
- Cloudformation to configure
- EC2: producer to generate click stream data and consumer
- Kinesis data stream to capture streaming data
- DynamoDB stores the data

# Kinesis shards vs Consumers
## Kinesis client library 
- Kinesis Client Library runs on the consumer instances
- Tracks the number of shards in your stream
- Discovers new shards when you reshard (increase new shard added)
- Ensures that for every shard there is a record processor
- Manages the number of record processors relative to the number of shard & consumers
- If you have only one consumer, KCL will create all the record processors on a single consumer
- If you have two consumers it will load balance and create half the processors on one instance and half on another
- You should ensure that the number of instances does not exceed the number of shards (except for failure or standby purpose)
- Never need multiple instances to handle the processing load of one shard
- However one worker can process multiple shards
- Example:
	- 1 kinesis data streams, 4 shards
	- 1 consumers, processing 4 shards
	- 4 x record processors are running on the consumer
- It's fine if the number of shards exceeds the number of instances => no neeed to add more instances if you reshard
- CPU utilization is what should drive the quantity of consumer instances you have, not the number of shards in your Kinesis stream
- Use the auto scaling group, and base scaling decisions on CPU load on your consumers
