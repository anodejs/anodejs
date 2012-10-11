## Introduction

These steps guide you through the workflow of setup for the simplest ANODE cluster. This cluster includes only one ANODE farm, which is functional, but not yet fully secured.

## Prepare Azure Subscription

### Create Azure Subscription

If you don't have Azure subscription, you can create one. For evaluation purposes you can always use trial subscription, which is free.

### Create storage account for your ANODE cluster

Go to Azure portal and login with your LiveId, which owns Azure subscription. Navigate to storage accounts and create storage account for your ANODE cluster. You can choose any name for this storage account. Let's assume you've called it ```myanodestorage```. The information we will have to take from here is the account primary access key. It is a long string you can copy from the portal.

## Setup git

You will host ANODE and your applications in github repositories.

You will also need private git space, where you will keep ANODE secrets. For this purpose you can use github private repository. However, on github, private repository cannot be created in a free account. As an alternative you can use bitbucket to host private git repository (or repositories, if you will need private application repositories). 

The following steps guide you how to create all repositories on github. However, if you are using public repository to host secrets, you will have to redo a few settings on bitbucket. You may proceed with setting up bitbucket in advance (see [here](https://github.com/anodejs/anodejs/blob/master/docs/BITBUCKET_SETUP.md)), to avoid repeating some steps, or, if you don't want to mess with bitbucket now, to return to this after basic ANODE farm works.

If you don't have github account, create one (on https://github.com). 

### Create buddy account

We need buddy account to let farms to pull sources. The account should not have rights stronger than reading (pulling) repositories. Letting farms operate over stronger account would harm security.

Create new github account. Let's assume you've called it ```myanodebuddy```.

### Setup github organization

Github organization is useful for managing permissions for developers collaborating in building applications on your ANODE deployment.

Let's assume you've called the organization ```myanodeorg```. You already have predefined team called ```Owners```. You are the only member.

Crate two more teams:
* ```Developers``` - set push&pull permissions. Later, it will include developers' accounts and application repositories.
* ```Readonly``` - set pull permissions only. Add buddy account here. Later it can be used for private repositories, if any.

## Fork ANODE repositories

Fork 2 ANODE repositories to your github organization (__NOTE__: Pay attention you are forking to the organization account and not to your own account). The repositories are:
* https://github.com/anodejs/system
* https://github.com/anodejs/bootstrap

You will now have 2 forked repositories like those:
* https://github.com/myanodeorg/system
* https://github.com/myanodeorg/bootstrap

### Important Important Important (remove this section after system and bootstrap made public)

As long as system and bootstrap repositories are private, the forked repositories remain private as well. You should add forked repositories to your github ```Readonly``` team, which grants read permissions to buddy account. I am not sure if it will work, given you are not administrators of ```anodejs``` team, which owns parent private repositories. I hope github doesn't suck and it will not work, meaning, it will not allow granting read permissions to repositories owned by ```anodejs```. Talk to me (yosefd@microsoft.com) and we will figure out how to let your buddy account to access these repositories (probably can be solved by adding one of ```anodejs``` owners as a co-owner of your ANODE organization).

## Create ANODE cluster configuration

ANODE cluster configuration is kept in cluster repository. This repository doesn't include any code, just configuration parameters for cluster's farms.

Eventually cluster repository has to be private as it includes various secrets: Azure storage account key and certificates keys. We will return to [the issues of security](https://github.com/anodejs/anodejs/blob/master/docs/SECURITY_SETUP.md) after the basic ANODE farm would be configured.

Each farm in the cluster should have a branch in the cluster repository. ```master``` branch is reserved for development environment (consider developer's computer as another farm in ANODE cluster).

Refer to the template of cluster repository at https://github.com/anodejs/sample-cluster

### Setup cluster repository

Create new repository in the github organization. You can call it ```cluster```.

If you pay github for more advanced plan and you can create private repository. If you have free github plan, you will have to recreate cluster repository later, on [bitbucket](https://github.com/anodejs/anodejs/blob/master/docs/BITBUCKET_SETUP.md). 

Configure access rights by adding cluster repository to ```Readonly``` team.

Create branch for the farm. Let's assume you have called it ```farm```. 

Create ```rebus``` directory and place 3 json files in it:
* deploy.json
* farm.json
* log.json

```farm``` branch of ```cluster``` repository will look just like https://github.com/anodejs/sample-cluster/tree/farm

#### Update ```farm.json```

The file would look like:

```javascript
{
  "name": "myanodefarm",
  "workersDisabled": false,
  "azureStorage": {
    "account" : {
      "name" : "myanodestorage",
      "key" : "myanodestorageprimarykey"
    }
  }
}
```

Fill the farm name (same as service URL prefix), Azure storage account name and the primary key.

#### Other JSON configuration files

At the moment, there is no need to modify ```deploy.json``` and ```log.json```. They can remain as shown in the sample.

deploy.json:
```javascript
{
  "namespaces": {}
}
```

log.json:
```javascript
[
  {"name": "azuretable", "write" : true, "default": true}
]
```

## Prepare Azure deployment files

### Azure configuration

Modify the template from https://github.com/anodejs/anodejs/blob/master/resources/production/ServiceConfiguration.anode-sample.cscfg with appropriate settings. The template looks like:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="anode" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="2" osVersion="*">
  <Role name="anodejsrole">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="" />
      <Setting name="Git.user" value="myanodebuddy" />
      <Setting name="Git.password" value="" />
      <Setting name="Bootstrap.Origin" value="github.com/myanodeorg/bootstrap" />
      <Setting name="Bootstrap.Branch" value="master" />
      <Setting name="System.url" value="https://github.com/myanodeorg/system" />
      <Setting name="Farm.url" value="https://github.com/myanodeorg/cluster#farm" />
    </ConfigurationSettings>
    <Certificates>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Those are parameters you need to change:
* Instances - the number of instances (virtual machines).
* Git.user - the name of buddy account on github and bitbucket (should be the same name for both).
* Git.password - the password of buddy account on github and bitbucket (should be the same password for both). __IMPORTANT__: The user and the password should be URL encoded (e.g. '$' is '%24'). Use http://meyerweb.com/eric/tools/dencoder/ to encode the password.
* Bootstrap.Origin - the name of ANODE bootstrap repository to be used for the farm. Change the organization name.
* Bootstrap.Branch - the branch of ANODE bootstrap repository be used for the farm. Leave it ```master```.
* System.url - URL for ANODE system repository. It should point to ANODE system repository in the github ANODE organization.
* Farm.url - URL for the branch dedicated for the farm in the cluster repository. It is made of cluster repository URL, pound symbol and the name of the branch.

### Azure package

Download Azure package for ANODE from https://anodejs.blob.core.windows.net/anode/production/anode.cspkg

## Start the farm

### Verify buddy account has access to the repositories

Before you proceed, verify yourself. Log in into github with buddy account. Verify buddy account can see system, bootstrap and cluster repositories.

### Create Azure hosted service and deployment for the new farm

In Azure portal create new hosted service. Fill the form:
* URL prefix - the farm name. Better be as the name specified in ```farm.json``` above (e.g. ```myanodefarm``` as referred in this manual)
* Name for the service - use the same name as URL prefix (not must, but easier to manage).
* Choose region where to place this ANODE farm.
* Deployment name is insignificant.
* In ```Deployment option``` (sometimes called ```Environment```) choose ```Production```. We don't use Azure ```Staging``` deployments.
* In ```Package location``` browse to the location of the downloaded ANODE package file.
* In ```Configuration file``` browse to the configuration file you've created for the farm.

NOTE: If you create farm with one instance (in Azure configuration you've specified ```Instances count="1"```), don't forget to check Azure's check box ```Deploy even if one or more roles contain a single instance``` or/and ignore the warning Azure portal may issue.

Submit the form and wait for all instances to be in the state ```Ready```. 

If this doesn't happen, please, recheck if everything is crafted according to this manual. 

For troubleshooting bootstrap phase, you may need to [enable remote access](https://github.com/anodejs/anodejs/blob/master/docs/TROUBLESHOOTING_SETUP.md#configuring-remote-access).

## Try it

The farm is now up and running. Access farm's dashboard via the farm's URL. In this example, going to myanodefarm.cloudapp.net will lead you to the dashboard.

Notice command line at the bottom of browser window. Type ```help``` to get the list of supported commands. ```man``` command shows manuals for ANODE dashboard.

Try ```log``` command to see the latest logs from the farm.

## Configure deployment notifications

Changes in repositories (system, cluster and application repositories) should trigger deployment to the farm. For each relevant repository configure notifications.

### Notifications from github

In the repository, go to ```Admin``` section. Use ```Service Hooks``` option. In ```WebHook URLs``` add URL for the farm. Assuming URL prefix chosen for the farm was ```myanodefarm```, the URL to be set here will be http://myanodefarm.cloudapp.net/deploy.sys/github?$bcast

[Continue to follow ANODE farm setup manual for more advanced steps](https://github.com/anodejs/anodejs#setup-anode-farm)