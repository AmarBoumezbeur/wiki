# Introduction


# Spring framework



# Example
## SIGAL


## How to build

### PREREQUISITES

Make sure builder machine are available and configured:

    * [GPG](/gollum/create/GPG)
    * [GIT](/gollum/create/GIT)

### Mac OS

#### Packet manager: 
Le gestionnaire de paquets utilisé est homebrew disponible ici: https://brew.sh/

#### Maven:
```text
brew install --ignore-dependencies maven
```

#### Java-17:
```text
brew install openjdk@17
echo 'export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

#### Git:
```text
brew install git
```

#### Protocol Buffer:
```text
brew install protobuf@3
echo 'export PATH="/opt/homebrew/opt/protobuf@3/bin:$PATH"' >> ~/.zshrc
nano ~/.zshrc
Ajouter ces éléments:
export LDFLAGS="-L/opt/homebrew/opt/protobuf@3/lib"
export CPPFLAGS="-I/opt/homebrew/opt/protobuf@3/include"
source ~/.zshrc
Vérifier la version de protobuf (dernière version fonctionnelle: 3.20.3):
protoc --version
```

### Linux

#### Git:
```text
sudo apt install git
```

#### Dependencies
Java project manager:
```text
sudo apt install maven
```

Java-17:
```text
apt install openjdk-17-jdk
apt install openjdk-17-jre-headless ca-certificates-java
```

Protocol Buffers:
```text
sudo apt install protobuf-compiler
sudo apt install protobuf-compiler-grpc
sudo apt install protobuf-compiler-grpc-java-plugin
```

Building dependencies:
```text
sudo apt install git-buildpackage
sudo apt install dh-exec
```

### SIGAL DEPENDENCIES

#### esl-client

original repository
```text
git clone https://github.com/voiceip/esl-client
cd /home/username/esl-client
touch pom.xml
nano pom.xml
```

pom.xml:
```text
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<modelVersion>4.0.0</modelVersion>
<groupId>org.freeswitch.esl.client</groupId>
<artifactId>esl-client</artifactId>
<version>0.10.6</version>
<name>esl-client</name>
<description>description</description>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-compiler-plugin</artifactId>
<version>3.8.1</version>
<configuration>
<source>1.8</source>
<target>1.8</target>
</configuration>
</plugin>
</plugins>
</build>
<dependencies>
<dependency>
<groupId>io.netty</groupId>
<artifactId>netty-all</artifactId>
<version>4.1.86.Final</version>
<scope>compile</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/com.google.guava/guava -->
<dependency>
<groupId>com.google.guava</groupId>
<artifactId>guava</artifactId>
<version>32.1.2-jre</version>
</dependency>
<dependency>
<groupId>org.slf4j</groupId>
<artifactId>slf4j-api</artifactId>
<version>1.7.25</version>
<scope>compile</scope>
</dependency>
<dependency>
<groupId>ch.qos.logback</groupId>
<artifactId>logback-classic</artifactId>
<version>1.2.9</version>
<scope>runtime</scope>
</dependency>
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>4.13.1</version>
<scope>test</scope>
</dependency>
<dependency>
<groupId>com.fasterxml</groupId>
<artifactId>jackson-xml-databind</artifactId>
<version>0.6.2</version>
<scope>compile</scope>
</dependency>
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-core</artifactId>
<version>2.13.0</version>
<scope>compile</scope>
</dependency>
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-annotations</artifactId>
<version>2.13.0</version>
<scope>compile</scope>
</dependency>
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-databind</artifactId>
<version>2.13.4.1</version>
<scope>compile</scope>
</dependency>
<dependency>
<groupId>org.apache.commons</groupId>
<artifactId>commons-lang3</artifactId>
<version>3.1</version>
<scope>compile</scope>
</dependency>
</dependencies>
<distributionManagement>
<repository>
<id>thirdparty</id>
<name>thirdparty</name>
<url>http://10.0.2.180:8081/nexus/content/repositories/thirdparty/</url>
</repository>
</distributionManagement>
</project>
```

local repository
```text
git clone git@gitlab.alphalink.tech:voip/esl-client.git
cd /home/username/esl-client
mvn install
```

Installation
```text
mvn install:install-file  -Dfile=target/esl-client-0.10.6.jar -DgroupId=esl-client -DartifactId=esl-client -Dversion=0.10.6 -Dpackaging=jar -DgeneratePom=true -X
```

asterisk-java

Original repository
```text
git clone git@gitlab.alphalink.tech:voip/asterisk-java.git
cd /home/username/asterisk-java
mvn install
```

## Installation

mvmannstall:install-file  -Dfile=target/asterisk-java.jar -DgroupId=asterisk-java -DartifactId=asterisk-java -Dversion=3.39.1 -Dpackaging=jar -DgeneratePom=true -X


BUILDING SIGAL DEBIAN PACKAGE

BASICS

Clone SIGAL:

git clone git@gitlab.alphalink.tech:voip/sigal.git

Checkout branch:

cd /home/username/sigal
sudo git branch MAN
sudo git checkout MAN

Compiling:Exclude tests:

mvn -Dmaven.test.skip=true clean install -U -X

Clean install:

mvn clean install -U -X


RELEASING PACKAGE

After pushing the changes to the git branch.Upgrade Sigal version:

sed -i 's/5.1.0/6.0.0/g' core/pom.xml com.initsys.com.google.protobuf-java/pom.xml as/pom.xml routag/pom.xml agent/pom.xml pom.xml
git add .
git commit -m "Bump 6.0.0"
git push

Update the changelog:

gbp dch -caR --debian-branch MAN
git push

Example:

[ Adrien Martin ]
 * Support for JSON options in LIDB and EXDB.
 * Support routing by calling number
 * Update Maven PMD plugin
 * Fix /var/lib/sigal/ owner
 * Upgrade to Ehcache 3 with JCache (JSR 107)
 * Upload Redis LUA scripts at startup
 * Do not try to add Location header for C5 using number without INSEE code
 * Upgrade Postgresql JDBC
 * Upgrade Spring
 * Emergency number detection from translations
 * Identity header support through Vault

[ Amar Boumezbeur ]
 * Bump 6.0.0
 * Full support of Identity Header Signature and Verification

-- Amar Boumezbeur <a.boumezbeur@alphalink.fr>  Sun, 16 Jul 2023 16:18:22 +0200

Create tag:

git checkout MAN
git tag -a 6.0.0 -m "6.0.0"
git push

Build debian package:

gbp buildpackage -sa --git-debian-branch=MAN_Final --git-upstream-tree=6.0.0-0 --git-no-pristine-tar
cd ..
ls -l
a.boumezbeur@builder-11 ~/sigal_builds 
drwxr-xr-x 10 a.boumezbeur a.boumezbeur     4096 Jul 17 23:09 sigal
-rw-r--r--  1 a.boumezbeur a.boumezbeur 57506332 Jul 17 23:11 sigal_6.0.0-0_all.deb
-rw-r--r--  1 a.boumezbeur a.boumezbeur    31887 Jul 17 23:11 sigal_6.0.0-0_amd64.build
-rw-r--r--  1 a.boumezbeur a.boumezbeur     9009 Jul 17 23:11 sigal_6.0.0-0_amd64.buildinfo
-rw-r--r--  1 a.boumezbeur a.boumezbeur     2698 Jul 17 23:11 sigal_6.0.0-0_amd64.changes
-rw-r--r--  1 a.boumezbeur a.boumezbeur     1295 Jul 17 23:11 sigal_6.0.0-0.dsc
-rw-r--r--  1 a.boumezbeur a.boumezbeur  8057089 Jul 17 23:09 sigal_6.0.0-0.git
-rw-r--r--  1 a.boumezbeur a.boumezbeur   252878 Jul 16 16:42 sigal_6.0.0.orig.tar.gz

Push package to GOLD:

dput bullseye-test ../sigal_6.0.0-0_amd64.changes