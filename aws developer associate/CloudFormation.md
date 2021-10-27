# Overview
- Manage, configure, and provision your AWS infrastructure as code
- Resources are defined using a CloudFormation template
- CloudFormation interpret the template and makes the appropriate API calls to create the resources you have defined
- Supports YAML or JSON
=> Consistent: insfra is provisioned consistently with fewer mistakes
=> Quick & efficient: less time and effort than configuring things manually
=> Version control: can version control and peer review your template
=> Free to use: you are charged only for the aws resources you create using AWS resource
=> Can be used to manage updates and dependencies
=> Can rollback to a previous state and delete the entire stack as well
## CloudFormation Process
- YAML or JSON template used to describe the end state of the infra 
- Upload the template to CloudFormation using S3
- CloudFormation reads the template and makes the API calls
- The resulting set of resources that CloudFormation builds from your template is called a stack
## Example 
```
AWSTemplateFormatVersion: "2021-10-26"
Description: "Template to create an EC2 instance"
Metadata:
	Instances:
		Description: "Web server interface"
Parameters: 
	EnvType:
		Description: "Environment type"
		Type: String
		AllowedValues:
			- prod
			- test
Conditions
	CreateProdResources: !Equals [ !Ref EnvType, prod]
	Mappings: 
	RegionMap:
		eu-west-1:
			"ami": "ami-0bdb1d6c15a40392c"
Transform
	Name: 'AWS::Include'
	Paramters:
		Location: 's3://MyAmazonbucket.yaml'
Resources:
	EC2Instance:
		Type: AWS::EC2::Instance
		Properties:
		Outputs
	InstanceID:
		Description: The Instance ID
		Value: !Ref EC2Instance
			InstanceType: t2.micro
			ImageId: ami-0bdb1d6c5a4032c
```
- The resources section is mandatory
- The transform section is for referencing additional code. The Transform section specifies one or more macros that AWS CloudFormation uses to process your template. The Transform section builds on the simple, declarative language of AWS CloudFormation with a powerful macro system. The declaration `Transform: AWS::Serverless-2016-10-31` is required for AWS SAM template files.
- Parameters section let you input custom values
- The conditions section let you provision resources based on environment
- The mapping section allows you to create custom mappings like Region : AMI

# CloudFormation and SAM
## Serverless Application Model
- CloudFormation for Serverless: SAM is an extension to CloudFormation used to define Serverless application
- Simplified Syntax for defining serverless resources: APIs, Lambda function, DynamoDB tables, etc.
- SAM cli: use SAM CLI to package your deployment code, upload it to S3, deploy your serverless application
- Commands:
	- sam packages: takes a template as its input and it outputs a sam compatible template
	```
	sam package --template-file ./mytemplate.yml \
				-- output-template-file sam-template.yml \
				--s3-bucket s3-bucket-name
	```
	- sam deploy: 
	```
	sam deploy --template-file sam-template.yml \
			   --stack-name mystack \
			   --capabilities CAPABILITY_AIM
	```
	
	# Nested stack
	- CloudFormation enable re-use of CloudFormation code for common use cases
	- A standard configuration for a load balancer, web server, application server
	- Create a standard template for each common use case and reference from within your CloudFormation template
	## Template structure
	- Reference it in the Resources section or any CloudFormation template using the Stack resource type
	```
	Resources:
	Type: AWS::CloudFormation::Stack
	Properties:
		NotificationARNs:
			- String
		Parameters:
			AWS CloudFormation Stack Parameters
		Tags:
			- Resource Tag
		TemplateURL: https://s3.amazonaws.com/.../template.yml
		TimeoutIntMinute: Integer