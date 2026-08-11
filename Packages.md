# Introduction

Debianasation of a project in Java, Ruby , bash ... or else.


# What to do :
## Java application
1. Structure:
```text
myapp-1.0.0/
├── debian/
|   |   application.properties
│   ├── control
│   ├── changelog
│   ├── rules
│   ├── compat            (or debian/control: Build-Depends: debhelper-compat)
│   ├── copyright
│   ├── myapp.install
│   ├── myapp.dirs
│   ├── myapp.service      (if using systemd)
│   ├── myapp.postinst     (optional)
│   ├── myapp.prerm        (optional)
│   └── source/
│       └── format
├── src/                    (your Java source, or pre-built JAR)
└── pom.xml / build.gradle
```
    * application.properties
```text
#debug=true
logging.config=/etc/sigal/log4j2.properties

spring.main.allow-bean-definition-overriding=true
spring.main.allow-circular-references=true
spring.cache.type=jcache
spring.cache.jcache.config=classpath:ehcache.xml
# Using Spring-boot Webflux enable a different set of classes which prevent using @ImportResource
spring.main.web-application-type=none
```
    * Control
```text
Source: myapp
Section: java
Priority: optional
Maintainer: Amarb <you@alphalink.fr>
Build-Depends: debhelper-compat (= 13), dh-exec, maven, default-jdk
Standards-Version: 4.6.0

Package: myapp
Architecture: all
Depends: default-jre-headless, ${misc:Depends}
Description: Short description
Longer description, indented with one space.
```
    * rules (the build recipe)
```text
#!/usr/bin/make -f

include /usr/share/dpkg/pkg-info.mk

export VERSION = $(DEB_VERSION_UPSTREAM)

%:
	dh $@ --with-systemd

override_dh_auto_build:
	mvn clean install
	# or: gradle build
```
    * myapp.install (maps built files into the package tree better than in rules)
```text
#! /usr/bin/dh-exec
agent/target/myapp-${VERSION}.jar => /usr/share/sigal/myapp.jar
debian/application.properties /etc/myapp/
```
    * myapp.service (daemon configuration)
```text
[Unit]
Description=myapp
After=network-online.target
Wants=network-online.target

[Service]
User=myapp
EnvironmentFile=/etc/default/myapp
# NB: $JAVA_PARAMS is not the same as ${JAVA_PARAMS}, the first create as many argument as necessary, the second only one
ExecStart=/usr/bin/java $JAVA_PARAMS -jar /usr/share/myapp/myapp.jar
TimeoutStartSec=20
TimeoutStopSec=20
Restart=on-failure
WorkingDirectory=/var/lib/myapp
SyslogIdentifier=myapp

[Install]
WantedBy=multi-user.target
```
    * compact
```text
# debhelper-compat version
13
```
    * changelog (gbp dch -caR --debian-branch @branch)
```text
myapp (1.0.0-1) bullseye; urgency=medium

  * Initial release.

 -- Amarb <you@alphalink.fr>  Tue, 11 Aug 2026 12:00:00 +0200
```
    * myapp.postinst (create myapp user)
```text
#!/bin/sh

set -e

if [ "$1" = "configure" ]; then
  adduser --system --group --quiet --home / --no-create-home --shell /bin/false myapp
  chown myapp:myapp /var/lib/myapp
fi

#DEBHELPER#

exit 0
```
    * myapp.dirs
```text
/etc/myapp
/var/lib/myapp
```
    * myapp.default (JMXTERM configuration)
```text
JAVA_PARAMS=-Dconfig.path=/etc/myapp \
-Dspring.config.location=/etc/myapp/application.properties \
-Dcom.sun.management.jmxremote \
-Dcom.sun.management.jmxremote.port=1099 \
-Dcom.sun.management.jmxremote.authenticate=true \
-Dcom.sun.management.jmxremote.password.file=/etc/sigal/jmxremote.password \
-Dcom.sun.management.jmxremote.access.file=/etc/sigal/jmxremote.access \
-Dcom.sun.management.jmxremote.local.only=true \
-Dcom.sun.management.jmxremote.ssl=false
```

## Ruby application
1. Generate debian/
```text
sudo apt install gem2deb
cd myapp/
gem2deb
```
2. Structure
```text
myapp-1.0.0/
├── debian/
│   ├── control
│   ├── changelog
│   ├── rules
│   ├── compat / debhelper-compat in control
│   ├── copyright
│   ├── myapp.dirs
│   ├── myapp.service       (if daemonized)
│   └── myapp.postinst
├── lib/
├── bin/
└── myapp.gemspec           (if packaged as a gem)
```
    * control
```text
Source: myapp
Section: ruby
Priority: optional
Maintainer: Amarb <amarb@alphalink.fr>
Build-Depends-Indep: debhelper (>= 9), ruby, gem2deb, ruby-rspec, ruby-sequel, ruby-sqlite3
Standards-Version: 3.9.3
XS-Ruby-Versions: all

Package: myapp
Architecture: all
Depends: ${misc:Depends}, ruby-zepar (>= 0.6), ruby-sequel, ruby-pg
Recommends: ruby-sequel-pg
Description: Description
XS-Ruby-Versions: ${ruby:Versions}
```
    * rules (rspec used for unitary tests)
```text
#!/usr/bin/make -f

%:
	dh $@ --buildsystem=ruby --with ruby --with systemd

# allow heartbeat like cluster to upgrade without starting prov-as on slave
override_dh_systemd_enable:
	dh_systemd_enable --no-enable

override_dh_systemd_start:
	dh_systemd_start --no-start

# service not enabled but need to install tmpfiles
override_dh_installinit:
	dh_installinit --no-start

override_dh_auto_test:
	rspec
```
    * compact
```text
13
```
    * myapp.service
```text
[Unit]
Description=myapp
Requires=network-online.target
After=network-online.target

[Service]
User=myapp
Group=myapp
ExecStartPre=-/bin/rm /run/prov-as/myapp.pid
ExecStart=/usr/bin/myapp
Restart=always

[Install]
WantedBy=multi-user.target

```
    * myapp.dirs
```text
/usr/bin/library /usr/bin/myapp
```
    * myapp.postinst
```text
#!/bin/sh

set -e

if [ "$1" = "configure" ]; then
  adduser --system --group --quiet --home / --no-create-home --shell /bin/false myapp
fi

#DEBHELPER#

exit 0
```