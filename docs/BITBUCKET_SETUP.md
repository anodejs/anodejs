## Introduction

Bitbucket (https://bitbucket.org) offers free private repositories. Since cluster repository keeps secrets, it should be private. If you are on free github plan, for sake of private cluster repository, you have to sign up for account on bitbucket.

## Create buddy account

Create buddy account on bitbucket. 

__IMPORTANT__: The name and the password for the buddy account should be the same on [github](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#create-buddy-account) and on bitbucket.

_IMPORTANT__: buddy account should not have any administrative rights. You should continue managing bitbucket with your own account. Log in back into __your__ bitbucket account to continue.

## Setup bitbucket team 

Create bitbucket team complimentary to github organization. Let's assume you've called it by the same name as github organization - ```myanodeorg```.

NOTE: The team on bitbucket requires separate e-mail account.

As the owner of the team you can manage members. Create three groups in the team (complimentary to what you have on github):
* ```Owners``` - rename existing ```Administrators``` team (not must, just for sake of common terminology between the two sites).
* ```Developers``` - setup write permissions and optionally allow creating repositories (depends on policies you want to apply).
* ```Readonly``` - only read permissions. Add buddy account to this group.

## Setup private cluster repository

Import cluster repository template https://github.com/anodejs/sample-cluster from github into [your bitbucket team](https://github.com/anodejs/anodejs/blob/master/docs/BITBUCKET_SETUP.md#setup-bitbucket-team) and give it name ```cluster```.

### Update ```farm.json``` in ```rebus``` directory

Bitbucket doesn't support editing on the site. Clone ```cluster``` repository to your computer.

Switch to ```farm``` branch:

```bash
git checkout farm
```

Modify ```farm.json``` file like it is described [here](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#update-farmjson-in-rebus-directory).

Push the changes back to origin:
```bash
git commit -am 'set farm parameters'
git push origin farm
```

### Setup permissions for the cluster repository

In repository configuration, go to ```Access Management```. Attach cluster repository to all groups you've created: ```Owners```, ```Developers``` and ```Readonly```.

Log in into bitbucket with the buddy account and verify buddy account has access to the cluster repository.

Don't stay logged into buddy account. Logout and and login back to your bitbucket account.

## Change Azure configuration

In Azure configuration file [created before](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#azure-configuration), change farm settings URL to point to the farm branch of the cluster repository on bitbucket:

```xml
<Setting name="Farm.url" value="https://bitbucket.org/myanodeorg/cluster#farm" />
```
## Configure deployment notifications

Like on github, bitbucket repositories should be configured to notify ANODE farm about changes.

### Notifications from bitbucket

In the repository configuration, use ```Services``` option. Select ```POST``` service and fill URL for the farm. Assuming URL prefix chosen for the farm was ```myanodefarm```, the URL to be set here will be http://myanodefarm.cloudapp.net/deploy.sys/bitbucket?$bcast

## Update farm to use cluster repository from bitbucket

If farm was created over public cluster repository, change it to use private cluster repository from bitbucket. In Azure portal, change deployment configuration. Upload the new configuration file. Reboot all instances to take effect.