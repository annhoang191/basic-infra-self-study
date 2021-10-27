# Overview
- A central code repository: store source code binaries, libraries, images, html files
- Basically like Github
- Allow multipe developers work on code at the same time
- Version control

# Create CodeCommit repo
- Select service CodeCommit -> Repositories -> Create repository 
- Commit change to repo
- Create branch
- Create a codecommit repo with cli
  ```
 aws codecommit create-repository --repository-name RepoFromCLI --repository-description "My demonstration repository"
    ```
