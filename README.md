# github-actions-masterclass
===========================
github-actions-masterclass
===========================
Path to keep workflow yml files:
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
