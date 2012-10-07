# Introduction

These steps guide you through the workflow of setup for the simplest ANODE cluster. This cluster includes only one ANODE farm, which is functional, but not yet fully secured.

# Setup steps

## Prepare Azure Subscription

### Create Azure Subscription

If you don't have Azure subscription, you can create one. For evaluation purposes you can always use trial subscription, which is free.

### Create storage account for your ANODE cluster

Go to Azure portal and login with your LiveId, which owns Azure subscription. Navigate to storage accounts and create storage account for your ANODE cluster. You can choose any name for this storage account. Let's assume you've called it ```myanodestorage```. The information we will have to take from here is the account primary access key. It is a long string you can copy from the portal.

## Setup git

You will host ANODE and your applications in github repositories.

You will also need private git space, where you will keep ANODE secrets. For this purpose you can use github private repository. However, on github, private repository cannot be created in a free account. As an alternative you can use bitbucket to host private git repository (or repositories, if you will need private application repositories).

If you don't have github account, create one (on https://github.com). If you have only free github account and you cannot create private repositories on github, you will also need account on bitbucket (https://bitbucket.org).

### Create buddy account

Create new github account to be used by ANODE farm to pull sources. Let's assume you've called it ```myanodebuddy```.

If you cannot create private repositories on github, create buddy account on bitbucket as well. You should use the same name and the same password for this buddy account.

### Setup github organization

Github organization is useful for managing permissions for developers collaborating in building applications on your ANODE deployment.

Let's assume you've called the organization ```myanodeorg```. You already have predefined team called ```Owners```. You are the only member.

Crate two more teams:
* ```Developers``` - set push&pull permissions. Later, it will include developers' accounts and application repositories.
* ```Readonly``` - set pull permissions only. Add buddy account here. Later can be used for private repositories, if any.

### Setup bitbucket team (if needed)

If you are using bitbucket for private repositories, you need to create complimentary bitbucket team. Let's assume you've called it by the same name aa github organization - ```myanodeorg```.

As the owner of the team you can manage members. Create three groups in the team (complimentary to what you have on github):
* ```Owners``` - rename existing admin team (not must, just for sake of common terminology between the two sites).
* ```Developers``` - setup write permissions and optionally allow creating repositories (depends on policies you want to apply).
* ```Readonly``` - only read permissions. Add buddy account to this group.

## Fork ANODE repositories

Fork two ANODE repositories to your github organization. The repositories are:
* https://github.com/anodejs/system
* https://github.com/anodejs/bootstrap

### Important note for early adopters (remove this section after system and bootstrap made public)

As long as system and bootstrap repositories are private, the forked repositories remain private as well. You should add forked repositories to your github ```Readonly``` team, which grants read permissions to buddy account. I am not sure if it will work, given you are not administrators of ```anodejs``` team, which owns parent private repositories. I hope it will not work, meaning, it will not allow granting read permissions to repositories owned by ```anodejs```. Talk to me (yosefd@microsoft.com) and we will figure out how to let your buddy account to access these repositories (probably can be solved by adding one ```anodejs``` owner as co-owner of your ANODE organization).

## Create ANODE cluster configuration

ANODE cluster configuration is kept in special private cluster repository. This repository doesn't include any code, just configuration parameters for your cluster farms.

Cluster repository has to be private as it includes various secrets: Azure storage account key and certificates keys.

Each farm in your cluster should have a branch on cluster repository. ```master``` branch of the cluster repository is reserved for development environment (consider you development machine as another farm in your ANODE cluster).

You can refer to a template of cluster repository at https://github.com/anodejs/sample-cluster

### Setup private cluster repository

Create private repository in your github organization or bitbucket team. You can call it ```cluster```.

Create branch for your farm. Let's assume you have called it ```farm```. 

Create ```rebus``` directory and place 3 json files in it:
* deploy.json
* farm.json
* log.json

Your ```farm``` branch of ```cluster``` repository will look just like https://github.com/anodejs/sample-cluster/tree/farm

