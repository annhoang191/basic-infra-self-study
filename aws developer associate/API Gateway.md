# What is API gateway
- Publish, maintain, monitor APIs: API gateway is a service which allows you to publish, maintain, monitor, and secure APIs at any scale
- A front door: is like a front door for application to access data, business logic, or functionality from your backend services like applications running on EC2, lambda
- Supported API types: 
	- RESTful APIs are optimized for stateless, serverless workloads
	- Websocket APIs are optimized for real-time, two-way, stateful communication. E.g: chat apps
- API gateway provides a single endpoint for all client traffic interacting with the backend of your application
	- Ex: user send requests to API gateway, then API gateway can forward the request to different services  like Lambda, EC2, DynamoDB, etc.
=> API Gateway can allows you to connect to applications running on Lambda, EC2, Elastic BeanStalk, DynamoDB, Kinesis
=> Supports multiple endpoints and targets: send each API endpoint to a different target
=> Support multiple version: allows you to maintain multiple version of your APIs, so you can have different version for your development, testing, production environment
=> Intergrates with CloudWatch so it logs all API calls
=> Helps you manage traffic with throttling so that backend operations can withstand traffic spikes and denial of service

# Building a serverless website
- A static websites hosted in S3 => Turn that static web into dynamic by using API Gateway and Lambda function

# Importing APIs into API Gateway
- Importing API definition files: you can use the API gateway import API feature to import an API using a definition file
- Supported protocols: 
	- OpenAPI (Swagger)
		- Create new and udpate existing APIs: can use OpenAPI definition to create a new API or update an existing API
	- SOAP: which returns a response in XML format instead of JSON
		- You can configure API gateway as a SOAP web service passthrough
		- Use API Gateway transform the XML response to JSON

# API Gateway Caching and Throttling
## API Gateway caching
- Caches your endpoints response: reduces the number of calls made to your endpoint and can also improve the latency for requests to API
- Caches responses from your endpoint for a specified time-to-live TTL period in seconds. Default is 300 seconds
- API Gateway responds to new request by looking up the response from the cache instead of making a new request to application
=> Help you improve the performance of your APIs, and the latency your end user experience by caching the output of API calls to avoid calling your backend application every time
## API Gateway throttling
- Prevent your API from being overwhelmed by too many requests
- By default API Gateway limits the steady-state request rate to 10,000 request per second per region
- Concurrent Request: the maximum concurrent requests is 5,000 requests across all APIs, per Region
- If you exceed limit you will receive 429 Too Many Requests
- Example:
	- 5k initial requests: you receive 5,000 requests in the first milisecond and then another 5,000 requests evenly spread throughout the remaining 999 milisecond
	- Within the limit: this is still within the per region limit of 10,000 requests per second and 5,000 concurrency requests
	- Process all requests: API processes all 10k requests in the one-second period without returning an error
	- 