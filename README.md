# hms-build-environment

- [hms-build-environment](#hms-build-environment)
  - [Overview](#overview)
  - [Usage](#usage)
  - [Tooling installed](#tooling-installed)
    - [Alpine packages](#alpine-packages)
    - [PIP Packages](#pip-packages)
    - [Other tools](#other-tools)
  - [Release model](#release-model)

## Overview
The Dockerfile inside of this repo is used to produce a container image that contains a base set of tooling for use when building HMS artifacts.

**Question:** Why build and publish an intermediate image to Artifactory, instead of building the entire container image on demand during an a Github Action workflow run?

**Answer:** By building and publishing an intermediate image to Artifactory we can speed up the how long it takes for Github Action workflows runs take to execute, as it doesn't need to pull down and install the same set of packages and tools every time. As an added benefit it will help prevent our builds from getting rate limited when downloading and installing required tools. 

## Usage
The currently expected usage pattern for the hms-build-environment container image is for a [Docker container based Github Action](https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action) to use the hms-build-environment image as a base for installing the action's special tooling into (if any).

The following is an example [Dockerfile from the hms-build-changed-charts-action](https://github.com/Cray-HPE/hms-build-changed-charts-action/blob/main/Dockerfile):
```Dockerfile
FROM artifactory.algol60.net/csm-docker/stable/hms-build-environment:1.0.0

COPY ./scripts/ /usr/local/bin/
WORKDIR /workspace
```

## Tooling installed
### Alpine packages
The following Alpine packages are installed:
- bash
- python3
- py3-pip
- git
- openssh
- curl 
- jq
- yamllint

### PIP Packages
- yamale

### Other tools
| Tool          | Version  |
| ------------- | -------- |
| kubectl       | v1.19.15 |
| helm          | v3.7.1   | 
| chart-testing | v3.4.0   |
| yq            | v4.14.1  | 

## Release model

When you make changes you should tag the code branch with an vX.Y.Z semver. A stable build of hms-build-environment will only occur when a Git tag is created, all other builds are considered to be unstable.
