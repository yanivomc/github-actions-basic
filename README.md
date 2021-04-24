# GITHUB ACTIONS NOTES
### Each workflow must have a name
workflow will be named as Sample Workflow
~~~
name: Sample workflow
~~~

#### ON
Each workflow is triggered on some events.
this one will be triggered on push
~~~~
on: push 
~~~~
will be triggered on push and create pull_request
~~~~
on: [push, pull_request] 
~~~~

#####We can also have some configuration like this
~~~~
on:
  push: # will be triggered for any push
  pull_request:
    types: [opened] # will be triggered when a pull request is created
  issues:
    types: [opened] # will be triggered when a issue is created
  issue_comment:
    types: [deleted] # will be triggered when a comment in a issue is deleted
~~~~    
####We can trigger any workflow using schedule too
~~~~
on:
  schedule: 0 12 * * * # this will be triggered on 12pm (UTC) each day 
~~~~

#### We can filter out branches and tags too
~~~~
on:
  push:
    tags: # will be triggered when v1.0 or v2.0 is pushed.
      - v1.0
      - v2.0
    branches: # will be triggered when code is pushed to master or any branch starting with release/
      - master
      - release/*
  pull_request:
    branches: # will be triggered when a pull request is submitted to develop or master branch
     - develop
     - master
~~~~

###### we can also ignore branches and tags too by simply using branches-ignore instead of branches
same goes for tags-ignore in place of tags
we can not use branches-ignore and branches together and same of tags
~~~~
on:
  push:
    branches-ignore:
      - feature/* # will not trigger for any push to feature/ branches
    tags-ignore:
      - v1.* # will not trigger any version like v1.1, v1.5, v1.8

~~~~


###ENVS
We can have environment variables in the workflow
this will be available in each job and steps inside it.
~~~~
env:
  PROJECT_ID: sample-project-id
  USERNAME: sample-username
~~~~

So now in each steps you can access them by 
~~~~
$PROJECT_ID or $USERNAME
~~~~
Normally the environment variables will show their value when printed in logs

We might want to hide sensitive information in logs like password - For this use case, we will use <b>secrets</b>. 

we can set secrets under Variables in each repository settings:

#####Repo --> Settings --> Variables
once we set it up in our repo,
we can address it as followed:

~~~~
env:
  PROJECT_ID: sample-project-id
  USERNAME: sample-username
  PASSWORD: ${{secrets.PASSWORD}} # you have to set PASSWORD in variables under repo settings 
~~~~
We can have env variables in jobs and steps too.  


####Jobs
there might be multiple jobs definition under jobs. 

this is the job annotation. It can contain multiple job
in each job definition runs-on and steps are required.
~~~~
jobs: 
  # name of the job to be shown in GitHub
  first-job: 
    # os in which the job will run
    # Possible values are ubuntu-latest, windows-latest, ubuntu-16.04
    # there might be many version.
    runs-on: ubuntu-latest # required
    # job specific env variables
    # this env variables won't be available to second-job
    env:
      ENV_VAR: hello-world
      SECOND_VAR: hello-world
    # I will explain the steps separately. Generally steps are list of objects.
    steps: # required
      - 
  second-job:
    runs-on: windows-latest
    steps:
      - 
      
 ~~~~     
      
One more interesting thing we can do is to run a job in different os!

For this, you need to strategy under each job config
~~~~
jobs:
  first-job:
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
    # This will allow the job to run in 3 different os
    runs-on: ${{ matrix.os }}
    steps:
      - 
~~~~~
    
Apart from this we can also run all the steps in a docker container!! which is what we came here for.

~~~~

jobs:
  first-job:
    runs-on: ubuntu-latest
    container:
      # this will run all the steps in this job inside a python:3 docker container
      image: "python:3"
    steps:
     - 
~~~~ 
     
By default all the jobs that are defined under jobs keyword. Sometime we need to start a job after a job is finished.


for this we need the needs keyword

~~~~
jobs:
  # as first-job and fourth-job don't have any needs keywords
  # they will start together
  first-job:
  second-job:
    # only run when first-job is successful
    needs: [first-job]
  third-job:
    # only run when first-job and second-job is successful
    needs: [first-job, second-job]
  fourth-job:
  
~~~~

also sometimes you need docker container to be started as sidecar for a job to run
~~~~

jobs:
  first-job:
    services:
      postgres:
        image: postgres # docker image name
        ports:
          - "5432:5432" #port exposed
      redis:
        image: redis


~~~~