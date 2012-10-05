# Introduction

These steps guide you through the workflow of setup for the most simple ANODE cluster. This cluster includes only one ANODE farm, which is functional, but not yet secured.

# Setup steps

## Prepare Azure Subscription

### Create Azure Subscription

If you don't have Azure subscription, you can create one. For evaluation puposes you can always use trial subscription, which is free.

### Create storage account for your ANODE cluster

Go to Azure portal and login with your LiveId, which own Azure subsciption. Navigate to storage accounts and create storage account for your ANODE cluster. You can choose any name for this storage account. Let's assume we called it ```anodesample```. The information we will have to take from here is the account primary access key. It is a long string, you can copy from the portal.

## Setup github organization

If you don't have github account, create one. 

Now you can setup new organization. This organization will be used to develop applications in 