### Missing notifications

Github and bitbucket send notification of changes in repositories. Sometimes these sites may fault. We find bitbucket somewhat less reliable and recommend github as the site of choice to host your repositories.

If following push you don't see your change picked up by the farm, check the deployment status by using ```deploy status``` command of the dashboard. If you see deployment wasn't triggered, you can trigger it manually via ```deploy``` command. See ```help deploy``` for list of options of the command and specifically ```help deploy now!``` for details about deployment trigger.

For example, to trigger synchronization of cluster repository you may issue command like:

```
deploy now! -g https://bitbucket.org/ -ga myanodeorg -r cluster -b repo
```

To trigger synchronization of you application repository ```rrr```, the command may look like:

```
deploy now! -ga myanodeorg -r rrr
```
(github site is the default; master branch is the default too).

Notice that you can set your organization account name in dashboard’s environment. Use command like this to accomplish this:

```
set gitAccount myanodeorg
```

Next time to trigger deployment of ```rrr``` manually you need to issue just this:

```
deploy now! -r rrr
```

### Connfiguring Remote Access

If bootstap doesn't work, there is no way to understand what is going on, but to use RDP (Remote Access) to ANODE instances. To configure RDP follow [these steps](https://github.com/anodejs/anodejs/blob/master/docs/RDP_SETUP.md)