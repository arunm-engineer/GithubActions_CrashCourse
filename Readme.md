# Github Actions
#### 1. To create your CI/CD pipeline
#### 2. To automate your software development process => Building, Testing, Deploying

### Pre-requisites
#### 1. Basics of Git [Push, Commit, Pull-request, Merge, Branches]
#### 2. YAML syntax

## YAML Syntax
#### YAML documents are basically a `collection of key-value pairs`. The value can be a simple string or can also be a collection further which has further more key-value pairs. In YAML `indentation` plays a major role to define structure of the YAML document. Tabs are not allowed and the amount of whitespace doesn't matter as long as the child node is more indented than the parent.

#### Some of the datatypes we can have as values:
#### 1. Strings
#### 2. Number
#### 3. Float
#### 4. Boolean
#### 5. Null
#### 6. Complex Datatypes (dates and timestamps)

```yml
pepcoding:
  # Strings can be with or without quotes
  name: 'John'
  department: Development
  # For multiline strings
  domains: |
    JavaScript
    Browser
    React
    Backend
    Devops
  age: 100
  price: 100.00
  Course-availablity: true
  # We can represent "null" in either ways
  null-value:
  null-value: null
```

#### Lists or sequences of values => Array Like. We use hypens to represent these Lists of values.

```yml
count:
  - one
  - two
  - three
```

#### Lists of objects (Nested form) 

```yml
nineteen-eighty-four:
    author: George Orwell
    published-at: 1949-06-08
    page-count: 328
    description: |
        A Novel, often published as 1984, is a dystopian novel by English novelist George Orwell.
    count:
      - one
      - two
      - three

the-hobbit:
    author: J. R. R. Tolkien
    published-at: 1937-09-21
    page-count: 310
    description: |
        The Hobbit, or There and Back Again is a children's fantasy novel by English author J. R. R. Tolkien.
```

## Flow in Github Actions
### Terminologies to know while working with github actions
#### 1. Events
#### 2. Action
#### 3. Workflows
#### 4. Runners

### Events
#### In github, we have certain pre-defined tasks which are referred to as events. That is, push, pull request, merge all resemble events. Whenever any event occurs, we can perform certain actions to be carried out.

### Actions
#### From the above github events, we can trigger certain actions which we have pre-defined in Github Actions. We can either pick pre-defined actions from `Github Marketplace` where you can find all kinds of actions already available or we can create our own custom actions in JavaScript Lang itself.
#### Examples of actions, let's say you want to use a pre-defined template of a particular process; that is deploying to Digita Ocean or Heroku (since the process requires authentication, stuff like that, eases our work); or Sending a feedback message to the Dev Team Slack Channel whenever our CI/CD Pipeline fails or succeeds.

### Workflows
#### Workflow is nothing but a yaml file in which you write your entire process to carried out. Let's say you want to execute all of the build process in a workflow with your build commands `npm run build` for react in so-called build.yml file. Then to test you app create a another workflow file test.yml with your test commands and likewise. You can have more than one workflows and all of them will run in parallel.
#### `Note:` Your workflows are yaml files and the folder structure to place these files so that github could recognize and perform operations is mentioned below:
#### In the root folder of your project (i.e. root of your github repo), create `.github` directory and in that create `workflows` directory, then place all your .yml files i.e. your workflows in that `workflows directory`. 
#### `.github > workflows > build.yml`

### Runners
#### Runners are nothing but virtual machines (servers) hosted by github in which workflow gets executed. These runner are managed by github itself, just that the process gets executed over the virtual environment in those runners. We can also customize the virtual environment with our requirements like having OS like windows or macOS or linux and can also install our new dependencies like node and check our for those environments.
#### Also if we want customizations over hardware like some other OS then we can also execute the process over our own self-hosted runner (i.e. own server which has to be pre-installed with the Github actions software to work with.)
#### Therefore, we can have 2 types of runners
#### 1. Github hosted runners (comes with required softwares installed)
#### 2. Self-hosted runners (we have to install required softwares)

## Your first workflow
#### Breaking down further, your workflow will have 3 major properties to consider:
#### 1. Event (Already discussed let's say on push event this particular workflow will execute)
#### 2. Jobs
#### 3. Steps

### Jobs and steps
#### Basically you want to excute an entire CI/CD workflow for your project. So you will mention your individual jobs to be carried out, example build, test. And in each of those jobs you want need to mention steps to execute a job, examples you will mention your build command as a step `npm run build`, for test your step `npm test -- --coverage`. `Note:` Actually, in a workflow these jobs would be running in runner let it be windows or linux. And any job can run in any runner type you wish.

