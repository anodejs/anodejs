# Introduction

After basic setup ANODE farm is not secured. Anybody can access any application running on the farm, including system applications. Anybody can access farm's dashboard.

Also, all applications are exposed via HTTP.

The following steps will secure your ANODE farm. HTTPS will be enabled. Applications configured to be exposed only via HTTPS will do so. This includes all system applications, including the dashboard.

In addition, access to restricted (aka private) applications will be granted only to farm developers, who will authenticate with client certificate.

# Setup steps

## Obtain domain name and server certificate

It is recommended to purchase domain name for your farm. With the domain name you will get your service certificate.

Domain name is optional. You can continue and take care of security without properly signed server certificate. Skip the next session if you want to postpone domain configuration.

### Configuring domain

Let's assume your domain is ```myservice.net```. You also have certificate. Let's assume you have myservice.pfx file.

#### Configure DNS

Create DNS CNAME record to resolve your domain as ```myanodefarm.cloudapp.net```. You need to create CNAMEs for both ```myservice.net.``` and ```*.myservice.net.```

#### Configure domain resolution

Go to farm's branch of your cluster repository. Modify ```farm.json``` in ```rebus``` directory by adding your domain name. The property should like: ```"domains": ["myservice.net"]```.

It is possible to have multiple domains referring to your farm, e.g. ```"domains": ["myservice.net", "myservice.com"]```.

Your ```farm.json``` file may look like:

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

Push the change into origin. Reboot instances of your farm.

You can now access dashboard on your farm via ```myservice.net```.

#### Extract service PEM files

With ```openssl``` tool extract private and public keys of your certificate:

```bash
$ openssl pkcs12 -in myservice.pfx -out public.pem –nokeys
$ openssl pkcs12 -in myservice.pfx -out private.pem -nocerts -nodes
```

## Option: create self-signed server certificate

If you don't want to acquire domain (can always do this later), you can secure your farm with self-signed certificates. Browsers will warn you on non-trusted certificates, but if you ignore warning you will be still able to access the farm via HTTPS.

Create self-signed certificates with ```openssl``` tool.

```bash
$ openssl genrsa -out private.pem 1024
$ openssl req -new -x509 -key private.pem -out public.pem -days 3660
```

## Setup server certificates

In farm branch of your cluster repository, create directory ```certs```. Create sub-directory ```inter``` in ```certs``` directory. Copy ```private.pem``` and ```public.pem``` you have obtained in the previous sections into ```inter``` subdirectory.

Push the changes into origin. Reboot instances of your farm.

Now, when you access your farm secured applications, you get automatically redirected to HTTPS. Notice, that all system applications, including dashboard, are secured.

## Setup client certificates

To restrict access to private application, you have to install client certificate on your farm.

Create self-signed client certificate

```bash
$ openssl genrsa -out cc_private.pem 1024
$ openssl req -new -x509 -key cc_private.pem -out cc_public.pem -days 3660
$ openssl pkcs12 -export -inkey cc_private.pem -out cc.pfx -in cc_public.pem
```

The last command asks you to create password for accessing the certificate.

The certificate ```cc.pfx``` should be distributed between your developers. You can put it in the cluster repository, as you grant access to this repository only to members of your organization. Ask developers to install this certificate on their computers, and of course do the same (double clicking PFX file will do the job).

Copy ```cc_private.pem``` and ```cc_public.pem``` in ```inter``` subdirectory, next to server keys.

Push the changes into origin. Reboot instances of your farm.

Now, when you access dashboard on your farm, you are offered by the browser to authenticate using installed client certificate. Confirm it and you will get access to the dashboard. Whoever doesn't have the certificate installed, will not be able to access private applications on your farm, including the dashboard.

In the [following sections of ANODE setup manual](https://github.com/anodejs/anodejs#setup-anode-farm) you will see how to deploy application repositories.