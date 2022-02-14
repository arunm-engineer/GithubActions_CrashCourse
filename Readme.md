# Github Actions
#### 1. To create your CI/CD pipeline
#### 2. To automate your software development process => Building, Testing, Deploying

### Pre-requisites
#### 1. Basics of Git [Push, Commit, Pull-request, Merge, Branches]
#### 2. YAML syntax

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
        # So, above we have ${{}} which indicates for dynamic value assigned to output (just like JS template literal string).
        # Here, to access output github has many objects pre-defined which can be used,
        # "steps" an object which have all steps detail so to target "hello_world" step we used it, by giving "id" to identify, 
        # then "outputs" is output from the referred step, then finally the variable "time" which is defined in action itself which contains the exact output
```

#### `Note` [ Refer this link to know more about this action](https://github.com/actions/hello-world-javascript-action)

![Alt text](./resources/ref-2.png?raw=true "Optional Title")
![Alt text](./resources/ref-3.png?raw=true "Optional Title")