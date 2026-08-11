# Introduction

Debianasation of a project in Java, Ruby , bash ... or else.


# What to do :
## Java application
1. Structure:
```text
myapp-1.0.0/
├── debian/
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
2. 