# Serverless 101
- Allows you to run your application code in the cloud without having to worry about managing any servers
- AWS handles the infra management tasks so that you can focus on writing code
	- Capacity provisioning
	- Patching
	- Auto scaling
	- High availability

=> You can focus on writing code
=> Competitive advantage
	- Speed to market: eliminating the overhead of managing servers, you can release code quickly and get your application to market faster
	- Super scalable
	- Lower costs: never pay for over-provisioned and are event-driven, you are only charged when your code is executed
	- Focus on your application
- Serverless technologies:
	- Lambda: enable you to run code as function without provisioning any servers
	- SQS: a message queuing service that allows you to decouple and scale your application
	- SNS: a messaging service for sending text mesages, mobile notifications and emails
	- API gateway: allows you to create, publish and secure APIs at any scale
	- DynamoDB: fully managed NoSQL database
	- S3: object storage and web hosting
- Example of serverless architecture:
	- Browser sends requests to API gateway and your request is then routed to the appropriate lambda function, data is stored on S3 

	