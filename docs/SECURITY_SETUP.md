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

Create DNS CNAME record to resolve your domain as ```myanodefarm.cloudapp.net```.

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

You can now access dashboard on your farm via ```myservice.net```.

#### Extract service PEM files

With ```openssl``` tool extract private and public keys of your certificate:

```bash
$ openssl pkcs12 -in myservice.pfx -out public.pem –nokeys
$ openssl pkcs12 -in myservice.pfx -out private.pem -nocerts -nodes
```