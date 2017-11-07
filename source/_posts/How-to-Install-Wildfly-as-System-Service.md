---
layout: post
title: How to Install Wildfly as System Service
date: 2017-11-07 16:49:15
tags:
- Wildfly
category:
- Java
---
The latest wildfly is wildfly-11.0.0.Final. Acutally the tutorial is located in jboss.home.dir/docs/contrib/scripts. There are init.d way and systemd way. There's also a service way which used for Windows system.

As I just tried the systemd way, so I just introduce it.
Open systemd/README file, it says clearly:

--------------
== How to configure WildFly as a systemd service

== Create a wildfly user

    # groupadd -r wildfly
    # useradd -r -g wildfly -d /opt/wildfly -s /sbin/nologin wildfly

== Install WildFly

    # tar xvzf wildfly-11.0.0.Final.tar.gz -C /opt
    # ln -s /opt/wildfly-11.0.0.Final /opt/wildfly
    # chown -R wildfly:wildfly /opt/wildfly

== Configure systemd

    # mkdir /etc/wildfly
    # cp wildfly.conf /etc/wildfly/
    # cp wildfly.service /etc/systemd/system/
    # cp launch.sh /opt/wildfly/bin/
    # chmod +x /opt/wildfly/bin/launch.sh

== Start and enable

    # systemctl start wildfly.service
    # systemctl enable wildfly.service
-------------------------
After you execute the commands above, you would probably get some error:
1. `Failed to execute operation: Access denied` when executing "systemctl start wildfly"
I can't come up with the solution with google, I just reboot and it is gone.
2. service started failed, and log file says `java.lang.RuntimeException: java.net.UnknownHostException: centostest: centostest: Name or service not known`.
This error is just because of the host name is not added to hosts file. I came up with a workaround with add `127.0.0.1 centostest` to /etc/hosts file.
3. The application can be visited by http://127.0.0.1:8080/app but can't be visit by local network PCs. You need to modify jboss.home.dir/standalone/configuration/standalone.xml, search the interfaces element, edit like this:
```
    <interfaces>
        <interface name="management">
            <inet-address value="${jboss.bind.address.management:127.0.0.1}"/>
        </interface>
        <interface name="public">
            <!--<inet-address value="${jboss.bind.address:127.0.0.1}"/>-->
            <any-address/>
        </interface>
```
And restart service by:
`# systemctl restart wildfly`
4. If you still can't visit the application, it might be firewall issue. You can disable firewall to check if it works:
`# systemctl stop firewalld.service       (for redhat/centos)`
`# ufw disable     (for debain/ubuntu)`
If it works, you just need to add port 8080 and 8443 to firewall(Below is just for redhat/centos).

a. enable firewall
`sudo systemctl start firewalld.service`
b. enable http
`$ sudo firewall-cmd —zone=public —permanent —add-service=http`
c. enable https
`sudo firewall-cmd —zone=public —permanent —add-service=https`
d. open port 8080
`sudo firewall-cmd —zone=public —permanent —add-port=8080/tcp`
e. open port 8443
`sudo firewall-cmd —zone=public —permanent —add-port=8443/tcp`
f. reload firewall configuration
`sudo firewall-cmd —reload`

For debain/ubuntu:
a. enable firewall
`sudo ufw enable`
b. enable http
`sudo ufw allow http`
c. enable https
`sudo ufw allow https`
d. open port 8080 and 8443
`sudo ufw allow 8080/tcp`
`sudo ufw allow 8442/tcp`
e. reload configuration
`sudo rfw reload`