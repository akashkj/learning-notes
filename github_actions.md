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
![Sample workflow](https://github.com/akashkj/learning-notes/blob/github-actions/sample_workflow.png)

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
  - Each job is identified by a string identifier
  - Job name can have only alphanumeric, dashes or underscores. It must start with a letter or a underscore.
  - **runs-on** 
    - Specify the type of machine needed to run the job. E.g. Windows Server 2019, Ubuntu 18.04, Ubuntu 16.04, macOS Catalina 10.15, etc.
    - Self hosted runners can also be used instead
  - **steps**
    - Defines the list of actions or commands to be run into the environment defined in previous attribute
    - Each step has access to the environment file system and runs in its own process
    - **uses**
      - Specify an action to be used by the job
      - Actions are a collection of code used to perform a specific task or operation
      - Actions are docker images
      - This tag tells workflow how to find the action needed by the step
      - Actions can be specified from the same repository or from another public github repository
    - **run**
      - Alternative of using action for a step
      - It executes a command or a series of commands in a shell on the virtual environment
    - **name**
      - Used in conjunction with uses and runs command to identify a step
      - It is an optional field
  



Reference: [Linkedin: Learning Github Actions](https://www.linkedin.com/learning/learning-github-actions-2)
