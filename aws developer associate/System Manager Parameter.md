# System Manager Parameter
## Scenario
- Work as a system admin for a bank
- Need to find a way to store parameters used by your applications (license keys, database connection info, username and password, etc)
- This info need to be passed to your EC2 instances as a bootstrap script
- Need to maintain the confidentiality of the info and avoid hard coding params in your code

=> Use System Manager Parameter

## Create a parameter store
- Choose service System Manager -> Parameter store -> Create parameter -> Set up name, description, choose tier (standard: limit of 10,000 params and param value size up to 4kb / advanced: more than 10,000 params and param value size up to 8kb) -> choose datatype for param and set value

## Exam tips: 
- Store confidential info
- Reference: can refer your params using the parameter name in a bootstrap script
- Plain text or encrypted: can store values as plaintext or encrypt them
- Integrated with aws services like ec2, cloudformation, lambda, codebuild, codepipeline, codedeploy