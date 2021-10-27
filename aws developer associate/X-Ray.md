# X-Ray overview
- A tool which helps developers analyze and debug distributed applications
- Allow you to troubleshoot the root cause of performance issues and errors
- Provides a visualization of your application's underlying components => X-Ray service map provides an end-to-end view of requests as they travel through your application
	- latency
	- http status code
	- error messages
## Integrations
- AWS services: ec2, elastic beanstalk, elastic container service, lambda, sns, sqs, dynamodb, elastic load balancer, API Gateway
- integrate with your application written in Java, Nodejs, .net, Go, Ruby, Python
- API calls: X-ray SDK automatically captures metadata for API calls made to AWS services using aws sdk
## X-Ray architecture
- Install X-Ray agent: must install the agent on your ec2 instance
- Configure: instrument your application using X-Ray sdk
- X-Ray SDK: it gathers information from request and response headers, the code in your application, and metadata about the aws resources on which it runs, and send this trace data to x-ray. E.g: incoming http requests, error code, latency data
## X-Ray configuration
- X-Ray SDK sends the data to the X-Ray daemon which buffers segments in a queue and uploads them to X-Ray in batches
- Need both the X-Ray SDK and the X-Ray daemon on your system
- You use the SDK to instrument your application to send the required data. eg: data about incoming and outgoing HTTP requests that are being made to your Java application
### X-Ray high level configuration steps:
- On premises & EC2 instances: install the x-ray daemon on ec2 instances ỏ on-premises server
- Elastic beanstalk: install x-ray daemon on the ec2 instances inside your elastic beanstalk environment
-  Elastic container service: install x-ray daemon in its own Docker container on ECS cluster alongside your app
### Annotations & Indexing
- Annotation: when instrumenting your application, you can record additional information about requests by using annotation
- Key-value pairs: annotations are simple key-value pairs that are indexed for use with filter expressions so that you can search for traces that contains specific data and group related traces together in the console - eg: game_name=TicTacToe, game_id=293232
- 