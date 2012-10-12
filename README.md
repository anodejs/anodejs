# Introduction

ANODE is a PaaS (Platform as a Service) for developing and hosting node.js WEB applications on Azure.

Azure already offers great integration with node.js and git. See http://go.microsoft.com/fwlink/?LinkId=254424&clcid=0x409. 

ANODE adds more [features](https://github.com/anodejs/anodejs#features), like multitenancy, dashboard, logging, github integration and a few more.

ANODE is available as free open source in github. You can clone all ANODE code and have full control over the platform. The steps [below](https://github.com/anodejs/anodejs#setup-anode-farm) guide you in creating your own ANODE deployment.

## Features

In a nutshell, ANODE offers the following features:

* Multitenancy - ANODE hosts multiple versions of multiple applications, from git repositories and branches, side-by-side.
* Instantaneous deployment - push into git leads to immediate service update in the cloud. ANODE is integrated with github (and bitbucket), supports and leverages github development process.
* Security - ANODE wraps all security handling and exposes HTTPS endpoint. ANODE applications are HTTP servers, agnostic of security.
* Instantaneous logging - ANODE offers instant viewing and querying logs from applications.
* Management dashboard - all management is performed via ANODE's management dashboard. The dashboard is extendable and can be used for managing applications.
* Scale out - ANODE symmetrically deploys applications on Azure instances. Scaleout is opaque to applications.
* Interoperability - ANODE allows applications to collaborate with each other. ANODE itself is implemented as a number of node.js applications.
* Services - there are multiple services offered by ANODE, e.g. scheduling delayed jobs.
* Testing - ANODE supports test suites development and execution.

## Terminology

* ANODE farm - Azure cloud service with one production deployment of ANODE role running on several instances.
* Instance - Azure instance (aka VM). We use "Small" sized instances.
* ANODE cluster - Set of several ANODE farms sharing common storage account.

## Prerequisites

You need Git to be installed on your development computer. Follow the [these steps](https://help.github.com/articles/set-up-git) if you don't have Git yet.

# Setup ANODE farm

* The 1st phase - [setup basic functional ANODE farm](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md)
* The 2nd phase - [secure the farm](https://github.com/anodejs/anodejs/blob/master/docs/SECURITY_SETUP.md)
* The 3rd phase - [start deploying applications](https://github.com/anodejs/anodejs/blob/master/docs/APPREPO_SETUP.md)
* The 4th phase - [setup development environment](https://github.com/anodejs/anodejs/blob/master/docs/DEVENV_SETUP.md)
* [Troubleshooting](https://github.com/anodejs/anodejs/blob/master/docs/TROUBLESHOOTING_SETUP.md)

# [Reference](https://github.com/anodejs/anodejs/blob/master/docs/REFERENCE.md)