# Introduction

Deployment rules govern placement of branches of application repositories on the farm. ANODE can deploy multiple branches of multiple application repositories side-by-side.

## Deployment configuration

Deployment configuration is defined by ```deploy.json``` in ```rebus``` directory of the farm's branch of the cluster repository.

Empty deployment configuration doesn't deploy any applications on the farm:

```javascript
{
  "namespaces": {
  }
}
```

Deployment rules can be added in ```namespaces``` clause.

## Deployment rule

```namespace``` object properties are the deployment rules.

The key part defines the target directory. The value part is the source URL. For example:

```
"rrr": "https://github.com/myanodeorg/rrr"
```

The rule above places master branch of ```rrr``` repository from ```myanodeorg``` account on github in ```rrr``` directory.

### Branch

By default the branch deployed is the master branch. It is possible to specify other branch appending # and the branch name:

```
"rrr/ba": "https://github.com/myanodeorg/rrr#ba"
```

The rule above places ```ba``` branch of ```rrr``` repository from ```myanodeorg``` account on github in ```rrr/ba``` directory.

### Branch regular expression

The branch part of the url can include regular expression to cover multiple branches within the single rule. The regular expression should include at least one capturing group (in round brackets). The captured content, matching to the group can be used in target part as ```$n```, where n is the number of the capturing group.

For example:

```
"rrr/$1": "https://github.com/myanodeorg/rrr#(.+)-d"
```

The rule above deploys all ```rrr``` repository branches ending with ```-d``` suffix. The branch is deployed to a subdirectory of ```rrr``` with name equal to the prefix of the branch name.

Such rule creates policy that distinguishes between deployable and non-deployable branches. The name convention allows deploying new branches without ANODE administrator intervention. When new branch with ```-d``` suffix is created, it is automatically deployed on the farm in appropriate namespace.

## Deployment configuration update

Deployment configuration can be changed on-the-fly. Push into farm's branch on origin triggers [notification](https://github.com/anodejs/anodejs/blob/master/docs/SIMPLE_SETUP.md#configure-deployment-notifications). The deployment system responds by switching to the updated deployment configuration.

## Example

Putting all together:

```javascript
{
  "namespaces": {
    "rrr/__master": "https://github.com/myanodeorg/rrr",
    "rrr/$1": "https://github.com/myanodeorg/rrr#(.+)-d",
    "xxx": "https://github.com/myanodeorg/rrr#xxx"
  }
}
```

This policy deploys ```rrr``` master branch in ```rrr/__master``` directory. The rule uses opaque directory name ```__master``` (is prefixed by ```__```), as described in [namespaces reference](https://github.com/anodejs/anodejs/blob/master/docs/REFERENCE.md#application-namespace) and leads to suffix ```.rrr``` for all applications in the master branch.

Branch ```xxx``` is deployed in ```xxx``` directory. The applications from the branch can be accessed by using ```.xxx``` suffix.

All branches with suffix ```-d``` are deployed and application names suffix is derived from the branch name. For example, application in the branch named ```yyy-d``` can be accessed by using ```.yyy.rrr``` suffix.