#### `.github > workflows > firstworkflow.yml`

```yml
# Use # for commenting

# Naming my workflow, it's optional to name even in jobs and steps
name: Execute simple Commands

# on is the keyword to say on this particular event, example push event
# For single event
on: push

# For multiple events, enclose in array with comma-separated
# on: [push, fork]

# All of the jobs you want to execute, theses jobs will execute parallely if they are independent jobs to execute.
# Note: Can also add "needs" as a step in a job to be dependent on another job
jobs:
  # Can have more than one jobs running
  # Name your job
  execute-linux-commands:
    # The Github hosted runner with the OS you want
    runs-on: ubuntu-latest
    # All of the steps you your actual commands which will complete your tasks
    # In these steps, you will use pre-defined Github Actions or custom actions
    steps: 
      # - hyphen indicates the array of steps you want execute one after the other
      # Naming my step
      - name: List all files & Directories
        # The command which you want to run in your terminal
        run: ls
      # The output of these steps and jobs can be found in the actions tab in your Github repository at the top.
      - name: Echo a simple string
        run: echo "Hello World!!"

  # Just executing another job parallely with another OS
  execute-windows-commands:
    runs-on: windows-latest
    steps:
      # Some common software like Git, Node, npm, curl will be pre-installed.
      - name: Check version
        # Execute multi-line commands
        run: |
          git --version
          node -v
          npm -v
```

#### Now try to make a git push in your repository which would cause push event and you would see all of your workflows being executed. Refer below image.

#### Here in the Actions tab of your repository you can find the detailed output of your workflows, jobs, steps
![Alt text](./resources/ref-1.png?raw=true "Optional Title")

## Running steps in different shells in your wokflow for each steps

```yml
name: Execute simple Commands

on: push

jobs:
  execute-windows-commands:
    runs-on: windows-latest
    steps:
      - name: Check node.js version
        run: |
          node -v
          npm -v
        shell: pwsh # powershell
      - name: Check Git version
        run: git --version
        shell: cmd # command prompt

  execute-linux-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: List all files & Directories
        run: ls
      - name: Echo a simple string
        run: echo "Hello World!!"
        shell: bash # bash
```
## A Job dependent on one or more other jobs in a workflow

#### By default all Jobs in a workflow runs parallely, so use "needs" for dependency of other jobs.

```yml
name: Execute simple Commands

on: push

jobs:
  execute-linux-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: Echo a simple string
        run: echo "Hello World!!"

  execute-windows-commands:
    runs-on: windows-latest
    steps:
      - name: Check node.js version
        run: node -v

  execute-mac-commands:
    runs-on: macos-latest
    # This "needs" shows an array of dependent job. i.e. Only after those jobs this job would execute.
    needs: ["execute-linux-commands", "execute-windows-commands"]
    steps:
      - name: Listing directories and files
        run: ls
```

#### `Note` [Refer this link for different shells and different OS](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#using-a-specific-shell)

## Using first Action in our workflow from the Github Marketplace
#### Here we will be using the `actions/hello-world-javascript-action`. This action which is already pre-defined just prints "Hello World" to your output. Note, we can also write our own customised actions like these since actions are written in JavaScript only by using some modules available by github.
#### `actions/hello-world-javascript-action`, here actions is the global user by github in which we have all actions repository are published and "hello-world-javascript-action" is one among them.
#### `Note` [ Refer this link to know more about this action](https://github.com/actions/hello-world-javascript-action)

```yml
name: Using First Action

on: push

jobs:
  execute-hello-world-action:
    runs-on: ubuntu-latest
    steps:
      - name: Simple Hello world action
        id: hello_world # Giving unique identity to this step to refer in future
         # "uses" defines which Action to be used
        #  "v1" indicates the version release of the action repository, can also use latest commit id of the action instead of "v1"
        uses: actions/hello-world-javascript-action@v1
        # Can assume an action to be a funtion, thereby the function can take input for the action
        # "with" is used to pass the input for keyword "who-to-greet" already in the action
        with: 
          who-to-greet: Alexander
      - name: Outputs of the action # Want to log the output being given by the function
        run: |
          echo "Hey this is your output from hello_world action",
          echo "${{ steps.hello_world.outputs.time }}"
        # So, above we have ${{}} called "Expression context" which indicates for dynamic value assigned to output (just like JS template literal string).
        # Here, to access output github has many objects pre-defined which can be used,
        # "steps" an object which have all steps detail so to target "hello_world" step we used it, by giving "id" to identify, 
        # then "outputs" is output from the referred step, then finally the variable "time" which is defined in action itself which contains the exact output
```

