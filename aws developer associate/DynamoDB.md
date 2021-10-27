# DynamoDB Overview
- Fast and flexible NoSQL Database
	- Great for applicatinos which need consistent, single-digit milisecond latency at any scale
- Fully managed
	- Supports key-value data models
	- Supported document formats are JSON, HTML, XML
- Use case:
	- Mobile, web gaming, ad tech, IoT, other applications
- Integrates well with Lambda
- Can be configured to automatically scale
- Popular choice for developers and architects who are designing serverlesss application
- DynamoDB data is stored on SSD => consistently fast performance for read and write
- The hardware supporting your DynamoDB table is spread across 3 geographically distinct data centers => resilience
- Choice of 3 consistency models for DynamoDB: 
	- Eventually consisten reads (default): consistency across all copies of data is usually reached within a second => best for read performance
	- Strongly consistent read: always reflects all successful writes, writes are reflected across all 3 locations at once => best for read consistency
	- DynamoDB Transactions: provide the ability to perform ACID Transactions (Atomic, Consisten, Isolated, Durable)
## DynamoDB Primary Keys
- DynamoDB stores and retrieves data based on a primary key
- Two types: 
	- partition key 
		- based on a unique attribute (customer id, product id, email, etc.)
		- value of the partition key is input to an internal hash function which determines the partition or physical location on which the data is stored
		- If you are using the partition key as your primary key, then no two items can have the same partition key
	- Composite key (partition key + sort key)
		-  Use when the partition key was not necessarily unique within your table
		-  Ex: a table stores forum posts and you expect to have users posting multiple message in the same forum => The user id alone is not going to be a unique identifier => Use a combination of user_id and sort key which is a timestamp 
		- A unique combination: items in the table may have the same partition key, but hey must have different sort key
		- Storage: all items with same partition key are stored together and then sorted according to the sort key value
## Create DynamoDB using aws cli
```
aws dynamodb create-table --table-name ProductCatalog --attribute-definitions\
> AttributeName=Id.AttributeType=N --key-schema \
> AttributeName=Id.KeyType=HASH \
> --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
```
## Populate the table with a json file
```
aws dynamodb batch-write-item --request-items file//items.json
```
## Get  item
```
aws dynamodb get-item --table-name ProductCatalog --region us-east-1 --key '{"Id": {"N": "403"}}'
```

# DynamoDB Access Control
- IAM: authentication and access control is managed using AWS IAM
- IAM Permissions: create IAM users within your aws account with specific permission to access and create DynamoDB table
- IAM Roles: create IAM roles, enabling temporary access to DynamoDB
- Restricting User Access: use special IAM condition to restrict user access to only theirr own records
- Ex: users need to access their own top score for each game that they are playing. Access must be restricted to ensure they cannot view anyone else data
=> Use IAM condition parameter `dynamodb:LeadingKeys`

# DynamoDB Indexes
- Secondary index allows you to perform more flexible querying: query based on an ttribute that is not the primary key
	- Fast queries on specific columns in a table
	- You select columns that you want included in the index and run your searches on the index, rather than on the entire dataset
- DynamoDB allows you to run a query on non-primary attributes using global secondary indexes and local secondary indexes
## Local Secondary Index
- Has the same partition key as your original table but a different sort key
- Gives you a different view of your data, organized according to an alternative sort key
- Any queries based on this sort key are much faster using the index than the main table
-  Example: Partition key - User ID, Sort key - Account creation date
-  Can only be created when you are creating your table. You cannot add, remove, or modify it later
## Global Secondary Index
- A completely different primary key: different partition and sort key
- Can create whenever you like
- Gives a compeletly different view of the data
- Speed up any queries relating to this alternative partition and sort keys
- Example: partition key - email address, sort key - last login date

# Scan vs Query API calls
## Query
- Finds items in table based on the primary key attribute and a distince value to search for
- Ex: selecting an item where user id = 212 will select all attributes for that item
- Can use an optional sort key name and value to refine the result: if your sort key is a timestamp, you can refine query to only select items with a timestamp of the last 7 days
- By default query returns all attributes for the item but you can use `ProjectionExpression` param if you want to only return specific attributes
- Result are always sorted by sort key: by numeric order or ASCII character code values
- Can reverse the order by setting the ScanIndexForward param to false (only apply to Query)
- By default all queries are eventually consistent
- Need to explicit set the query to be strongly consistent
## Scan
- Scan operation examines every item in the table
- By default it returns all data attributes
- Use the `ProjectionExpression` param to refine the scan to only return the attribute you want

=> Query is more efficient than a scan: a scan dumps the entire table and filter out the values to provide desired result, removing unwanted data
=> Adds an extra step of removing the data you dont want. As the table grows, the scan operation takes longer
=> A scan operation on a large table can use up the provisioned throughput for a large table in just a single operation
### Improving performance
- Set a smaller page size, which use fewer read operations. Then you will end up with larger number of small operations
- Avoid scans: design table in a way that you can use the Query, get, or 
- Use parallel scan: 
	- by default a scan operation processed data sequentially, returns 1MB increment  increments before moving on to retrieve the next 1MB data
	- Scans one partition at a time
	=> Can configure DynamoDB to use parallel scan instead by logically dividing a table or index into segments and scanning each segments in parallel
	=> Best to avoid parallel scan if your table or index is already incurring heavy read or write activity from other application
	=> Isolate scan operations to specific tables and segregrate them from your mission-critical traffic => means writing data to 2 different tables
	
