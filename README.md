# Introduction

ANODE is a PaaS (Platform as a Service) for developing and hosting node.js WEB applications on Azure.

ANODE is available as free open source in github. You can clone all ANODE code and have full control over the platform. The steps [below](https://github.com/anodejs/anodejs#setup-anode-farm) guide you in creating your own ANODE deployment.

## Features

In a nutshell, ANODE offers the following features:

* Multitenancy - you can host multiple versions of multiple applications, from git repositories and branches, side-by-side.
* Instantaneous deployment - push into git leads to immediate update of the application in the cloud. Integration with github (and bitbucket). Using github development process for the full extent.
* Security - ANODE wraps all security handling and is responsible for HTTPS termination.
* Instantaneous logging - you can instantly view and query logs from applications.
* Management dashboard - all management is performed via management dashboard offered by ANODE. The dashboard is extendable, so you can use it to manage applications.
* Scaleout - ANODE symmetrically deploys your applications on Azure instances. Scaleout is opaque to application.
* Interoperability - ANODE allows applications to collaborate with each other. ANODE itself is implemented as a bunch of node.js applications.
* Services - there are multiple services offered by ANODE, e.g. scheduling delayed jobs.
* Testing - support test suites development and execution.

## Terminology

* ANODE farm - Azure cloud service with one production deployment of ANODE role running on several instances.
* Instance - Azure instance (aka VM). We use "Small" sized instances.
* ANODE cluster - Set of several ANODE farms, which share common account storage.

## Prerequisites

You need Git be installed on your development computer. Follow the [following steps](https://help.github.com/articles/set-up-git) if you don't have Git yet.

# Setup ANODE farm

* The 1st phase - [setup basic functional ANODE farm](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md)
* The 2nd phase - [secure your farm](https://github.com/anodejs/anodejs/blob/master/docs/SECURITY_SETUP.md)
* The 3rd phase - [start deploying applications](https://github.com/anodejs/anodejs/blob/master/docs/APPREPO_SETUP.md)
* The 4th phase - [setup development environment](https://github.com/anodejs/anodejs/blob/master/docs/DEVENV_SETUP.md)
* [Troubleshooting](https://github.com/anodejs/anodejs/blob/master/docs/TROUBLESHOOTING_SETUP.md)