#### `Note` We also have other global objects just like "steps" which can be used in the workflow and those are "github", "job", "runner", "steps", etc...

![Alt text](./resources/ref-2.png?raw=true "Optional Title")
![Alt text](./resources/ref-3.png?raw=true "Optional Title")

## Using one of the most important action `Checkout Action`
#### When your workflow runs in the runner virtual machine it will not have the code files and folders of your repository. Let's say you want to build or test your code, for that you must have all your code files only then you can execute the build process. So checkout actions helps in pulling the code of your repository in that virtual machine and then you can follow any of the build or test or deploy process. This checkout action is officially created by Github itself.

```yml
name: Checkout our code

on: push

jobs:
  checkout-code-in-runner:
    runs-on: ubuntu-latest
    steps:
        # To verify whether the runner actually contains the code files and folder or not
      - name: Code check before Checkout Action
        # This "pwd" will be the default working directory in which our code wil be placed
        run: |
          pwd 
          ls
      - name: Checkout code using Checkout Action
        uses: actions/checkout@v1
      - name: Code check after Checkout Action
        run: |
          pwd 
          ls
```

#### This time we can see the files and folders being present in our repository logged from the below image, since the entire codebase is pulled
![Alt text](./resources/ref-4.png?raw=true "Optional Title")

## Trigger workflows only event occurs on particular branch
#### So let's say you have more number of branches associated with your project -> main, develop, bugfix and so on... Now I want to trigger the workflow only when the push event occurs in the particular "develop" branch and not for other branch events. So we need to describe these details in our workflow

```yml
name: Worflow for develop branch

# Now these below line nowhere defines any additional details for the event
# on: [push] 
# on: push

# Inorder to describe additional details we have to specify the events syntax in objects (key-value) pattern
on:
  push:
    branches: 
      - develop
      - main 
      # Want to trigger in "main" branch as well, then can put like this array of branches format
  # To trigger workflow in PR for the "main" branch
  pull_request:
    branches:
      - main

jobs:
  execute-linux-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: Echo a simple string
        run: echo "Hello World!!"
```

#### `Note` [For more additional details and parameters to consider refer this link](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#push)

## Using Environment Variables in our worlflow
#### Different scopes of environment variables are available and can be used in our workflow.
#### 1. Workflow scope (Available across workflow and can be used anywhere in jobs, steps)
#### 2. Jobs scope (Available across the job and can be used in any steps of that workflow)
#### 3. Steps scope (Available across the step and can be used in that step)
#### 4. Global scope (These are globally available env's provided by Github itself which can be used anywhere in the workflow)

```yml
name: Environment Variables in workflow

on: push
# Env for the workflow scope - available for all jobs
env:
  MY_WORKFLOW_ENV: Env for workflow scope

jobs:
  execute-my-env:
    runs-on: ubuntu-latest
    env: 
      MY_JOBS_ENV: Env only for "execute-my-env" - Job scope
    steps: 
      - name: Echo a simple string
        id: Echoing
        env:
          MY_STEP_ENV: Env only for "echoing" step - Step scope
        # Env can be accessed in same dynamic variable type
        run: |
          echo "MY_WORKFLOW_ENV: ${MY_WORKFLOW_ENV}"
          echo "MY_JOBS_ENV: ${MY_JOBS_ENV}"
          echo "MY_STEP_ENV: ${MY_STEP_ENV}"
  
  execute-global-scope-env:
    runs-on: ubuntu-latest
    # Execute self-defined env variables along with global scope env available by github
    steps: 
      - name: Check Self-defined & Global Env's
        run: |
          echo "MY_WORKFLOW_ENV: ${MY_WORKFLOW_ENV}"
          echo "MY_JOBS_ENV: ${MY_JOBS_ENV}"
          echo "MY_STEP_ENV: ${MY_STEP_ENV}"

          echo "HOME: ${HOME}"
          echo "GITHUB_WORKSPACE: ${GITHUB_WORKSPACE}"
          echo "GITHUB_ACTOR: ${GITHUB_ACTOR}"
          echo "GITHUB_REPOSITORY: ${GITHUB_REPOSITORY}"
```

