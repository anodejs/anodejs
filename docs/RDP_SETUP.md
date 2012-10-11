## Introduction

For using ANODE there is no need to enable Remote Access to Azure instances. Therefore, production package doesn't include RDP support. However, troubleshooting needs may require RDP.

## Instal RDP enabled package

ANODE comes with RDP enabled package and sample certificate, which can be used for RDP. Notice, that this certificate is known by everybody, hence, it is not recommended to use it beyond initial setup. Once RDP enabled role is installed, switch to your own certificate.

### Install sample RDP certificate

Download sample RDP certificate from https://github.com/anodejs/anodejs/blob/master/resources/rdpenabled/rdpforall.pfx

In Azure portal, add this certificate to certificates of the hosted service. The password for the sample certificate is ```Everyb0dyKnow$```

Since the certificate is opened to everybody, switch to your own certificate as soon as possible, [as described later](https://github.com/anodejs/anodejs/blob/master/docs/RDP_SETUP.md#change-rdp-certificate-and-access-password).

### Prepare RDP enabled deployment configuration

Azure deployment configuration file used previously doesn't have RDP parameters. You should create new configuration file. Modify the template from https://github.com/anodejs/anodejs/blob/master/resources/rdpenabled/ServiceConfiguration.anode-sample-rdp.cscfg

Copy all settings from the original configuration file, you've created [here](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#azure-configuration).

These are the parameters configured then, and should be copied now:

```xml
<Setting name="Git.user" value="myanodebuddy" />
<Setting name="Git.password" value="" />
<Setting name="Bootstrap.Origin" value="github.com/myanodeorg/bootstrap" />
<Setting name="Bootstrap.Branch" value="master" />
<Setting name="System.url" value="https://github.com/myanodeorg/system" />
<Setting name="Farm.url" value="https://bitbucket.org/myanodeorg/cluster#farm" />
```

Copy all these parameters into RDP enabled configuration file.

### Upgrade deployment to RDP enabled version

In Azure portal, upgrade the deployment. The RDP enabled package can be found at https://anodejs.blob.core.windows.net/anode/rdpenabled/anode.cspkg. The configuraiton file was prepared in the [previous step](https://github.com/anodejs/anodejs/blob/master/docs/RDP_SETUP.md#prepare-rdp-enabled-deployment-configuration).

### Try RDP to instances

Upon upgrade completion, you should be able to RDP into Azure instances. The user name for RDP connection is ```admin```, the password is ```Everyb0dyKnow$```

## Change RDP certificate and access password

### Create new RDP certificate

Follow steps [similar to those](https://github.com/anodejs/anodejs/blob/master/docs/SECURITY_SETUP.md#setup-client-certificates) to create new self-signed certificate. This certificate can be used for RDP.

### Install the certificate

Add the new certificate to the hosted service.

### Change RDP configuration

In Azure portal select ```anodejsrole```. Remote Access configuration should be enabled. Configure Remote Access. Choose the thumbprint of the newly created certificate. Set desired user name and password for RDP connection.

Once the deployment is updated, remove ```rdpforall``` certificate from the service. There is no need for it, anymore.