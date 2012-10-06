# Introduction

The farm is now working and it is secured. You can start deploying applications on it. This manual guides you through the steps required to deploy your 1st application repository.

# Setup steps

## Setup application repository

### Create repository

Create repository in your github organization or in bitbucket team.
Let's assume you have create repository named ```rrr```.

### Setup permissions

Setup permissions by adding the repository into ```Developers``` and ```Readonly``` teams you've created previously (this is github terminology; on bitbucket they are called groups).

### Configure notifications

Like you've configured notifications for system and cluster repositories, you should configure notifications for all application repositories.

Follow instructions [here](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#configure-deployment-notifications).

### Configure deployment

Add deployment rule for your new repository into ```deploy.json``` file in ```rebus``` directory on your farm branch of the cluster repository.

Deployment system is very flexible. For demonstration here we are adding the most trivial rule that deploys your repository ```rrr``` in the namespace ```rrr```. The rule for such deployment is ```"rrr": "https://github.com/myanodeorg/rrr``` (assuming the repository was created in github).

Your ```deploy.sys``` may look now like:

```javascript
{
  "namespaces": {
    "rrr": "https://github.com/myanodeorg/rrr"
  }
}
```

Push changes in farm branch of the cluster repository into origin. This will automatically kick deployment process.

On ANODE dashboard you can try command ```deploy status``` to see what happened. Command ```log -a deploy.sys``` will show logs from ANODE's deployment service.

## Create ANODE application

ANODE looks for files called ```index.js``` and considers them ANODE applications. For each ANODE application, ANODE creates entry in the name space. The name is derived from the path of the file relatively to the repository root.

For example, lets create directory ```aaa``` in our repository and place ```index.js``` in it that looks like:

```javascript
var express = require('express');

var srv = express.createServer();
srv.listen(process.env.PORT || 5000);

srv.get('/', function(req, res) {
  res.send('<font size="20">Welcome to <font color="red"><b>ANODE</b></font> application!</font>', 200);
});
```

ANODE system reserves name ```aaa.rrr``` for your application.

Commit and push the changes to the origin.

Use ```deploy status``` command of ANODE dashboard to see how fast your application got deployed.

Try to access your application. In our example the application can be reached via https://aaa.rrr.myservice.net (if you have the domain ```myservice.com``` configured for the farm). If you don't have associated domain, you can reach the application as https://myanodefarm.cloudapp.net/aaa.rrr

NOTE: Notice that you didn't have to install ```express``` node module in your repository, though your application uses it successfully. This works since ANODE comes with many popular node modules it uses by itself (```async```, ```express```, ```connect``` and ```request```, to mention a few). You can examine the full list in ```node_modules``` directory of the system repository.