# DynamoDB Provisioned Throughput
- It measured in capacity units
- When you create your table, you can specify your requirements in terms of read capacity units and write capacity units
- It defines how much data you can read and write to your DynamoDB table
- Write capacity units: 1 x write capacity unit = 1 x 1KB write per second
- Read capacity units: 
	- 1 x read capacity unit = 1 x strongly consistent read of 4KB per second
	- 1 x read capacity unit = 2 x eventually consistent reads of 4KB per seond (default))
- Example: 5 x read capacity units and 5 write capacity units => 5 x 4KB strongly consisten reads = 20KB per second 
=> If your app reads or writes larger items, it will consume more capacity units and cost you more as well
- Challenge:
 1. your application needs to read 80 items per second from DynamoDB Table. Each items is 3KB in size. You need strongly consistent reads. Each read capacity unit gives you 1 full kb strongly consistent read per second. How many read capacity units you need?
=> Calculate how many 4KB read capacity need for that:
	- Calculate the size of each item / 4KB: 3KB /4KB = 0.75 => round up to the nearest number so each read operation will need 1 x read capacity units
	- Multiply by the number of read operations per second = 80 read capacity units required for strongly consistent reads: 1 x 80 = 80
2.  1. your application needs to read 80 items per second from DynamoDB Table. Each items is 3KB in size. You need eventually consistent reads. Each read capacity unit gives you 1 full kb strongly consistent read per second. How many read capacity units you need?
=> Size of each item / 4 KB: 3KB / 4KB = 0.75 = 1
=> Multiply by the number of reads per second: 1 x 80 = 80
=> Divide by 2: 80 / 2 = 40 => You only need 40 read capacity units
3. You want to write 100 items per second. Each item is 512 bytes in size. Each write capacity unit gives you 1 full kb write per second. How many write capacity unit you need?
=> Capacity units for each write: 512 bytes / 1024 bytes = 0.5 = 1 
=> Multiply by write per second:  1 x 100 = 100 write capacity units

# DynamoDB On-Demand capacity
- Charges apply for reading, writing, and storing data
- Instantly scale up and down based on the activity of your application
=> Great for unpredictable workloads, new applications when you don't know the use pattern yet, when you want to pay for only what you use (pay per request)
## Pricing models
- On-Demand capacity:
	- Unknown workloads
	- Unpredictable application traffic
	- Spiky, short-lived peaks
	- A pay-per-use model is desired
	- Might be more difficult to predict the cost
- Provisioned Capacity
	- Read and write capacity requirements can be forecasted
	- Predictable application traffic
	- Application traffic is consistent or increases gradually
	- You have more control over the cost
	
# DynamoDB Accelerator (DAX)
- A fully managed, clustered in-memory cache for DynamoDB
- Delivers up to 10x read performance improvêmnt. Microsecond performance for millions of requests per second
=> Ideal for read-heavy workload: auction applications, gaming applications, retail sites when they are running big promotion
## How does it work
- DAX is a write-through caching service: data is written to the cache and the backend store at the same time => Anytime DynamoDB got changes then the DAX cache is also will be updated
- Allows you to point your DynamoDB API calls at the DAX cluster => API calls will try and query the DAX cluster first
=> Reduces the read load on DynamoDB table
=> May be able to reduce provisioned read capacity on your table and save money on your aws bill
## When is DAX not suitable
- Caters for eventually consisten reads only => Not suitable for applications that require strongly consistent reads
- Applications which are mainly write-intensive
- Applications that do not perform many read operations
- Applications that do not require microsecond response times

# DynamoDB Time To Live (TTL)
- Defines an expiry time for your data. Expired items marked for deletion. Those items will be deleted within the next 48 hours
- Great for removing irrelevant or old data (eg: session, event logs, temporary data)
- Reduces the cost of your table by automatically removing data which is no longer relevant
- TTL is in Epoch time format (a numerical value represent the number of seconds which have elapsed since 12:00 am on 1/1/1970)

# DynamoDB Streams
- A time ordered sequence/stream of item level modifications (eg: insert, update, delete)
- It recalls all of those actions as a log. These logs are encrypted at rest and stored for 24 hours
- They can be used for auditing and archiving transactions
- They're mainly used to trigger events based on a particular change => good for serverless architecture, can use to trigger a lambda function 
- Are accessed using their own dedicated endpoint
- Primary key: The minimum amount of data you can record is the primary key of the item that is being modified 
- Can capture the state of the item before the change and the state of items after the change
- Use cases: audit or archive transactions, trigger an event based on a particular transaction, or replicate data across multiple tables
## An event source for lambda
- The events are recorded in near-real time
- Applications can take actions based on contents of the stream
=> DynamoDB Stream can be an event source for lambda
- Lambda will poll the DynamoDB stream and then can execute code

# Provisioned throughput and exponential backoff
## Provisioned throughput exceed
- ProvisionedThroughputExceededException: your request rate is too high for the read/write capacity provisioned on your DynamoDB table 
- The AWS SDK will automatically retry the requests until successful
- Not using AWS SDK: reduce your request frequency or use exponential backoff
## Exponential backoff
- Overloaded components => implement retry in the client
- In addition to simple retries, all AWS SDKs use exponential backoff
- With exponential backoff, the requester uses progressively longer waits between consecutive retries, for improved flow control
- Put it simply, if the first request fail, it will wait 50ms to retry, then if it's still fail, it will wait 100ms before trying again