#### [Refer more on Global or default environment variables from this link](https://docs.github.com/en/actions/learn-github-actions/environment-variables#default-environment-variables)
#### Refer below image for the output of the above workflow

![Alt text](./resources/ref-5.png?raw=true "Optional Title")
![Alt text](./resources/ref-6.png?raw=true "Optional Title")

## Pass Environment variables as encrypted Secrets across your workflows
#### Now we would not want to expose our environment variables, so to encrypt and make it available (access) globally across all your workflows, jobs and steps follow below steps.
#### Go to your repository in Settings tab and then Secrets in sidebar and click actions. Now add your repository secret with the key-value pair. That's it!!

![Alt text](./resources/ref-7.png?raw=true "Optional Title")

#### Now you can access the secret added in any of your workflow files

```yml
name: Secret Environment Variables in workflow

on: push
# Accessing your repo secret as an environment variable by not exposing your value
env:
  MY_SECRET_ENV: ${{ secrets.MY_REPO_SECRET }}

jobs:
  execute-my-env:
    runs-on: ubuntu-latest
    steps: 
      - name: Echo a simple string
        run: echo "Hello World!"
```

#### Being a secret the value is not exposed and logged
![Alt text](./resources/ref-8.png?raw=true "Optional Title")

## Using If conditionals in our workflows
#### So now we can use certain If conditionals in our workflow both "Jobs" level as well as at "Steps" level. Saying if so and so condition meets only then trigger the job or step.

```yml
name: Workflow - If conditionals

on: push

jobs:
  execute-linux-commands:
    runs-on: ubuntu-latest
    # Checks if the event is "push" only then this job will execute
    # If at Job level
    if: github.event_name == 'push'
    # When a step fails all other steps which have to be executed "will not" execute, since they trigger sequentially
    # So lets handle just like Exceptional handling we do in Java
    steps:
      - name: Print Hello World
        # Made a spelling mistake, so that this step fails as a result below steps will not execute
        run: eccho "Hello World"
      - name: Node.js version
        # Since above step fails, but still I want to run this step
        if: failure() # will give true if if above step fails, thereby this step will execute
        run: node -v
      - name: npm version
        run: npm -v
      - name: Git version
        run: git --version
        # Run this step always regardless of whatsoever happens in above steps - success or failure
      - name: Print Working Directory
        if: always()
        run: pwd

  execute-simple-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: List all files & Directories
        run: ls
```

![Alt text](./resources/ref-9.png?raw=true "Optional Title")

## Using Strategy and Matrix in your workflow
#### So, here if we want to run across multiple environments, let's say on Windows OS, Ubuntu OS, Mac OS,... and also want to test your project's code compatibility across all different versions on Node.js like 10, 12, 14 likewise.. So for this we would try to have different jobs for each environment OS, and then on each environment OS you will have all Node.js versions you want to test with. So here the combination count of individual environment increases. So for easier, to solve this purpose using strategy and matrix keys which are used in your workflow which will create your combination of environments and you can check your code's compatibility in workflow's execution.

```yml
name: Strategy and Matrix

on: push

jobs:
  execute-across-environments:
    # So first we will define strategy key by having the matrix (basically array which has values of the environment)
    strategy:
      matrix:
        my_os: [ubuntu-latest, windows-latest, macos-latest]
        my_node_version: [8, 10, 12, 14]
        # In matrix key you just have key-value pair where "my_os" is just a custom defined variable which has array of values
        # Array of values which it want to create the environment based on, basically these combination of these values will be replaced below
        # And combination of environment will be formed

    # Now, in this expression-context those combination of value will be palced mapping to the key mentioned below in matrix object
    runs-on: ${{ matrix.my_os }}
    steps:
      - name: Setup Node.js
        # This action below is used to setup Node.js in runners 
        uses: actions/setup-node@v1
        # Below accepts a parameter node_version which is dynamically replaced by combination of key-mapped values
        with:
          node_version: ${{ matrix.my_node_version }}
      - name: Print Node.js version
        run: node -v
```

#### So like this you can create multiple combinations of customised environments easily. And from below image you can verify all of your combination of jobs environments created and workflow executed for each individual job of environment.
![Alt text](./resources/ref-21.png?raw=true "Optional Title")

# Project 1 - React App Full CI/CD With Deployment on Heroku

