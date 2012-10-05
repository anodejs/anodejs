# Introduction

These steps guide you through the workflow of setup for the most simple ANODE cluster. This cluster includes only one ANODE farm, which is functional, but not yet fully secured.

# Setup steps

## Prepare Azure Subscription

### Create Azure Subscription

If you don't have Azure subscription, you can create one. For evaluation puposes you can always use trial subscription, which is free.

### Create storage account for your ANODE cluster

Go to Azure portal and login with your LiveId, which own Azure subsciption. Navigate to storage accounts and create storage account for your ANODE cluster. You can choose any name for this storage account. Let's assume you've called it ```myanodestorage```. The information we will have to take from here is the account primary access key. It is a long string, you can copy from the portal.

## Setup git

You will host ANODE and your applications in github repositories.

You will also need private git space, where you will keep ANODE secrets. For this purpose you can use github private repository. However, on github, private repository cannot be created by free account. As an alternative you can use bitbucket to host private git repository (or repositories, if you will need private application repositories).

If you don't have github account, create one (at https://github.com). If you have only free github account and you cannot create private repositories on github, you will also need account on bitbucket (https://bitbucket.org).

### Create buddy account

Create new github account that will be used you your ANODE farm to pull sources. Let's assume you've called it ```myanodebuddy```.

If you cannot create private repositories on github, create buddy account on bitbucket as well. You should use the same name and the same password for this buddy account.

### Setup github organization

Now you can setup new organization. You will need the organization to manage permissions for developers collaborating in building applications on your ANODE deployment.

Let's assume you've called the organization ```myanodeorg```. You already have predefined team called ```Owners```. You are the only member.

Crate two more teams:
* ```Developers``` - set push&pull permissions. Later, you will include accounts of your developers and application repositores you will create.
* ```Readonly``` - set pull permissions only. Add buddy account here. Later you will add private repositories here, if any.

### Setup bitbucket team (if needed)

If you are using bitbucket for private repostories, you need to create complimentary bitbucket team. Let's assume you've called it by the same name ad your github organization - ```myanodeorg```.

As the owner of the team you can manage members. Take care you have there groups in the team (complimentary to what you have on github):
* ```Owners``` - rename existing admin team (not must, just for sake of common terminology between two sites).
* ```Developers``` - setup write permissions and optionally rights to create repositories (depends on policies you want to apply).
* ```Readonly``` - only read permissions. Add buddy account to this group.

## Get ANODE repositories

Fork two ANODE repositories to your github organization. The repositories are:
* https://github.com/anodejs/system
* https://github.com/anodejs/bootstrap

### Important note for early adopters (remove after system and bootstrap made public)

As long as system and bootstrap repositories are private, the forked repositories remmain private as well. You should add forked repositories to your github ```Readonly``` team. I am not sure if it will work, given you are not administrators of ```anodejs``` team, which owns parent private repositores. I hope it will not work, meaning, it will not allow granting read permisions to repositories own by ```anodejs```. Talk to me (yosefd@microsoft.com) and we will figure out how to let your buddy account to access these repositories (pobably you will have to make me or othee ```anodejs``` owner, coowner of your ANODE organization).