#### Update ```farm.json```

In this file you should configure the name to be used with your farm and azure storage account. The sample file looks like:

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

Fill the real name of your farm, Azure storage account name and they key.

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

You will find in more advanced manuals how to manage these settings.

#### Setup permissions for cluster repository

If on bitbucket, go to ```Admin``` section and to ```Access Management```. Attached all groups you've created: ```Owners```, ```Developers```` and ```Readonly```.

If private cluster repository was crated on github, add it to ```Readonly``` team.

## Prepare Azure deployment files

### Azure configuration

To create Azure deployment, you need Azure configuration file. Modify the template from https://github.com/anodejs/anodejs/blob/master/resources/production/ServiceConfiguration.anode-sample.cscfg with appropriate settings. The template looks like:

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
      <Setting name="Farm.url" value="https://bitbucket.org/myanodeorg/cluster#farm" />
    </ConfigurationSettings>
    <Certificates>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Those are parameters you may need to change:
* Instances - the number of instances (virtual machines).
* Git.user - the name of buddy account you've created on github and bitbucket (should be the same name on both).
* Git.password - the password of buddy account on github and bitbucket (should be the same password on both). __IMPORTANT__: The user and the password should be URL encoded (e.g. '$' is '%24').
* Bootstrap.Origin - the name of ANODE bootstrap repository to be used for the farm. It should be ANODE bootstrap repository from your anode organization.
* Bootstrap.Branch - the branch of ANODE bootstrap repository be used for the farm. Leave it ```master```.
* System.url - URL for your ANODE system repository. It should point to ANODE system repository in your github ANODE organization.
* Farm.url - URL for the branch dedicated for your farm in the cluster repository you've created. It is made of URL to the cluster repository, pound symbol and the name of the branch.

### Azure package

Download Azure package for ANODE from https://github.com/anodejs/anodejs/blob/master/resources/production/anode.cspkg

You may prefer to clone this repository (https://github.com/anodejs/anodejs) to your development machine to have all the resources locally.

## Start the farm

### Create Azure hosted service and deployment for your new farm

In Azure portal create new hosted service. Fill the form:
* URL prefix - your farm name. Better be as the name specified in ```farm.json``` above (e.g. ```myanodefarm``` as referred in this manual)
* Name for the service - use the same name as URL prefix (not must, but easier to manage).
* Choose region where you want to place this ANODE farm.
* In ```Deployment options``` specify ```Deploy to production environment```
* In ```Package location``` browse to the location of ANODE package file, you've downloaded from this repository.
* In ```Configuration file``` browse to the configuration file you've created for the farm.

Press ```OK``` and wait for all instances to be in state ```Ready```.

## Configure deployment notifications

Changes in repositories (ANODE system, cluster and application repositories) should trigger ANODE farm deployment. For each relevant repository configure notifications.

### Notifications from github

In the repository, go to ```Admin``` section. Use ```Service Hooks``` option. In ```WebHook URLs``` add URL for your farm. Assuming URL prefix chosen for your farm was ```myanodefarm```, the URL to be set here will be http://myanodefarm.cloudapp.net/deploy.sys/github?$bcast

### Notifications from bitbucket

In the repository, go to ```Admin``` section. Use ```Services``` option. Select ```POST``` service and fill URL for your farm. Assuming URL prefix chosen for your farm was ```myanodefarm```, the URL to be set here will be http://myanodefarm.cloudapp.net/deploy.sys/bitbucket?$bcast

## Try it

Your farm is now up and running. Access your farm dashboard via the farm's URL. In this example, going to myanodefarm.cloudapp.net will lead you to the dashboard.

Notice command line at the bottom of your browser window. Type ```help``` to get the list of supported commands. ```man``` command will show you some manuals for ANODE dashboard.

Try ```log``` command to see the latest logs from the farm.

[Continue to follow ANODE farm setup manual for more advanced steps](https://github.com/anodejs/anodejs#setup-anode-farm)