# Introduction

While it is possible now to run applications on the real cloud, it would be nice to have the option to run and debug them right on the development computer.

ANODE can run on the development machine. It becomes just like another single instance farm in your cluster.

In development environment you can execute all applications just like they are executed on Azure. You can also use ANODE dashboard running in development environment. Besides local debugging, development environment lets you to browse logs for other farms in your cluster, even if the farm itself is not accessible for some reason.

# Setup steps

The master branch of the cluster repository bears settings for development environment. Set your current branch of the repository to ```master```.

## Configure farm setting for development environment

Your ```farms.json``` in ```rebus``` directory on the ```master``` branch of cluster repository should look like https://github.com/anodejs/sample-cluster/blob/master/rebus/farm.json

Set Azure storage account name and keys.

Push changes to the origin.

## Setup emulated topology

Copy topology configuration file ```topology.json``` from https://github.com/anodejs/sample-cluster/blob/master/rebus/topology.json to ```rebus``` directory of the ```master``` branch of the cluster repository. On Azure, this file is generated from Azure configuration. For development environment it should be present in the directory. The file emulates environment of a single instance of your emulated farm.

## Setup empty deployment settings

Copy empty deployment settings from https://github.com/anodejs/sample-cluster/blob/master/rebus/deploy.json to ```rebus``` directory on the ```master``` branch of the cluster repository.

On the development environment, deployment is not invoked. Instead, developers setup ANODE repositories themselves.

## Setup logging configuration.

Copy development environment logging configuration from https://github.com/anodejs/sample-cluster/blob/master/rebus/log.json to to ```rebus``` directory of the ```master``` branch of the cluster repository. 

In addition to Azure table logging transport, the configuration for development environment lists settings for the console transport, which lets you see logs live in console window.

# Developers guide