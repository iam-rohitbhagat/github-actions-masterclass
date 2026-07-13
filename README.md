GitHub Actions:
===============
.guthub/workflows/multiple workflow file with yml extention

First workflow:

name: first GitHub actions workflow
on: workflow_dispatch	#we should manually trigger this
jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
	- name: hello step
	  run: echo "Welcome  to GHA"

#For multiple command to run in single steps:

	steps:
	   - name: hello step
	     run: |
		echo "this is first line"
		echo "this is second line"
#creating multiple jobs in a single workflow:

name: multijob workflow
on: workflow_dispatch
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: build step
        run: echo "A demo build"
  
  test:
    runs-on: ubuntu-latest
    steps:
      - name: test step
        run: echo "A demo test"
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: deploy step
        run: echo "A demo deploy"
==============================================
# How to run jobs in sequential:
we use needs keyword

name: hello world
on: workflow_dispatch
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: build step
        run: echo "A demo build"

  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: test step
        run: echo "A demo test"

  deploy:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - name: Deploy step
        run: |
          echo " A dempo Deploy on prod1"
          echo " Amo deploy on prod2"
===================================================

# GitHub actions Workflow Triggers & Types:

1. Event-based triggers: push, pull_request
2. Manual triggers
3. Scheduled triggers
4. workflow trigger

=======================================================

working with variables and working in workflows:

#vault kv put secret/myapp username=admin password=Pass123

For accessing variables:
${{ vars.variable_name }}

For accessing Secrets: it will be masked
${{ secrets.secretsName }}

=======================================================


Action in GitHub workflow:

to checkout the code we use:
uses: actions/checkout@v4

Example of workflow:
name: Github actions demo
on: workflow_dispatch
jobs:
  actions:
    runs-on: ubuntu-latest
    steps:
      - name: checkout code
        uses: actions/checkout@v4
      - name: show index.html data
        run: cat index.html

======================================================
Deploy a static Website to S3 Bucket:
		1. Configure S3 bucket to host a static website
		2. Writegithub actions workflow to deploy to s3 bucket.


to configure AWS credentials we have build in action

uses: aws-actions/configure-aws-credentials@v4
with:
  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}


Workflow:

name: deploy static website to s3
on:
  push:
    branches:
      - main
      - develop
      - QA
jobs:
  deploy-static:
    runs-on: ubuntu-latest
    steps:
      - name: checkout the code from repo
        uses: actions/checkout@v4
      - name: configure aws credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-east-1
       - name: copy static code to s3
         run: |
           aws s3 sync ./ s3://jhc-gha-static-website-demo/ \
           --exclude "*" \
           --include "index.html"|

===================================================================================

Environment variable ;

  Environment variable at workflow level--->third highest precedences
  environment variable at job level second highest precedence
  environment variable at step level--->highest precedence 

Using inputs/Parameter in a workflow files:\
this must be manual trigger.


Example workflow:

name: input demo
on:
  workflow-dispatch:
    inputs:
      environment:
      description: "adding environment"
      required: true
      default: dev   
      type: choice
      options:
        - dev
        - test
        - prod
   artifact_tags:
     description: "tag to deploy"
     required: true
     type: string
jobs:
  input-job:
    runs-on: ubuntu-latest
    steps:
      - name: "input job to run"
        run: echo "deploying tag ${{ inputs.artifact_tags }} to ${{ inputs.environment }" 

========================================================================================

Understanding context variables in GitHub actions:
 toJson is object use to print entire GitHub variables:

${{ tojson(GitHub) }}

Environment variable:
$GITUB_SHA
==========================
Outputs in GitHub workflow:
	outputs are values returned by one task which is used by another task
        using outputs we can share data between tasks/steps
        sharing data between jobs

$GITHUB_OUTPUT is file file where we store output to use in rquired place in the workflow.
syntax is steps.stepId.outputs.nameofvariable

name: output demo
on: push
jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: output store in github_output file
        id: step1
        run: echo "version=0.0.1" >> $GITHUB_OUTPUT
      - name: using output in step2
        run: echo "the version is ${{ steps.step1.output.version }}"


     