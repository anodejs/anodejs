## ANODE application

ANODE considers each file named ```index.js``` to be ANODE application. The application is assigned with name that can be used to access the application.

## Application name

The application name is derived from layout on file system. Each directory becomes a segment in the application dotted name in the reverse order.

For example, let’s assume ```index.js``` file is placed in the directory ```aaa``` of the repository ```rrr```. Also, let’s assume that the branch ```zzz-d``` of the repository is deployed in directory ```rrr/zzz``` by the deployment configuration (see [deployment example](https://github.com/anodejs/anodejs/blob/master/docs/DEPLOYMENT_REF.md#example)). The name assigned to the application will be ```aaa.zzz.rrr```

## Opaque directories

The directory with 2 underscore symbols in front, is considered opaque directory and doesn't contribute to application name.

Consider example similar to the previous one. The file ```index.js``` is in directory ```aaa``` of the repository ```rrr```. The master branch is deployed to ```rrr/__master``` (see [deployment example](https://github.com/anodejs/anodejs/blob/master/docs/DEPLOYMENT_REF.md#example)). The name assigned to the application will be ```aaa.rrr```

## URL naming schema

ANODE parses requests arrived to the farm and routes them to applications. There are two ways to specify application name in URL. Application kept agnostic on the way it was called.

### Application domain

ANODE creates virtual application domain for each application. The domain is built by concatenation of application name and the farm domain.

For example, if the farm domain is ```myservice.net```, than application with name ```aaa.rrr``` can be accessed via domain name ```aaa.rrr.myservice.net```. Request with URL https://aaa.rrr.myservice.net/something/anything?querywhatever=somethingelse will be routed to application ```aaa.rrr```

### Application path

Application can be accessed with application name specified in URL path, rather than domain.

For example, to access application ```aaa.rrr``` on the farm ```myanodefarm.cloudapp.net```, you can use URL https://myanodefarm.cloudapp.net/aaa.rrr/something/anything?querywhatever=somethingelse

In application, the path of URL it receives doesn't include application name. This allows applications to work identically, regardless the way they are called.