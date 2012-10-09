# Introduction

The farm is now working and it is secured. You can start deploying applications on it. This manual guides you through the steps required to deploy your 1st application repository.

# Setup steps

## Setup application repository

### Create repository

Create repository in your github organization or in the bitbucket team. Let's assume you have created repository named ```rrr```.

### Setup permissions

Setup permissions by adding the repository into ```Developers``` and ```Readonly``` teams [created previously](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#setup-github-organization) (this is github terminology; on bitbucket they are called groups).

### Configure notifications

Like you've configured notifications for system and cluster repositories, you should configure notifications for all application repositories.

Follow instructions [here](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#configure-deployment-notifications).

### Configure deployment

Add deployment rule for the new repository into ```deploy.json``` file in ```rebus``` directory on the farm's branch of the cluster repository.

[Deployment system](https://github.com/anodejs/anodejs/blob/master/docs/REFERENCE.md#deployment) is very flexible. For demonstration here we are adding the most trivial rule that deploys repository ```rrr``` in the namespace ```rrr```. The rule for such deployment is ```"rrr": "https://github.com/myanodeorg/rrr``` (assuming the repository was created in github).

```deploy.sys``` may look now like:

```javascript
{
  "namespaces": {
    "rrr": "https://github.com/myanodeorg/rrr"
  }
}
```

Push changes in farm's branch of the cluster repository into origin. This automatically kicks deployment process.

On ANODE dashboard try command ```deploy status``` to see what happened. Command ```log -a deploy.sys``` shows logs from ANODE's deployment service.

## Create ANODE application

ANODE looks for files called ```index.js``` and considers them ANODE applications. For each ANODE application, ANODE creates entry in the name space. The name is derived from the relative path of the file in the repository.

For example, lets create directory ```aaa``` in our repository and place ```index.js``` in it that looks like:

```javascript
var express = require('express');

var srv = express.createServer();
srv.listen(process.env.PORT || 5000);

srv.get('/', function(req, res) {
  res.send('<font size="20">Welcome to <font color="red"><b>ANODE</b></font> application!</font>', 200);
});
```

Notice that the only interface with ANODE in application, is the referral to environment variable ```PORT```. This variable is set by ANODE. The application is written in a way that allows it to run independently of ANODE. In case ```PORT``` is not defined, the application listens on local port 5000.

ANODE system reserves name ```aaa.rrr``` for this application.

Commit and push the changes to the origin.

Use ```deploy status``` command on ANODE dashboard to see how fast the application got deployed.

Try to access the application. In our example the application can be reached via https://aaa.rrr.myservice.net (if the domain ```myservice.com``` [was assigned to the farm](https://github.com/anodejs/anodejs/blob/master/docs/SECURITY_SETUP.md#configure-domain-resolution)). If there is no associated domain, the application can be readed as https://myanodefarm.cloudapp.net/aaa.rrr

NOTE: Notice that you didn't have to install ```express``` node module in the application repository, though the application uses it. This works since ANODE comes with many popular node modules (```async```, ```express```, ```connect``` and ```request```, to mention a few). You can examine the full list in the [```node_modules``` directory of the system repository](https://github.com/anodejs/system/tree/master/node_modules).

NOTE: Nothing prevents from creating ANODE application in the application repository root. For example, if ```index.js``` file is placed in the application repository ```rrr``` root, it will define application that can be accessed as https://rrr.myservice.net or https://myanodefarm.cloudapp.net/rrr