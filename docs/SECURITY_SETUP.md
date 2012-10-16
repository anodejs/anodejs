## Introduction

After basic setup ANODE the farm is not secured. Anybody can access any application running on the farm, including system applications. Anybody can access farm's dashboard.

Also, all applications are exposed via HTTP.

The following steps will secure the farm. HTTPS will be enabled. Applications configured to be exposed only via HTTPS will do so. This includes all system applications, especially the dashboard.

In addition, access to restricted (aka private) applications will be granted only to farm developers, who authenticate with client certificate distributed by the administrator (you).

## Ensure cluster repository is private

If you've created public cluster repository, you to recreate it again as private. Follow [these steps](https://github.com/anodejs/anodejs/blob/master/docs/BITBUCKET_SETUP.md) to setup bitbucket and to host private cluster repository there.

## Obtain domain name and server certificate

It is recommended to purchase domain name for the farm. With the domain name you recieve service certificate.

Domain name is optional. You can continue and take care of security without properly signed server certificate. Skip the next session if you want to postpone domain configuration.

### Configuring domain

Let's assume your domain is ```myservice.net```. Let's also assume the certificate for services in this domain is myservice.pfx.

#### Configure DNS

Create DNS CNAME records to resolve the domain as ```myanodefarm.cloudapp.net```. You need to create CNAMEs for both ```myservice.net.``` and ```*.myservice.net.```

#### Configure domain resolution

Go to farm's branch of the cluster repository. Modify ```farm.json``` in ```rebus``` directory by adding domain name. The property should like: ```"domains": ["myservice.net"]```.

It is possible to have multiple domains referring to the farm, e.g. ```"domains": ["myservice.net", "myservice.com"]```.

```farm.json``` file may look like:

```javascript
{
  "name": "myanodefarm",
  "domains": ["myservice.net"],  
  "workersDisabled": false,
  "azureStorage": {
    "account" : {
      "name" : "myanodestorage",
      "key" : "myanodestorageprimarykey"
    }
  }
}
```

Push the change into origin. Reboot instances of the farm.

You can now access farm's dashboard via ```myservice.net```.

#### Extract service PEM files

With ```openssl``` tool extract private and public keys from the certificate:

```bash
$ openssl pkcs12 -in myservice.pfx -out public.pem –nokeys
$ openssl pkcs12 -in myservice.pfx -out private.pem -nocerts -nodes
```

## Option: create self-signed server certificate

If you don't want to acquire domain (can always do this later), you can secure the farm with self-signed certificates. Browsers warn on non-trusted certificates, but by ignoring the warning, it is possible to access the farm via HTTPS.

Create self-signed certificates with ```openssl``` tool.

```bash
$ openssl genrsa -out private.pem 1024
$ openssl req -new -x509 -key private.pem -out public.pem -days 3660
```

## Setup server certificates

In farm's branch of the cluster repository, create directory ```certs```. Create sub-directory ```inter``` in ```certs``` directory. Copy ```private.pem``` and ```public.pem``` into ```inter``` subdirectory.

Push the changes into origin. Reboot instances of the farm.

Secured applications can now be accessed only via HTTPS. Notice, that all system applications, including dashboard, are secured.

## Setup client certificates

To restrict access to private application, configure client certificate for the farm.

Create self-signed client certificate

```bash
$ openssl genrsa -out cc_private.pem 1024
$ openssl req -new -x509 -key cc_private.pem -out cc_public.pem -days 3660
$ openssl pkcs12 -export -inkey cc_private.pem -out cc.pfx -in cc_public.pem
```

The last command asks to create password for accessing the certificate.

The certificate ```cc.pfx``` should be distributed between organization developers. Please it in the cluster repository, as you grant access to this repository only to members of the organization. Ask developers to install client certificate on their computers, and, of course, do the same (double clicking PFX file accomplishes the job).

Copy ```cc_private.pem``` and ```cc_public.pem``` in ```inter``` subdirectory, next to server keys.

Push the changes into origin. Reboot instances of the farm.

Now, when you access dashboard on the farm, you are offered by the browser to authenticate, using installed client certificate. Confirm and get access to the dashboard. Whoever doesn't have the certificate installed, will not be able to access private applications (e.g. the dashboard) on the farm.

In the [following sections of ANODE setup manual](https://github.com/anodejs/anodejs#setup-anode-farm) you will see how to deploy application repositories.