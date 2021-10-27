# Overview
## Container:
- A virtual operating environment
- A standardized unit with everything the software needs to run e.g libraries, system tools, code, runtime
- Microservices: applications are created using independent stateless components or microservices running in containers
- Docker or Windows container: use Docker to create Linux containers and Windows to Containers for windows workloads
=> Highly scalable: if the application becomes overloaded, scale only the services you need to
=> Fault tolerant: a single error in one of your containers shouldn't bring down your entire app
=> Easy to maintain
## ECS
- A container orchestration service which supports Docker and Windows container
- Quickly deploy and scale containerized workloads without having to install, configure, manage and scale your own orchestration platform
- Similar to Kubernetes but with deep integration with AWS services e.g IAM, VPC, Route53
## Fargate or EC2
- Clusters of virtual machine: ECS will run your containers on clusters of virtual machine
- Fargate for serverless containers and you don't need to worry about the underlying EC2 
- If you want to control the installation, configuration and management of your compute environment then use EC2
## Elastic Container Registry
- A place or a registry where you store your container image
- ECS connect to the registry and uses the image to deploy Docker container
- Services using ECS:
	- Amazon Sagemaker: quickly deploy and scale machine learning models for training and inference jobs
	- Amazon Lex: uses deep learning to build conversation interfaces e.g. chatbot
	- amazon.com: amazon's own recommendation engine is running on ecs

# Deploying Docker using Elastic Beanstalk
- Elastic Beanstalk supports the deployment of Docker containers
- Docker containers are self-contained and include all the configuration information software your web app requires to run
- Beanstalk handles the capacity provisioning, load balancer, scaling, app health monitoring
=> You can deploy your docker container to a single EC2 instance
=> You can deploy multiple Docker instances to an ECS cluster
=> To deploy a Docker application just upload your code bundle to Beanstalk
=> Code can be uploaded directly from your local machine or a public S3 bucket
=> You can also store your code in CodeCommit - but must use Beanstalk CLI
