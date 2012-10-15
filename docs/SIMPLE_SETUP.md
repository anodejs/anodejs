## Introduction

These steps guide you through the workflow of setup for the simplest ANODE cluster. This cluster includes only one ANODE farm, which is functional, but not yet fully secured.

## Prepare Azure Subscription

### Create Azure Subscription

If you don't have Azure subscription, you can create one. For evaluation purposes you can always use trial subscription, which is free.

Start from here for everything related to Azure: http://windowsazure.com

### Create storage account for your ANODE cluster

Go to Azure portal and login with your LiveId, which owns Azure subscription. Navigate to storage accounts and create storage account for your ANODE cluster. You can choose any name for this storage account. Let's assume you've called it ```myanodestorage``` (not literally, just for illustration purposes; choose appropriate unique name). The information we will have to take from here is the account primary access key. It is a long string you can copy from the portal.

## Setup github

You will host ANODE itself and your applications in github repositories.

You also need private git space, where you can keep ANODE secrets. For this purpose you can use github private repository. However, on github, private repository cannot be created using the free plan (see more about github plans [here](https://github.com/settings/billing)). As an alternative you can use [bitbucket](https://bitbucket.org/) to host private git repository (or repositories, if you will need private application repositories) for free. Later steps will show how to create private repository on bitbucket.

### Create buddy account

Buddy account will be used by the farms to pull files from github. The account should not have rights stronger than reading (pulling) repositories. Letting farms operate over stronger account would harm security.

Create new github account. Let's assume you've called it ```myanodebuddy``` (illustrative name, the real name should be unique account name).

__IMPORTANT__: buddy account should not have any administrative rights. You should continue managing github with your own account. Log in back into __your__ github account to continue.

### Setup github organization

[Github organization](https://raw.github.com/anodejs/anodejs/master/docs/images/githuborganizations.jpg) is useful for managing permissions for developers collaborating in building applications on your ANODE deployment.

Let's assume you've called the organization ```myanodeorg``` (illustrative name, the real name should be unique). You already have predefined team called ```Owners```. You are the only member.

[Create two more teams](https://raw.github.com/anodejs/anodejs/master/docs/images/githubteams.jpg):
* ```Developers``` - set push&pull permissions. Later, it will include developers' accounts and application repositories.
* ```Readonly``` - set pull permissions only. Add buddy account here. Later it can be used for private repositories, if any.

## Fork ANODE repositories

[Fork](https://raw.github.com/anodejs/anodejs/master/docs/images/githubfork.jpg) two ANODE repositories to your github organization (__NOTE__: Pay attention you are forking to the [organization account](https://raw.github.com/anodejs/anodejs/master/docs/images/githubselectorg.jpg) and not to your own account). The repositories are:
* https://github.com/anodejs/system
* https://github.com/anodejs/bootstrap

Now, you should have two forked repositories like those:
* https://github.com/myanodeorg/system
* https://github.com/myanodeorg/bootstrap

### Set permissions

As long as system and bootstrap repositories are private, the forked repositories remain private as well. Add forked repositories to Readonly team, which grants read permissions to the buddy account.

## Create ANODE cluster configuration

ANODE cluster configuration is kept in cluster repository. This repository doesn't include any code, just configuration parameters for cluster's farms.

Eventually cluster repository has to be private as it includes various secrets: Azure storage account key and certificates keys. We will return to the issues of security after the basic ANODE farm would be configured.

Each farm in the cluster should have a branch in the cluster repository. ```master``` branch is reserved for the development environment (consider developer's computer as another farm in ANODE cluster).

### Setup cluster repository

For cluster repository template https://github.com/anodejs/sample-cluster into [your github organization](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#setup-github-organization). Rename it ```cluster```.

If you pay github for more advanced plan convert the cluster repository to private. If you have only free github plan, you will have to recreate cluster repository, later, on bitbucket.

Configure access rights by adding cluster repository to ```Readonly``` team.

Switch to ```farm``` branch, which will be responsible for setting of your first farm.

#### Update ```farm.json``` in ```rebus``` directory

Make sure you are on ```farm``` branch of ```cluster``` repository.

The file looks like:

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

Change ```myanodefarm``` to the real name of your farm. This name will be later used as domain prefix for the farm URL.

Change the name of Azure storage account to the name of the [storage account you've created](https://github.
com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-storage-account-for-your-anode-cluster).

Change the name of Azure storage account key to the primary key of the [storage account you've created](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-storage-account-for-your-anode-cluster).

## Prepare Azure deployment files

### Azure configuration

Download farm configuration file template from https://github.com/anodejs/anodejs/blob/master/resources/production/ServiceConfiguration.anode-sample.cscfg.

Modify the template with appropriate settings. The template looks like:

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
* Git.user - the name of the [buddy account](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-buddy-account).
* Git.password - the password of the [buddy account](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-buddy-account). __IMPORTANT__: The user and the password should be URL encoded (e.g. '$' is '%24'). Use http://meyerweb.com/eric/tools/dencoder/ to encode.
* Bootstrap.Origin - the name of ANODE bootstrap repository to be used for the farm. Change the organization name.
* Bootstrap.Branch - the branch of ANODE bootstrap repository be used for the farm. Leave it ```master```.
* System.url - URL for ANODE system repository. It should point to ANODE system repository in the github ANODE organization.
* Farm.url - URL for the branch dedicated for the farm in the cluster repository. It is made of cluster repository URL, pound symbol and the name of the branch.

### Azure package

Download Azure package for ANODE from https://anodejs.blob.core.windows.net/anode/production/anode.cspkg

NOTE: Internet Explorer renames changes the file extension to .zip upon download. Rename the file back to .cspkg

## Start the farm

### Verify buddy account has access to the repositories

Before you proceed, verify yourself. Log in into github with buddy account. Verify buddy account can access system, bootstrap and cluster repositories from [your organization](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#setup-github-organization), which are like:

* https://github.com/myanodeorg/system
* https://github.com/myanodeorg/bootstrap
* https://github.com/myanodeorg/cluster

### Create Azure cloud service and deployment for the new farm

In Azure portal create new cloud service.

In ```URL``` fill the farm name ([the same as specified in ```farm.json```](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#update-farmjson)) as domain prefix.

Choose appropriate region. Better be the same as region chosen for [storage account](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-storage-account-for-your-anode-cluster).

### Create production deployment

Create new production deployment. NOTE: ANODE doesn't use staging deployments.

Deployment name is insignificant.

Upload [package file](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#azure-package) and [configuration file](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#azure-configuration) you've created.

NOTE: If you create farm with one instance (in Azure configuration you've specified ```Instances count="1"```), don't forget to check Azure's check box ```Deploy even if one or more roles contain a single instance``` or/and ignore the warning Azure portal may issue.

Wait for all instances to be in the state ```Ready```. This would take up to 15 minutes.

If this doesn't happen, verify yourself:

* Recheck if everything is crafted according to this manual. 
* In deployment configuration tab check deployment settings. Are all parameters (```Git.user```, ```Git.password```, ```Bootstrap.Origin```, ```Bootstrap.Branch```, ```System.url```, ```Farm.url```) set correctly?
* If you want to change the configuration, delete the deployment and create the new one.

## Try it

The farm is now up and running. Access farm's dashboard via the farm's URL. In this example, going to myanodefarm.cloudapp.net will lead you to the dashboard.

Notice command line at the bottom of browser window. Type ```help``` to get the list of supported commands. ```man``` command shows manuals for ANODE dashboard.

Try ```log``` command to see the latest logs from the farm.

## Configure deployment notifications

Changes in repositories (system, cluster and repositories where you will host ANODE applications) should trigger deployment to the farm. For each relevant repository (at this stage those are system and cluster) configure notifications.

### Notifications from github

In the repository, go to ```Admin``` section. Use ```Service Hooks``` option. In ```WebHook URLs``` add URL for the farm. Assuming URL prefix chosen for the farm was ```myanodefarm```, the URL to be set here will be http://myanodefarm.cloudapp.net/deploy.sys/github?$bcast

[Continue to follow ANODE farm setup manual for more advanced steps](https://github.com/anodejs/anodejs#setup-anode-farm)
