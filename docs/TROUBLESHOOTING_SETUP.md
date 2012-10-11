### Configuring Remote Access

If bootstrap doesn't work, there is no way to understand what is going on, but to use RDP (Remote Access) to ANODE instances. To configure RDP follow [these steps](https://github.com/anodejs/anodejs/blob/master/docs/RDP_SETUP.md).

### Missing notifications

Github and bitbucket send notification about changes in repositories. Sometimes these sites may fault. We find bitbucket somewhat less reliable and recommend github as the site of choice to host application repositories.

If following push to origin, the changes are not picked up by the farm, check the deployment status by using dashboard’s ```deploy status``` command. If according to status the deployment wasn't triggered by the site, it can be trigger it manually via ```deploy``` command. See ```help deploy``` for list of options of the command and specifically ```help deploy now!``` for details about deployment trigger.

For example, to trigger farm settings synchronization, one may issue command like:

```
deploy now! -g https://bitbucket.org/ -ga myanodeorg -r cluster -b farm
```

To trigger synchronization of the application repository ```rrr```, the command may look like:

```
deploy now! -ga myanodeorg -r rrr
```
(github site is the default; master branch is the default too).

Notice that it is possible to set  organization account name in dashboard’s environment. Use command like this to accomplish this:

```
set gitAccount myanodeorg
```

Next time to trigger deployment of ```rrr``` manually, you need to issue just this:

```
deploy now! -r rrr
```