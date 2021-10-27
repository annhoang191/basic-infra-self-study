# Elastic Beanstalk overview
- It allows you to deploy and scale your web applications onto widely used application server platforms
- Supported languages: java, .net, php, nodejs, python, ruby, go, docker
- Supported platforms: apache http server, tomcat, nginx, passenger, iis
- Developers can focus on writing code and do not worry about any of the underlying infrastructure needed to run the app
- You provide your code to Beanstalk and it will do the rest:
	- provisioning load balancers
	- launching ec2 instances
	- configuring auto scaling groups
	- taking care of installing your app server platform like apache
	- create s3 bucket, database, etc. 
- Integrates with CloudTrail and CloudWatch, also includes its own health
- In short, Beanstalk handles:
	- Infrastructure: provisioning infra, load balacing, auto scaling, application health monitoring
	- Application platform: installation and management of the application stack, including patching and updates to your OS and app platform
	- You are in control: have completed administrative control of the aws resources.
	- No additional charged for using Beanstalk, only charged for the resources deployed

# Deploy an app with Beanstalk 
- Start with PHP code in zip file, this file will be uploaded to Beanstalk. Beanstalk will do the rest:
	- Create EC2 instance and install Apache http server on the instance
	- Install php code on the instance, create security group, auto scaling group, load balancing, etc.
- Select Elastic Beanstalk service -> Select platform, platform branch, platform version -> Upload application code -> create application 

# Updating Elastic Beanstalk
## All at once deployment
- Deploys to all hosts concurrently
- Experience a total outage while the deployment takes place 
=> The website will be offline during deployment
=> Not ideal for mission-critical production system or applications which needs to be available 24/7
=> If the update fails you need to rollback by redeploying the original versions to all your instances
=> Another outage to get back to the previous version
=> Only use in development or test environment because it's quick and simple
## Rolling update deployment
- Deploys the new version in batches 
- Each batches is taken out of services while the deployment takes place
=> If it fails you need to perform an additional rolling update to rollback the changes
=> Will not experience outage but environment capacity will be reduced by the number of instances in a batch while the deployment takes place
=> Not ideal for performance sensitive system
=> Ideal when performance is not a factor, like a website for local library 
## Rolling with additional batches deployment
- Launches an additional batch of instances then deploys the new version in batches
- Maintains full capacity throughput deployment
- If the udpate fails you neeed to perform an additional rolling update to rollback the changes => still going takes some time 
=> Won't experience any degraded performance during the deployment process

## Immutable deployment
- Deploys the new version to a fresh group of instances before deleting the old instances
 - Only when the new instances pass their health checks, old instances be terminated
- If deployment fails just delete the new instances => pretty much immediately
=> Preferred approach for mission critical systems
## Traffic splitting deployment
- Install a new version on a new set of instances like immutable deploy
- it forwards just a percentage of the incoming client traffic to the new application version for evaluation
- If the new instances stay healthy, Beanstalk forwards 100% of the traffic to them and terminated the old ones 
=> Enables canary testing 
=> can test the new version in production before fully committing

# Advanced Elastic Beanstalk
- You can customize your Beanstalk environment by using Beanstalk configuration files
- Configuration files:
	- Define packages to install, create Linux users and groups, run shell commands, enable services, load balancer configuration
	- Can be written in YAML or JSON fornmat
	- The file must have a .config extension and be inside a folder called .ebextenstions
		- The .ebextensions must be included in the top-level directory of your app source code bundle
		- The configuration files can be placed under source control along with the rest of your application code
=> These files will be zipped together with your code which you supplied to Elastic Beanstalk

# RDS and Elastic Beanstalk
- Beanstalk supports 2 ways of integrating RDS database with Beanstalk environment: deploying RDS inside of your Elastic Beanstalk environment or outside of it
## Launch RDS within Beanstalk
- Launch the RDS instance from within the Beanstalk console
- It is created within Beanstalk environment
- If you terminate the environment the database will also be terminated
=> Good option for dev and test deployment, not for Prod
## Launch RDS outside Beanstalk
- Don't use Beanstalk to create RDS database
- Use RDS console or AWS cli to create rds database
- It allows you to tear down your application environment without affecting the database instance
=> Preferred approach for Prod environment
## Connecting to an outside database
- Add an additional security group to your environment auto scaling group to allows EC2 instance to communicate with RDS database on revelant port 
- Provide connection string information to your application servers using Elastic Beanstalk environment properties