## Startup

https://pm2.keymetrics.io/docs/usage/startup/

### Generating a Startup Script

To automatically generate and configuration a startup script just type the command (without sudo) `pm2 startup`:

```
$ pm2 startup
[PM2] You have to run this command as root. Execute the following command:
      sudo su -c "env PATH=$PATH:/home/unitech/.nvm/versions/node/v14.3/bin pm2 startup <distribution> -u <user> --hp <home-path>
```

Then copy/paste the displayed command onto the terminal:

```
sudo su -c "env PATH=$PATH:/home/unitech/.nvm/versions/node/v14.3/bin pm2 startup <distribution> -u <user> --hp <home-path>
```

Now PM2 will automatically restart at boot.

**Note**: You can customize the service name via the `--service-name <name>` option ([#3213](https://github.com/Unitech/pm2/pull/3213))

### Saving the app list to be restored at reboot

Once you have started all desired apps, save the app list so it will respawn after reboot:

```
pm2 save
```

### Manually resurrect processes

To manually bring back previously saved processes (via pm2 save):

```
pm2 resurrect
```

### Disabling startup system

To disable and remove the current startup configuration:

```
pm2 unstartup
```

The previous line code let PM2 detect your platform. Alternatively you can use another specified init system yourself using:

#### Updating startup script after Node.js version upgrade

When you upgrade the local Node.js version, be sure to update the PM2 startup script, so it runs the latest Node.js binary you have installed.

First disable and remove the current startup configuration (copy/paste the output of that command):

```
$ pm2 unstartup
```

Then restore a fresh startup script:

```
$ pm2 startup
```