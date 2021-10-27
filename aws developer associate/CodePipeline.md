# Overview
- Orchestrates build, test, deployment
- The pipeline is triggered everytime there is a change to your code
- Automated release process: fast, consistent, fewer mistakes. Enables quick release 
- CodePipeline integrates with CodeCommit, CodeBuild, CodeDeploy, Github, Jenkins, Elastic Beanstalk, CloudFormation, Lambda, Elastic Container Service
## Example CodePipeline Workflow
- Workflow is defined: the workflow begins when there is a change detected in your source code
- New code appears in CodeCommit
- CodeBuild immediately compiles source code, rbuild, run, tests
- CodeDeploy deploys everything
- 