---
layout: post
title: "Github Actions"
date: 2021-09-08 20:15:00 +0200
categories: jekyll update
background: "/img/posts/06.jpg"
---

## Introduction to Github Actions, Continuous Integration (CI)

---

<br/><br/>

### What is a CI Pipeline?

---

CI is a practice which facilitates merging code to the main branch and/or commiting code to your branch continuously. By introducing a CI pipeline in Github actions for example, a commit to your branch or merge attempt to the main branch (depending on what triggers you choose to use), triggers a series of automated steps. These steps may involve running the code build, unit tests, code analysis and more.

If a new branch were to pass all of the automated steps in the pipeline without any errors, a merge to the main branch is possible and may continue to the next stage e.g. deployment to the cloud. Should any of the steps fail, the development team may go through the error report, so that they may fix the issues and the merge attempt is prevented from progressing to any further stages.

As to describe this proccess, the pipeline acts as a safety net between commits that may "break" the application and a fully functional deployment.

### What benefits does CI bring to the table?

By aiming at merging to the main branch often and in short iterations, running automated builds and tests continously through out development, developers can catch any errors early in the development stage. Reducing time spent trying to find and fix bugs as well as preventing them from reaching deployment.

### How I implemented a CI pipeline into an existing project

---

As a first step, a .github directory is needed in my repository, this is where all Github specific files should be located. In this directory I introduce a workflows directory, this directory should contain my workflow yaml file, what I name this file besides from the .yaml extension is not important.

Once I have my yaml file, I start off by naming my workflow, this will be displayed in my repository's actions page once I trigger the workflow.

```yaml
name: workflow demonstration
```

The next step is to add the "on:" event which specifies on what event github should trigger the workflow.

```yaml
name: workflow demonstration
on: [push]
```

Now I have to specify what jobs and steps to run once this event is triggered, I start by naming my job. In my case I choose to build the application and run my unit tests in this job, I find: "Run build and tests" a suitable name for this.

```yaml
name: workflow demonstration
on: [push]
jobs:
  build:
    name: Run build and tests
```

Next my workflow needs to know what virtual enviroment to use to run this on, in my case i prefer to use linux ubuntu since performence is slightly faster.

```yaml
name: workflow demonstration
on: [push]
jobs:
  build:
    name: Run build and tests
    runs-on: ubuntu-latest
```

Now I have to consider what steps I need in order to run the build and to run the unit tests. In my case in order to run a build I need access to the code, which can be done by using the - uses: actions/checkout@v2 statement, this step clones the code from the repository to the virtual environment. Since this application is running on the .NET framework I need to make sure the virtual environment has that installed aswell, by using the - uses: actions/setup-dotnet@v1 statement and specifying a dotnet version to match this projects dotnet version.

My yaml file now looks like this:

```yaml
name: workflow demonstration
on: [push]
jobs:
  build:
    name: Run build and tests
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "5.0.x"
```

Now I am all set up to continue to the last two steps, running the build and unit tests. I simply tell these steps to run a dotnet build and dotnet test against the virtual environment and by providing the specific path each step should by running in. Now the final result of my workflow file end up looking like this:

```yaml
name: workflow demonstration
on: [push]
jobs:
  build:
    name: Run build and tests
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "5.0.x"
      - run: dotnet build ./Source/SpacePark.sln
      - run: dotnet test ./Source/RestApiTests
```

My automated workflow now triggers everytime I make a commit in this repository, running the steps and notifies me if any errors occurs in either the build or any of the unit tests.

### Conclusion

---

By implementing a CI pipeline to my repository, facilitates developing new features to my projects by catching errors at an early stage, reducing time spent trying to locate it. A nice feature Github Actions use, is notifying any occuring errors by email if I happen to overlook the latest commits workflow result. Although, this feature may also be irritating when getting stuck on an error, by flooding my mail with notifications.

### References

---

[semaphoreci-continuous-integration](https://semaphoreci.com/continuous-integration)

[docs.github/workflow-syntax-for-github-acitons](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)
