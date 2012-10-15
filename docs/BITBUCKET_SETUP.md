## Introduction

Bitbucket (https://bitbucket.org) offers free private repositories. Since cluster repository keeps secrets, it should be private. If you are on free github plan, for sake of private cluster repository, you have to sign up for account on bitbucket.

## Create buddy account

Create buddy account on bitbucket. __IMPORTANT__: The name and the password for the buddy account should be the same on [github](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-buddy-account) and on bitbucket.

## Setup bitbucket team 

Create bitbucket team complimentary to github organization. Let's assume you've called it by the same name as github organization - ```myanodeorg```.

NOTE: The team on bitbucket requires separate e-mail account.

As the owner of the team you can manage members. Create three groups in the team (complimentary to what you have on github):
* ```Owners``` - rename existing admin team (not must, just for sake of common terminology between the two sites).
* ```Developers``` - setup write permissions and optionally allow creating repositories (depends on policies you want to apply).
* ```Readonly``` - only read permissions. Add buddy account to this group.

## Setup private cluster repository

Create cluster repository as described [here](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#setup-cluster-repository).

### Setup permissions for the cluster repository

Go to ```Admin``` section and to ```Access Management```. Attach cluster repository to all groups you've created: ```Owners```, ```Developers``` and ```Readonly```.

Log in into bitbucket with the buddy account and verify buddy account has access to the cluster repository.

## Change Azure configuration

In Azure configuration file [created before](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#azure-configuration), change farm settings URL to point to the farm branch of the cluster repository on bitbucket:

```xml
<Setting name="Farm.url" value="https://bitbucket.org/myanodeorg/cluster#farm" />
```
## Configure deployment notifications

[Like on github](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#configure-deployment-notifications), bitbucket repositories should be configured to notify ANODE farm about changes.

### Notifications from bitbucket

In the repository, go to ```Admin``` section. Use ```Services``` option. Select ```POST``` service and fill URL for the farm. Assuming URL prefix chosen for the farm was ```myanodefarm```, the URL to be set here will be http://myanodefarm.cloudapp.net/deploy.sys/bitbucket?$bcast

## Update farm to use cluster repository from bitbucket

If farm was created over public cluster repository, change it to use private cluster repository from bitbucket. In Azure portal, change deployment configuration. Upload the same Azure packager, with the new configuration file. The farm automatically switches to the new cluster repository.