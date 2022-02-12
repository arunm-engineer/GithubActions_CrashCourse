# Github Actions
#### 1. To create your CI/CD pipeline
#### 2. To automate your software development process => Building, Testing, Deploying

## Flow in Github Actions
### Terminologies to know while working with github actions
#### 1. Events
#### 2. Action
#### 3. Workflows
#### 4. Runners

### Events
#### In github, we have certain pre-defined tasks which are referred to as events. That is, push, pull request, merge all resemble events. Whenever any event occurs, we can perform certain actions to be carried out.

### Actions
#### From the above github events, we can trigger certain actions which we have pre-defined in Github Actions. We can either pick pre-defined actions from `Github Marketplace` or we can create our own custom actions in JavaScript Lang itself.
#### Examples of actions, let's say you want to use a pre-defined template of a particular process; that is deploying to Digita Ocean or Heroku (since the process requires authentication, stuff like that, eases our work); or Sending a feedback message to the Dev Team Slack Channel whenever our CI/CD Pipeline fails or succeeds.

### Workflows
#### Workflow is nothing but a yaml file in which you write your entire process to carried out. Let's say you want to execute all of the build process in a workflow with your build commands `npm run build` for react in so-called build.yml file. Then to test you app create a another workflow file test.yml with your test commands and likewise.
#### `Note:` Your workflows are yaml files and the folder structure to place these files so that github could recognize and perform operations is mentioned below.
#### In the root folder of your project (i.e. root of your github repo), create `.github` directory and in that create `workflows` directory, then place all your .yml files i.e. your workflows in that `workflows directory`. `.github > workflows > build.yml`
