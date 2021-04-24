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
