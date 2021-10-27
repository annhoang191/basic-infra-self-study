# CodeDeploy overview
- Automated deployment to ec2 instances, on-premises & Lambda
- Quickly release new features
- Avoid downtime during deployment
- Avoid the risks associated with manual processes
- CodeDeploy deployment approaches
	- In-Place deployment: the application is stopped on each instance and the new instance is installed. Also known as Rolling Update
	- Blue/Green deployment: new instances are provisioned and the new release is installed on the new instances. Blue represents the active deployment, green is the new release
## In-Place Deployment
- Capacity is reduced during the deployment
- Lambda is not supported
- Rolling back involves a re-deploy
=> Great when deploying the first time
- Example: several servers are running behind a load balancer
	- CodeDeploy is going to stop the application on the first instance
	- The instance will be out of service during the deployment so capacity will be reduced
	- Elastic load balancer should be config to stop sending request to the instance while it is out of service 
	- CodeDeploy instance the new version on the instance, known as revision
	- The instance comes back into service once the revision has been deployed
	- CodeDeploy continues deploy next instance
	- Rollback: there's no quick fix => need to re-deploy the previous version which can be time consuming
## Blue/Green deployment
- Blue represents the current version of our application
- CodeDeploy provisions new instances which are completely independent from the current production environment and in the blue-green terminology => green environment
- The new revision is deployed to the green environment
- The green instances are registered with the ELB
- Traffic is routed away from the environment
- Rollback: set the load balancer to direct traffic to the blue environment and deregister the green (if you haven't terminated the old environment)
=> No capacity reduction
=> Green instances can be created ahead of time
=> Easy to switch between old and new
=> Pay for 2 environments until you terminate the old servers
=> Safest option

# CodeDeploy using the AppSpec File
- A configuration file: define the parameters to be used during a codedeploy deployment
- For EC2 and on-premises system, only supported YAML format
- For Lambda function, YAML and JSON are supported. File structure depends on if you are deploying to Lambda or EC2
## AppSpec File Structure
- Version: reserved for future use, currently the allowed value is 0.0
- OS: relates to the operating system you are deploying to
- Files: configuration files, packages. Use this section to define the location of any application files that need to be copied and where they should be copied to
- Hooks: lifecycle event hooks are scripts which need to run at set points in the deployment lifecycle. Hooks have a very specific run order
## Script you might run during a deployment
- Uzip files: unzip application files prior to deployment
- Run tests: run functional tests on a newly deployed application
- Deal with load balancing: de-register and re-register instances with a load balancer
## Example appspec.yml
```
version: 0.0
os: linux
files:
	- source: Config/config.txt
	  destination: /webapps/Config
	- source: Source
	  destination: /webapps/myApp
hooks:
	BeforeInstall:
		- location: Scripts/UnzipResourceBundle
		- location: Scripts/UnzipDataBundle
	AfterInstall:
	    - location: Scripts/RunResourceTests.sh
		  timeout: 180
    ApplicationStart:
		- location: Scripts/RunFunctionTests.sh
		  timeout: 3600
	ValidateService:
		- location: Scripts/MonitorService.sh
		  timeout: 3600
		  runas: codedeployuser
```
- The appspec.yml must be placed in the root of the directory of your Revision, otherwise the deployment will fail

# CodeDeploy Lifecycle Event Hooks
- Phase 1:  De-register instances from a load balancer
	- BeforeBlockTraffic: task you want to run on instances before they are de-register from a load balancer
	- BlockTraffic: de-register instances from a load balancer
	- AfterBlockTraffic: tasks you want to run on instances after they are de-registered from a load balancer
- Phase 2: the real nuts  & bolts the application deployment
	- ApplicationStop: gracefully stop the application
	- DownloadBundle: CodeDeploy agent copies the appilcation revision files to a temporary location
	- BeforeInstall: pre-installlation scripts, e.g. backing up the current version, decrypting files
	- Install: copy application revision files to final location
	- AfterInstall: post-install scripts e.g configuration, file permissions
	- ApplicationStart: start any services that were stopped during ApplicationStop
	- ValidateService: run tests to validate the service
- Phase 3: Re-register instances with load balancer
	- BeforeAllowTraffic: tasks you want to run on instances before they were registered with the load balancer
	- AllowTraffic: register instances with a load balancer
	- AfterAllowTraffic: tasks you want to run on instances after they are registered with a load balancer