## Part 1 - Creating first simple CI/CD
#### So let's create a Pipeline for our Simple ready-to-go React App for all the process and then finally deploy it over to heroku. First create your react app using the command `npx create-react-app <app-name>`. Create a repository over Github for your React App and link it. Now we will follow all of the process one-by-one in the job's steps of workflow.  First create this folder structure in your root directory of your project `.github > workflows > ci.yml`. And create your pipeline code in your ci.yml file.
#### Now include all of the steps:
#### 1. Checkout
#### 2. Node.js Setup
#### 3. Test
#### 4. Build
#### 5. Deploy

#### We will have to use certain pre-defined actions from the Github Marketplace to complete our pipeline task easily and smoothly.
#### 1. actions/checkout@v2 => To pull and checkout out our repository code in the runner
#### 2. actions/setup-node@v1 => To setup the Node.js environment in the runner, so that we can Test, Build and Deploy
#### 3. akhileshns/heroku-deploy@v3.12.12 => To ease our deployment process over Heroku


```yml
name: CI/CD for React App

# Trigger workflow only when "push" event is triggered for "main" branch
on:
  push:
    branches: [main]

jobs:
  workflow-react:
    runs-on: ubuntu-latest
    steps:
        # First we have to checkout i.e. have code from our repository into the runner
      - name: Checkout our code
        uses: actions/checkout@v2
        # Then we have to setup the node environment in the runner using Action from Marketplace
      - name: Use custom node.js version
        uses: actions/setup-node@v1
        with:
          # Passing parameter for specific Node.js version
          node-version: "14.x"
        # Installing dependencies using "npm ci" which is much preferred compared to "npm i" in CI/CD environments
      - name: Install dependencies
        run: npm ci
        # To test React app use the command which triggers "test script" in your React app
      - name: Run test
        # This below test command will also help you generate test reports of your App
        run: npm run test -- --coverage
        # Also this is a "must" Environment Variable to be passed for this step
        env:
          CI: true
        # Now "Build" process has to be performed only if the event triggered is "push" event
      - name: Build project
        if: github.event_name == 'push'
        run: npm run build
        # Similarly if "push" event then deploy React App to Heroku Platform
      - name: Deploy project
        if: github.event_name == 'push'
        # To Deploy over Heroku we have using the below action from the Marketplace
        uses: akhileshns/heroku-deploy@v3.12.12
        # Also have to pass these credentials to authenticate and safely deploy
        # We have passed these parameters using Github Secrets so that we don't expose it all over
        # So go to setting tab, secrets tab and add secrets in your repository
        with:
          # Can find API key in User's Dashbpard
          heroku_api_key: ${{ secrets.MY_HEROKU_API_KEY }}
          # Now select "unique" App name to deploy over Heroku smoothly
          heroku_app_name: ${{ secrets.MY_HEROKU_APP_NAME }}
          # Pass your Heroku registered email
          heroku_email: ${{ secrets.MY_HEROKU_EMAIL }}
```

#### Now here you can see detailed Successul reports for your CI/CD Pipeline.
![Alt text](./resources/ref-10.png?raw=true "Optional Title")

#### And here you have your Project being deployed over Heroku with the name for your Heroku app which you passed in your Github Secrets of your repository.
![Alt text](./resources/ref-11.png?raw=true "Optional Title")

## Part 2 - Involving Branches
#### So, till now we only had one "main" branch into which we pushed our code and eventually our workflow was triggered and the processes of Test, Build and Deploy occured. But for best practice we don't make changes in "main" branch and push, instead create a different branch add your code changes (features, debug,..) and make a pull request into your main branch and then merge your code if everything works fine.
#### So we will modify the workflow in this pattern, we will have two branches now "main" (which already exists) and create a new branch "develop" (add code changes in "develop" and make a Pull request in "main"). Follow below steps:
#### Points to remember:
#### 1. We want to create a "develop" branch to make code changes
#### 2. Then we make a pull request in "main" branch, which would a trigger the workflow
#### 3. Why trigger workflow? => Since we want to check if after our code changes made, only the "Test" Step in the workflow of our React App "passes" and only after that we can think of merging the code in "main" branch without any issues
#### 4. Now after the pull request triggered workflow and passes all the "Test" Step, we will "merge" the code in our "main" branch. But we also want to trigger the workflow while "merging" the code checking all Steps => Build, Test and Deploy. Thereby, `our code with changes` after passes all steps will be deployed over Heroku.

