# CI/CD overview
- Software development best practice
- Make small changes and automate everything: small, incremental code changes. Automates as much as possible. E.g: code integration, build, test, deployment
- Modern companies have been using CI/CD

## Continuous Integration workflow
- A shared code repository. Frequently merging.
- Auomated build: when changes appear in the code repo this triggers an automated build of the new code
- Automated test: run automated tests to check the code locally
## Continuous Delivery & Deployment workflow
- Code is merged: after successful tests, the code gets merged to the master repo
- Prepared for deployment: code is build, tested and packaged for deployment
- Manual decision: Human may be involved in the decision to deploy the code => Continuous delivery
- Automated => continuous deployment
## AWS tools
- CodeCommit: source & version control service enabling teams to collaborate on code, html page, scripts, images and binaries
- CodeBuild: automated build service that compiles source code, runs tests and produces packages that are ready to deploy
- CodeDeploy: automated deployment service that deploy code to any instance including EC2, Lambda or on-premises
- CodePipeline: manages the workflow, end-to-end solution, build, test, and deploy your application everytime there is a code change