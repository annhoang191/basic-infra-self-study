# SQS Overview
- One of the oldest service that AWS have
- A message queue service, it gives you access to a message queue which can be used to store message while waiting for an application to process them
=> Enables web service application to quickly and reliably queue messages that one component in the application generates for another component to consume
- Queue: a temporary repository for messages awaiting processing
=> Any application which is going to consume the messages needs to poll the queue to get the message, they ask you to select a pull-based system for messaging between application components  => Think about SQS
- Any messages which are partially processed but the application server is crashed will not be deleted and will end up back to the queue
	- Visibility timeout: when a message is first picked up by our application server it's going to be marked as invisible so no other application server can pick up that message => this is the amount of time that the application server gets to process the message. As soon as that window has expired if the messgae has not been completely processed then it will appear in the queue again
- Decoupling your infrastructure: You're removing dependencies between individual components in our application so your application can fail but the job itself and the information held in our user's query is not lost 
## Summary features
- Decouple application components: applications can run independently easing message management between components
- Store messages: messages can contain up to 256kb of text in any format
- Retrieve message: any component can later retrieve the messages programmatically using the Amazon SQS API
- A buffer between components receiving the data for processing and the component producing and saving the data
- Resolve scheduling issues: the queue resolves issues that arise if the producer is producing work faster than the consumer can process it or if the producer or consumer are only intermittenly connected to the network
## Key facts
- Is a pull-based, not a push-based system
- Messages are in 256kb in size
- Text data including xml, json, and unformatted text
- Guarantee messages will be processed at least once
- Messages can be kept in the queue from one minute to 14 days
- The default retention period is 4 days

# SQS Queue types
## Standard queue
- Nearly-unlimited number of transactions per second
- Guarantee that a message will be delivered at least once
- Best-effort ordering: ensures that messages are generally delivered in the same order as they are sent. Occasionally (because of the highly-distributed architecture that allows high throughput), more than one copy of a message might be delivered out of order, also message can be duplicate
=> The default queue type
## FIFO queues
- The order in which messages are sent and received is strictly preserved
- Exactly-one processing: a message is delivered once and remains available until a consumer processed and deletes it. Duplicates are not introduced
- 300 TPS Limit: limited to 300 transactions per second but have all the capabilities of standard queues
=> Good for banking transactions which need to happen in order
## Delay queues
- Postpone delivery of new messages to a queue for a number of second
- Msgs sent to the delay queue remain invisible to consumers for the duration of delay period
- Default delay is 0 seconds, maximum is 900 (15 minutes)
- For standard queue, chaning the setting doesn't affect the delay of messages already in the queue, only new messages
- For FIFO queue, this affects the delay of messages already in queue 
=> Use when large distributed applications which may need to introduce a delay in processing
=> Need to apply a delay to an entire queue of messages
=> Example: adding a delay of few seconds to allow for updates to your sales and stock control database before sending a notification to a customer confirming an online transaction
# SQS settings
- Visibility timeout: the amount of time that the message is invisible in the SQS queue after a reader picks up that message
	- Default timeout is 30 seconds
	- Increase it if the task takes more than 30s to complete
	- The maximum is 12 hours
- DelaySeconds allows the delivery of a message to be delayed for between 0 (default) and 900 seconds before the message becomes visible to consumers of the queue for the first time.
- MessageRetentionPeriod controls how long a message remains available in a queue before it is discarded.
- ReceiveMessageWaitTimeSeconds controls polling wait time. Amazon SQS Delay Queues
- Polling
	- Short polling: 
		- returns a response immediately even if the message queue being polled is empty
		=> result in a lot of empty responses if nothing is in the queue
		=> You will still pay for these responses
	- Long polling
		- Periodically polls the queue
		- Doesnt return a response until a message arrives in the message queue or the long poll times out
		=> can save money
		=> generally preferable to short polling
	
	# Managing large messages
	## Best practices for managing large sqs messages using S3
	- For large SQS message- 256kb up to 2GB in size
	- Use S3 to store the messages
	- Use Amazon SQS Extended Client Library for Java to manage them
		- Specify that msg are always stored in Amazon S3 OR only messages > 256kb
		- Send a message which references a message object store in S3
		- Get a message object from S3
		- Delete a message object from S3
	- You also need the AWS SDK for Java - provides an api for S3 bucket and object operations
	- You cannot:
		- Use AWS CLI
		- AWS Management Console /SQS Console
		- SQS API
		- Any other AWS SDK