#### Steps to proceed:
#### 1. So first create "develop" branch using `git checkout -b develop`. Then hit this command, so that your new "develop" branch is also tracked over remote Github `git push --set-upstream origin develop`
#### 2. Now go back to "main" branch using `git checkout main`
#### 3. In your workflow in ci.yml file add this event of "pull_request" in the "on" of your workflow for "main" branch so that we can trigger this workflow whenever there is a pull request made for "main" branch from any other branch (in this case from "develop" branch into the "main" branch). So your workflow after changes looks like this as below:
```yml
name: CI/CD for React App

# Trigger workflow when "push" or "pull_request" event is triggered for "main" branch
on:
  push:
    branches: [main]
  # Below 2 lines are added
  pull_request:
    branches: [main]


jobs:
  workflow-react:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout our code
        uses: actions/checkout@v2
      - name: Use custom node.js version
        uses: actions/setup-node@v1
        with:
          node-version: "14.x"
      - name: Install dependencies
        run: npm ci
      - name: Run test
        run: npm run test -- --coverage
        env:
          CI: true
      - name: Build project
        if: github.event_name == 'push'
        run: npm run build
      - name: Deploy project
        if: github.event_name == 'push'
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{ secrets.MY_HEROKU_API_KEY }}
          heroku_app_name: ${{ secrets.MY_HEROKU_APP_NAME }}
          heroku_email: ${{ secrets.MY_HEROKU_EMAIL }}
```

#### 4. Now add, commit and push in your "main" branch after making changes in your workflow using `git add . && git commit -m "Workflow change" && git push origin main`
#### 5. Now, go back to "develop" branch using `git checkout develop` and make some changes in your code or debug or add features,.. Then again commit your changes for your "develop" branch using `git add . && git commit -m "Workflow change" && git push origin develop`
#### 6. Now, go to your repository in Github and make a pull request from "develop" branch into your "main" branch and as a result your workflow will be triggered making only for the "Test" Step and not for Build or deploy. Why not for Build or Deploy, since we have already put a "if conditional" in our workflow if the event caused is a "push" event only then we must execute the Build and Deploy Steps. Check the steps and output from below images:

![Alt text](./resources/ref-12.png?raw=true "Optional Title")
![Alt text](./resources/ref-13.png?raw=true "Optional Title")

#### 7. Below you can see that some checks have to to be passed before merging your code from "develop" branch into "main" branch. And these checks are nothing but your workflow steps which have been triggered by your pull request event i.e. once all of your workflow steps passes without any failure your checks here will be successful and the "Merge Pull Request" button which is disabled now because of checks will be enabled after successful checks are passed
![Alt text](./resources/ref-14.png?raw=true "Optional Title")
![Alt text](./resources/ref-15.png?raw=true "Optional Title")

#### 8. Also you can notice only the Node setup and Test Steps and not Build and Deploy steps since we had "If" conditionals that run Build and Deploy only for "Push" event in the "main" branch
![Alt text](./resources/ref-16.png?raw=true "Optional Title")

#### 9. Since your steps of your worklow are passed your checks passes and the "Merge Pull Request" button is enabled to merge your code into "main" branch from your "develop" branch. SO just merge your pull request
![Alt text](./resources/ref-17.png?raw=true "Optional Title")
![Alt text](./resources/ref-18.png?raw=true "Optional Title")

#### 10. Now you might have noticed from above image that stil your workflow is triggered eventhough you have nowhere mentioned the "merge" event in your Github. That's because when you make a "merge", Github by default makes a "push" event in your target branch ("main" branch over here) and thereby since your workflow has "push" event your workflow runs
![Alt text](./resources/ref-19.png?raw=true "Optional Title")

#### 11. Finally all of your steps have been executed successfully and deployed over Heroku with your code changes added!! You can verify your changes going to the App's link over the Heroku platform and find your code changes made.
![Alt text](./resources/ref-20.png?raw=true "Optional Title")

# Project 2 - CI/CD of Dockerized (Containerized) React App on Digital Ocean with self-hosted runner

#### 1. In this project, we will first create a React App using `npx create-react-app <app-name>` and then containerize the React App using Docker.

#### 2. To Dockerize your React App you will have to write a `Dockerfile` in your root directory of your project. So, create a file named `Dockerfile` and put the below content in that Dockerfile.

```Dockerfile
FROM node:alpine

WORKDIR '/app'

COPY package.json .

RUN npm install

COPY . .

CMD ["npm", "start"]
```

#### 3. Now we want to use a self-hosted runner. So, for that we will have to use our own server which will create on `Digitalocean`. And then connect that server on Digitalocean with our Github to link both. So for that we will have to run some cammands provided by Github itself to install an apllication to create a self-hosted runner on the server on Digitalocean.

