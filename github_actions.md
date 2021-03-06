# Github Actions
- Tool for automation of software workflows
- Uses YAML for script generation

### YAML Summary
- A programming language used for serialization of data
- Acronym for Yaml Ain't a Markup Language
- Files are denoted by .yml or .yaml
- Formatting includes indentation, colons and dashes

### Getting Started
- Create a new repository in Github
- Navigate to the repository and click on Actions tab
- Use Simple workflow by clicking on 'Set up this workflow' button
- A file with certain yaml content will be created on new screen, with intention to print a greeting message
- Click on 'Start commit' and then 'Commit' to add the file to repository
- Navigate to 'Actions' tab to check that our newly created job is added there
- Screen shows history of all the jobs ran the in the past
- Click on one of the job and examine different steps executed, that were defined in the yaml file that we committed earlier
- If you examine, there will be steps for printing single line and multi-line messages, that were defined in the job earlier

### Attributes
![Sample workflow](https://github.com/akashkj/learning-notes/blob/github-actions/images/sample_workflow.png)

Following are the attributes defined in a workflow:
- **name** 
  - To provide name of the workflow
  - It is helpful when there are multiple workflows in singel repository
  - Optional field.
- **on** 
  - Defines an event that will trigger the workflow. 
  - It is a required attribute. E.g. push, pull_request, release
  - Webhooks can also be defined to trigger a workflow. These are not directly related to code change but are associted with repo. E.g. branch creation/deleteion, issues opened/resolved, members join/leave a project
  - Workflows can also be scheduled to trigger using various methods such as cron format
  - [List of supported events](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows)
- **jobs** 
  - There must be at least one job defined for a workflow
  - Each job is identified by a string identifier i.e. a job name
  - Job name can have only alphanumeric, dashes or underscores. It must start with a letter or a underscore
  - Jobs run in parallel by default, with an option to execute them sequentially
  - **runs-on** 
    - Specify the type of machine needed to run the job. E.g. Windows Server 2019, Ubuntu 18.04, Ubuntu 16.04, macOS Catalina 10.15, etc.
    - Self hosted runners can also be used instead
    - Each job can be run on a different runner
    - [Supported runners](https://docs.github.com/en/free-pro-team@latest/actions/reference/specifications-for-github-hosted-runners#supported-runners-and-hardware-resources)
  - **steps**
    - Defines the list of actions or commands to be run into the environment defined in previous attribute
    - Each step has access to the environment file system
    - Each step run in its own process
    - **uses**
      - Specify an action to be used by the job
      - Actions are a collection of code used to perform a specific task or operation
      - Actions are docker images
      - This tag tells workflow how to find the action needed by the step
      - Actions can be specified from:
        - Github marketplace
        - Same repository
          - Syntax: ./path/to/action
          - Example: ```uses: ./.github/actions/some-action```
        - Another public github repository
          - Syntax: {owner}/{repo}@{ref}
          - Example: ```uses: monacorp/action-name@master```
        - A docker image registry
          - Syntax: docker://{image}:{tag}
          - Example: ```uses: docker://heloo-world:latest```
    - **run**
      - Alternative of using action for a step
      - It executes a command or a series of commands in a shell on the virtual environment
      - Single line command
        - Syntax: {command} {parameters} {arguments}
        - Example: run: mv ./output ./archive
      - Multi line command
      ![Multi line command](https://github.com/akashkj/learning-notes/blob/github-actions/images/command.png)
    - **name**
      - Used in conjunction with uses and runs command to identify a step
      - It is an optional field
  

### Adding dependencies between Actions
- By default, jobs run in parallel
- A dependency can be added from one job to another in order to run them sequentially
- **needs**
  - An attribute used to define dependency of a job
  - It defines one or more jobs that must be completed successfully before current job is triggered
  - Example: Job 3 needs to run after Job 1 and Job 2 are executed
  - ![Jobs dependency](https://github.com/akashkj/learning-notes/blob/github-actions/images/jobs.png)
  
### Add conditions to a workflow
- We can define different trigger conditions of a workflow
- E.g. When we want to have different trigger events for different branches
```
on:
   push:
      branches:
         - develop
         - master
   pull_request:
      branches:
         - master
```


### Workflow and Action Limitations
- A repository can have maximum of 20 workflows running concurrently, with no limit on no. of workflows
- Concurrency limit is higher on upgraded github plans
- A job can have maximum runtime of 6 hours
- A job can make maximum of 1000 requests per hour to github's API
- Actions can't trigger other workflows
- Action log files can be of maximum 64 KB


### Actions
- From marketplace
  - When editing/adding a workflow, marketplace pane ia available that lists various Actions offered by github
  ![Marketplace](https://github.com/akashkj/learning-notes/blob/github-actions/images/marketplace.png)
- From a repository. Can be:
  - Self repo
  - Another repo
  - Docker image registry
- Passing arguments to an action
  - **with** attribute is used to pass arguments to an action
  - Creates a new block for mapping arguments to input
  - Syntax:
  ```
  uses: {repo}/{action-name}
  with:
        key:value
        key:value
  ```
  - Example - passing arguement to github's checkout action that checks out code from a github location. If these arguements are not provided, checkout action checks out the current reposiroty code, where action is running.
  ```
  steps:
     - name: Checkout the code
       uses: actions/checkout@v2
       with:
          repository: apache/tomcat
          ref: master
          path: ./tomcat
  ```
- Using environment variables
  - Case sensitive key-value pairs that are injected during runtime in virtual environment
  - [Default environment variables defined by github](https://docs.github.com/en/free-pro-team@latest/actions/reference/environment-variables#default-environment-variables) These env. variables are available to every step of the workflow.
  - To inject custom environment variables, use **env** attribute
  - Scope of custom variables can be workflow, job or step based on where it is defined
  - Accessed using:
    - Shell variable syntax:
      - Example, for bash: ```$VARIABLE_NAME```
      - Example, for powershell: ```$Env:VARIABLE_NAME```
    - YAML Syntax
      - Syntax: ```${{ env.VARIABLE_NAME }}```
      - Variable is read from the workflow
      - Variabes can be used in workflow configuration also
      - Variables defined at the step level can't be used in that step
- Using secrets
  - Used to store environment variables in encrypted format
  - Can be defined in the repository where workflow is defined
  - Step: Settings > Secrets > Add a secret
  - Access using: ```${{ secrets.SECRET_NAME }}```
  - Limits:
    - Can't be viewed, once created
    - A workflow cna have up to 100 secrets
    - Size limit of 64 KB. For a larger secret, it can be stored as a file in exrypted format and its decryption key can be passed as a secret to workflow.
    - Must be explicity passed to a step or action, they are not available by default.
- Using artifacts
  - Created using data preserved from a workflow
  - It can be a file or a collection of files such as comiled binaries, archives, test results, log files, etc.
  - Can be used as a shared data between workflow jobs. E.g. Job 1 create and upload artifact and job 2 uses that artifact
  - An artifact can only be uploaded by a workflow ```actions/upload-artifact```
  - An artifct can only be downloaded by a workflow ```actions/download-artifact```
  - Artifact can also be downloaded manually afte workflow is completed
  - Free github account has a limit of 500 MB storage for artifacts with a retention period of 90 days
  
  
### Developing a CI worflow
Following steps can be used to define a decent workflow for continous integration of a project. Different jobs can be defined to accomodate these steps.
- Linting
```
- name: Lint code
  run: |
     flake8 --ignore=E501,E231 *.py
     pylint --disable=C0301 --disable=C0326 *.py
```
- Unit tests
```
- name: Run unit tests
  run: |
     python -m unitest --verbose --failfast
```
- Building and managing artifacts
Here we are using Google Cloud Platform to build and push the image, by utilizing secrets.
```
- name: Setup GCloud
  uses: GoogleCloudPlatform/github-actions/setup-gcloud@master
  with:
     version: '274.0.1'
     service_account_email: ${{ secrets.GCP_PROJECT_ID }}
     service_account_key: ${{ secrets.GCP_SERVICE_ACCT_KEY }}
- run: |
     gloud config set project ${{ secrets.GCP_PROJECT_ID }}
     gloud config set run/region ${{ secrets.GCP_REGION }}
     gcloud auth configure-docker
     gcloud info
     
- name: Build and tag image
  run: docker build -t "gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ env.APPLICATION_NAME }}:latest" .
  
- name: Push to GCP image registry
  run: docker push gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ env.APPLICATION_NAME }}:latest
```
- Testing
```
test_image:
   needs: [build_image] # Adding dependency on build image job to ensure testing is done only after image is successfully built
   runs-on: ubuntu-18.04
   steps:
   - name: Setup GCloud
     # Use the same process as done above to setup GCloud account access
     
   - name: run unit tests in container
     run: docker run "gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ env.APPLICATION_NAME }}:latest" -m unittest --verbose --failfast   
```
- Deployment
```
deploy:
   needs: [test_image]
   runs-on: ubuntu-18.04
   steps:
   - name: Setup GCloud
     # Use the same process as done above to setup GCloud account access
     
   - name: Deploy to Cloud Run
     run: gcloud run deploy ${{ env.APPLICATION_NAME }} --image=gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ env.APPLICATION_NAME }}:latest
     
   - name: Test Deployment
     run: |
        DEPLOY_URL=$(gcloud run services describe app2 --platform=managed --region=us-central1 | grep https)
        curl -sL --max-time 300 -o /dev/null -w "%{http_code}" $DEPLOY_URL | grep 200 || exit 1
```
- Adding a status badge
  - Added usually in the readme page to give brief stats of the CI
  - Format: ``` https://github.com/<OWNER>/<REPOSITORY>/workflows/<WORKFLOW_NAME>/badge.svg ```
  
  
## Creation of a Custom Action
**Step 1: Defining an objective** 
<br/>
This example action will be used to create a release in github, using a keyword defined in the commit message

**Step 2: Adding a Dockerfile**
```
FROM alpine

RUN apk add --no-cache \
        bash           \
        httpie         \
        jq &&          \
        which bash &&  \
        which http &&  \
        which jq

COPY entrypoint.sh /usr/local/bin/entrypoint.sh
COPY sample_push_event.json /sample_push_event.json

ENTRYPOINT ["entrypoint.sh"]
```
To create image, run:
``` docker build --tag keyword-release . ```

**Step 3: Adding an entry point script**
<br>
A shell script that looks for keyword 'FIXED', created at same level as docker file
```
#!/bin/bash
set -e

# if keyword is found
if echo "$*" | grep -i -q FIXED;
then
    # do something
    echo "Found keyword."
# otherwise
else
    # exit gracefully
    echo "Nothing to process."
fi
```

**Step 4: Using runtime environment resources**
<br>
Multiple resources are available at runtime, such as:
- Compute resources
  - One virtual CPU
  - Memory up to 3.75 GB
- Local file system
  - Read and write access
  - Disk space up to 100 GB with access to
    - /github/home
    - /github/workspace
- Environment variables - [List](https://docs.github.com/en/free-pro-team@latest/actions/reference/environment-variables#default-environment-variables)
- Event payloads
  - Many events are available to trigger the worklow.
    - E.g. push, create branch, delete branch, pull request, etc.
    - [Available Webhooks](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows#webhook-events)
  - Ech event has a different payload
- Remote network connectivity, to connect to external APIs or package repositories

**Step 5: Local testing of Action**
Create a Makefile with content:
```
KEYWORD=FIXED
run: build
	docker run --rm keyword-release-action $(KEYWORD)
build:
	docker build --tag keyword-release-action .
test:
	./entrypoint.sh $(KEYWORD)
```

#### References: 
- [Linkedin: Learning Github Actions](https://www.linkedin.com/learning/learning-github-actions-2)
- [Github Actions Documentation](https://docs.github.com/en/free-pro-team@latest/actions)
