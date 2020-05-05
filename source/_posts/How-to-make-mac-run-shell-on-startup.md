layout: post
title: How to make mac run shell on startup
tags:
  - mac
categories:
  - Linux
  - Mac
comments: true
date: 2020-05-05 22:21:00
---
* Write a simple shell, like：

```
#!/bin/bash

HOME=/root
LOGNAME=root
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
LANG=en_US.UTF-8
SHELL=/bin/bash
PWD=/root

cd /Users/bejond/tools/mongodb-osx-x86_64-4.0.9/bin
./mongod --dbpath /Users/bejond/data/db
echo "Mongod Server Started"
```
Save it as a `.sh` file, like `/Users/bejond/startmongodb.sh`. Then make the shell runnable. 
`sudo chmod a+x /Users/bejond/startmongodb.sh`
* Create a `.plisth` file in `/Library/LaunchAgents/`, like `com.startup.mongodb.plist`, in order to run the script or command.

Edit the file, like:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>EnvironmentVariables</key>
    <dict>
      <key>PATH</key>
      <string>/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:</string>
    </dict>
    <key>Label</key>
    <string>com.startup.mongodb</string>
    <key>ProgramArguments</key>
    <array>
    <string>/Users/bejond/startmongodb.sh</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <false/>
    <key>LaunchOnlyOnce</key>        
    <true/>
    <key>StandardErrorPath</key>
    <string>/tmp/startup.mongodb.stderr</string>
    <key>UserName</key>
    <string>bejond</string>
    <key>GroupName</key>
    <string>admin</string>
    <key>InitGroups</key>
    <true/>
  </dict>
</plist>

```
Seeing `ProgramArguments`, this means we can pass a command and arguments. You can also change `ProgramArguments` to `Program`, but without `<array>` element.

* Add the `com.startup.mongodb.plist` to `launchd`:
`$ sudo launchctl load /Library/LaunchAgents/com.startup.mongodb.plist`
* Remove the `com.startup.mongodb.plist` from `launchd`:
`$ sudo launchctl unload /Library/LaunchAgents/com.startup.mongdb.plist`

* Then `/Users/bejond/startmongodb.sh` would be executed just now. And it would also run on next startup.

[1] [Running script upon login mac - answered by trisweb](https://stackoverflow.com/a/13372744/3908814)

[2] [Adding Startup Scripts to Launch Daemon on Mac OS X Sierra 10.12.6, by seeing the .plist file](https://medium.com/@fahimhossain_16989/adding-startup-scripts-to-launch-daemon-on-mac-os-x-sierra-10-12-6-7e0318c74de1)

[3] [Run command on startup / login (Mac OS X) - answered by Scott](https://superuser.com/a/229792/894570)