#### 4. First creating a droplet(server) on Digital ocean. Sign up on Digitalocean and follow below steps. Click on create and then Droplet.

![Alt text](./resources/ref-30.png?raw=true "Optional Title")

![Alt text](./resources/ref-23.png?raw=true "Optional Title")

![Alt text](./resources/ref-24.png?raw=true "Optional Title")

![Alt text](./resources/ref-25.png?raw=true "Optional Title")

![Alt text](./resources/ref-26.png?raw=true "Optional Title")

#### 5. Now, your droplet will be created. And you will find a number indicated by your below image `139.59.4.23`. You will find a different number for your server. Now that is your server's `ip-address` i.e. public ip address by which your server can be accessed over the internet.

![Alt text](./resources/ref-32.png?raw=true "Optional Title")

#### 6. First open command-prompt or powershell in your laptop. Then hit this below command. This below command will help you to connect to your server directly and you can work on that server from your machine itself. Then hit your password which you created for your server on Digitalocean.

```Powershell
ssh root@<your-ip-address>
```

![Alt text](./resources/ref-31.png?raw=true "Optional Title")

#### 7. Now we will be connected to our server on Digitalocean and we have to run some basic commands mentioned below first on the server.

```Powershell
# Creating a user, why bcoz without a different user other than "root" we cannot link this server as a self-hosted runner. After this set your password for the user created and  you can skip those pop-up questions by just clicking "Enter" key
sudo adduser <username>
# Then giving "sudo" power to the user created
sudo usermod -aG sudo <username>
```

#### 8. Then, after creating the user whenever you connect again to the server change to that new-user you created using below command.

```Powershell
sudo su - <username>
```

![Alt text](./resources/ref-38.png?raw=true "Optional Title")

#### 9. Now we have to connect Github and this server to form this server as a self-hosted runner. So go to Github, your repository's settings and refer below images. After creating the self-hosted runner we have to run those commands on the Digitalocean's server to form this as self-hosted runner.

![Alt text](./resources/ref-27.png?raw=true "Optional Title")

![Alt text](./resources/ref-28.png?raw=true "Optional Title")

#### 10. Now run those commands which is available for your on your Digitalocean's server. Refer below image.

```Powershell
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.287.1.tar.gz -L https://github.com/actions/runner/releases/download/v2.287.1/actions-runner-linux-x64-2.287.1.tar.gz
echo "8fa64384d6fdb764797503cf9885e01273179079cf837bfc2b298b1a8fd01d52  actions-runner-linux-x64-2.287.1.tar.gz" | shasum -a 256 -c
tar xzf ./actions-runner-linux-x64-2.287.1.tar.gz
./config.sh --url https://github.com/arun496/react_ci_cd --token <your-token>
./run.sh
```

![Alt text](./resources/ref-29.png?raw=true "Optional Title")

![Alt text](./resources/ref-39.png?raw=true "Optional Title")

#### 11. Now on Github you will find the name of your Digitalocean's server listed. Thereby your self-hosted runner is created. That's all about creating a self-hosted runner.

![Alt text](./resources/ref-40.png?raw=true "Optional Title")

#### 12. Now the actual part of creating the workflow i.e. the CI/CD pipeline for our Dockerized React App. So create this folder structure in the root directory of your project `.github > workflows > ci.yml`. And then put the below content in that ci.yml file which is our workflow.
#### `Note` For secrets of your workflow, MY_DOCKERHUB_USERNAME and MY_DOCKERHUB_TOKEN create an account on DockerHub and where you will get your DockerHub's username and to generate DockerHub Token refer below images. DockerHub is the central repository to store all your Docker images, just like how Github stores your code's repository.

![Alt text](./resources/ref-41.png?raw=true "Optional Title")

![Alt text](./resources/ref-42.png?raw=true "Optional Title")

![Alt text](./resources/ref-43.png?raw=true "Optional Title")

![Alt text](./resources/ref-44.png?raw=true "Optional Title")

