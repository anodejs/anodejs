# Introduction

After basic setup ANODE farm is not secured. Anybody can access any application running on the farm, including system applications. Anybody can access farm's dashboard.

Also, all applications are exposed via HTTP.

The following steps will secure your ANODE farm. HTTPS will be enabled. Applications confgiured to be exposed only via HTTPS will do so. This includes all system applications, including the dashboard.

In addition, access to restricted (aka private) applications will be granted only to farm developers, who will authenticate with client certifcate.

# Setup steps

## Obtain domain name and server certificate

It is recommended to purchase domain name for your farm. With the domain name you will get your service certificate.

Domain name is optional. You can continue and take care of security without properly signed server certificate. Skip the next session if you want to postpone domain configuration.

### Configuring domain

Let's assume your domain is ```myservice.net```. You also have certificate. Let's assume you have myservice.pfx file.

#### Configure DNS

Create DNS CNAME record to resolve your domain as ```myanodefarm.cloudapp.net```. You need to create CNAMEs for both ```myservice.net.``` and ```*.myservice.net.```

#### Configure domain resolution

Go to farm's branch of your cluster repository. Modify ```farm.json``` in ```rebus``` directory by adding your domain name. The property should like like: ```"domains": ["myservice.net"]```.

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

If you don't want to acquire domain (can always do this later), you can secure your farm with self-signed certificates. Browsers will warn you on non-trusted certificates, but if you ingore warning you will be still able to access the farm via HTTPS.

Create self-signed certificates with ```openssl``` tool.

```bash
$ openssl genrsa -out private.pem 1024
openssl req -new -x509 -key private.pem -out public.pem -days 3660
```

## Setup server certificates

In farm branch of your cluster repository, create directory ```certs```. Create sub-directory ```inter``` in ```certs``` directory. Copy ```private.pem``` and ```public.pem``` you have obtained in the privous sections into ```inter``` subdirectory.

Push the changes into origin. Reboot instances of your farm.

Now, when you access your farm secured applications, you get automatically redirected to HTTPS. Notice, that all system applications, including dashboard, are secured.