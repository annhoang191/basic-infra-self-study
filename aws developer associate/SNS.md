# SNS Overview
- A web service that makes it easy to set up, operate, and send notifications from the cloud
- Messages sent from an application can be immediately delivered to subscribers or other applications
=> Push notifications to devices (apple, google, fire os, windows, android)
=> SMS and email: sms text message or email to SQS queues or any HTTP endpoint
=> Trigger Lambda functions to process the information in the messages, publish to another sns topic or send messages to another aws service
## Pub-sub model
- Application publish or push messages to a topic
- Subscribers receive messages from a topic
- Example: 
	- An online banking application
	- Want to send out a notification when a loan is approved or when a new credit card statement is ready, etc.
	- These notifications get sent to an SNS queue and within SNS you can set up different topics where the messages are grouped together
	- Then the subscribers (lambda, sqs queue, email, mobile device) subscribe to the relevant topic, rather like subscribing to an email list
- Notifications are delivered using a push mechanism that eliminates the need to periodically check or poll for new information and updates
### Topic
- An access point, allowing recipients to subscribe to, and receive identical copies of the same notifications.
- SNS delivers appropriately-formatted copies of the msg to each subscriber (ios, android, sms, etc.)
## Features
### Durable storage
- Prevents messages from being lost if something goes wrong: All messages published to SNS are stored redundantly across multiple AZs
### Instantaneous
- Instantatenous, push-based deilvery (no polling)
### Simple
- Simple api and easy to integrate with other services
### Flexible
- Flexible message delivery over multiple transport protocol
### Inexpensive
- Inexpensive, pay-as-you-go model with no upfront costs
### Easy to configure
- Web-based aws console offers the simplicity of point-and-click interface
### Managed service
- High availability and durability and scalabilityes