```yml
name: CI/CD of Container

# Want to trigger workflow when "push" event occurs on "main" branch
on:
  push:
    branches: [ "main" ]

jobs:
  # First want to Build the docker Image for the App and then push the Image to the DockerHub
  # DockerHub is container registry which holds "Docker Images", just like Github how it holds code repositories
  build_and_push:
    runs-on: ubuntu-latest
    steps:
      # Since running on Github hosted runner, first we must checkout i.e. pull the code into the runner
      - name: Checkout code
        uses: actions/checkout@v2
      # Now, we want to build image for our App and push image to Docker Hub
      # But being Github hosted runner we may need to Login to Docker Hub first to get access to push Docker Image
      - name: Login to Docker hub
        # So, using an Action from Marketplace to login to Docker Hub
        # Also make sure you have an account created on Docker Hub
        uses: docker/login-action@v1
        # We need to pass parameters for validation of Docker Hub User, these parameters can be retrieved from DockerHub Website, refer images provided
        # Add these parameters in Github Secrets
        with:
          username: ${{ secrets.MY_DOCKERHUB_USERNAME }}
          password: ${{ secrets.MY_DOCKERHUB_TOKEN }}
      # Now we can build the actual Docker Image of our React App
      - name: Build container image
        # First we need to get into the working directory where our code is present after checkout
        # The we will build the Docker Image with the docker command below, which refers to a Dockerfile for instructions on how to build the Image
        run: |
            cd /home/runner/work/react_ci_cd/react_ci_cd/
            docker build -t arun496/react-frontend -f Dockerfile .
      # Now the Docker Image being built, we need to push the Docker Image to the Docker Hub using below "push" command
      - name: Push image to Docker Hub
        run: docker push arun496/react-frontend
  # Now we will get into "our hosted server" on Digital Ocean
  # Deploy our App by running the container for the Docker Image we built and pushed over Docker Hub
  deploy:
    # This time we will used the "self-hosted" runner which we configured and not Github Hosted runner
    runs-on: self-hosted
    # Before we start our "deploy" Job, we have a dependent Job to be completed first
    # So this "deploy" job will only execute after "build_and_push" job is over pushing Docker Image. So "needs" below.
    needs: build_and_push
    steps:
      # So our first move is to pull the Docker Image from Docker Hub into our DigitalOcean's self-hosted server to actually run a container
      - name: Pull Docker image
        run: docker pull arun496/react-frontend
      # Now we can run the container for the Docker Image we pulled from Docker Hub
      # That's it you can now go to your browser, enter  <your-server-ip>:<port> and you will see your React App running
      # "your-server-ip" is the Public IP Address your can find in your DigitalOcean's Dashboard
      # In this case "port" is 3000, so put <your-ip>:3000 in your browser and you will see your React App running
      - name: Run React App container of pulled Docker image
        run: docker run -p 3000:3000 arun496/react-frontend
```

![Alt text](./resources/ref-48.png?raw=true "Optional Title")

![Alt text](./resources/ref-49.png?raw=true "Optional Title")

#### 13. But the workflow which we created above is just about one time build  and then deploy. We cannot continuously change and integrate and deploy since already the old containers would be running on the server. So inorder to have continuous code changes and then integrate and deploy we first have to kill all existing containers and then run a new container with code changes already integrated in that image. So below we have the final workflow.

```yml
name: CI/CD of Container

on:
  push:
    branches: [ "main" ]

jobs:
  build_and_push:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Login to Docker hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.MY_DOCKERHUB_USERNAME }}
          password: ${{ secrets.MY_DOCKERHUB_TOKEN }}
      - name: Build container image
        run: |
            cd /home/runner/work/react_ci_cd/react_ci_cd/
            docker build -t arun496/react-frontend -f Dockerfile .
      - name: Push image to Docker Hub
        run: docker push arun496/react-frontend
  deploy:
    runs-on: self-hosted
    needs: build_and_push
    steps:
      # Now since this is a CI/CD Pipeline where we continuously develop and integrate
      # So when deploying again with your new code changes you must first remove "all" your existing containers running or in stopped state
      - name: Remove all Containers
        run: docker rm $(docker ps -aq) -f
      # So when deploying again with your new code changes you must first remove "all" your existing Docker Images pulled
      - name: Remove all images
        run: docker rmi $(docker images -q) -f
      - name: Pull Docker image
        run: docker pull arun496/react-frontend
      - name: Run React App container of pulled Docker image
        run: docker run -p 3000:3000 arun496/react-frontend
```

#### 14. Refer below images for your Github jobs successful output.

![Alt text](./resources/ref-46.png?raw=true "Optional Title")

#### 15. Finally go to your browser hit your server's ip-address from Digitalocean Dashboard and with port 3000 <your-ip:3000> and you will see your React App deployed and working in an Dockerized environment.

![Alt text](./resources/ref-45.png?raw=true "Optional Title")