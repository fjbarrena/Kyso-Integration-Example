# Kyso Integration Example

This repository contains an example of how to integrate Kyso with Gitlab CI/CD pipelines

## Prerequisites 

* Have an account at [kyso.io](https://kyso.io)
* Have an access token, you can create it following this [instructions](https://docs.kyso.io/posting-to-kyso/kyso-command-line-tool/authorization#access-token)
* Have a valid `kyso.yaml` file. Check the following [instructions](https://docs.kyso.io/posting-to-kyso/configuring-report-metadata)

## How to integrate Kyso and Github Actions

1. Create a `.github/workflows` directory in your repository on GitHub if this directory does not already exist.
2. In the `.github/workflows` directory, create a file named `kyso-action.yml`.
3. Copy the following YAML contents into `kyso-action.yml`:

```yaml
name: Push To Kyso.io
on: [push]
jobs:
  Push-To-Kyso:
    runs-on: node:16.14.0-slim
    steps:
      - run: npm install -g kyso
      - run: kyso login --kysoInstallUrl https://kyso.io --provider kyso --username [YOUR_EMAIL] --token [YOUR_ACCESS_TOKEN]
      - run: kyso push
```

## A full example 

This repository contains a single report based on [MultiQC](https://multiqc.info/), which is basically a self-contained html file.

We are using a demo user named *Eduardo Suárez Guanes*, which have an organization named *eduardo-suarez-guanes*. That organization has a team, named *public-examples*, in which we want to upload the report.

So, based on that data, our **kyso.yaml** should be as follows, pay special attention to the **organization**, **team** and **type** properties

```yaml
main: index.html
title: "My Awesome Report uploaded using Github Actions"
description: "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas aliquam ipsum et lacus porta porttitor."
organization: eduardo-suarez-guanes
team: public-examples
type: website
```

We, as *Eduardo Suárez Guanes*, and following this [instructions](https://docs.kyso.io/posting-to-kyso/kyso-command-line-tool/authorization#access-token), have this access token `824fb205-e725-4dbf-8e1f-a2a3a3b710fd` to login into Kyso

> That token is currently revoked for security reasons ;)

Then, to upload the report to Kyso we only need to create the `kyso-action.yml` and add the following stage:

```yaml
name: Push To Kyso.io
on: [push]
jobs:
  Push-To-Kyso:
    runs-on: node:16.14.0-slim
    steps:
      - run: npm install -g kyso
      - run: kyso login --kysoInstallUrl https://kyso.io --provider kyso --username eduardo.suarez.guanes@gmail.com --token 824fb205-e725-4dbf-8e1f-a2a3a3b710fd
      - run: kyso push
```

If we execute the CI/CD pipeline we will see something similar to the next log

```shell
$ npm install -g kyso
added 234 packages, and audited 235 packages in 19s
33 packages are looking for funding
  run `npm fund` for details
found 0 vulnerabilities
$ kyso login --kysoInstallUrl https://kyso.io --provider kyso --username eduardo.suarez.guanes@gmail.com --token 824fb205-e725-4dbf-8e1f-a2a3a3b710fd
Logged successfully
$ kyso push
Uploading report '.'
Founded 4 files:
Processing .gitlab-ci.yml
Processing README.md
Processing index.html
Processing kyso.yaml
Uploading files. Wait a moment..
🎉🎉🎉 Report was uploaded to
https://kyso.io/eduardo-suarez-guanes/public-examples/my-awesome-report-uploaded-using-github-actions
🎉🎉🎉
```

As the report is public, you can see the result [here](https://kyso.io/eduardo-suarez-guanes/public-examples/my-awesome-report-uploaded-using-github-actions/share)

