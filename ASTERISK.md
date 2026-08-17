# Introduction





# Build Asterisk 

## Dépôt

Official Debiant Asterisk repository : [asteirks](https://salsa.debian.org/pkg-voip-team/asterisk)

```text
gbp clone https://salsa.debian.org/pkg-voip-team/asterisk asterisk-pkg
git remote add asterisk git@github.com:AmarBoumezbeur/asterisk.git
git remote add upstream http://gerrit.asterisk.org/asterisk
```

## Build

### Upstream

```text
git co -b upstream-16.7.0 origin/upstream
git co -b 16.7.0_alphalink-1 origin/master

gbp import-orig --uscan -v --no-pristine-tar --upstream-version=16.7.0 --upstream-branch=upstream-16.7.0 --debian-branch=16.7.0_alphalink-1 --no-merge

git merge upstream-16.7.0 --commit
```

### Commits

```text
gbp dch -caR --ignore-branch
# Configurer la bonne version dans le changelog (ici 1:16.7.0~alphalink-1)

git push alphalink upstream-16.7.0
git push alphalink 16.7.0_alphalink-1
rsync ../asterisk_16.7.0~dfsg.orig.tar.xz builder-5-por1:asterisk/asterisk_16.7.0~alphalink.orig.tar.xz

dpkg-buildpackage

```

## Erreur uscan

Uscan semble essayer de vérifier la signature de l'archive upstream mais ne trouve pas de clé publique.

La signature est bonne d'après gpg --verify.

Il faut ajouter la clé publique dans debian/upstream/signing-key.asc.


# Asterisk-Java (AGI)

```text
To simplify for everyone what Christophe said:

    1. SIGAL gets outbound_gateways from as-db or carrier_code and executes the DIAL function through asterisk (example):

Sending Dialing command SIP/+33123456789@D201005050001

    2. Asterisk receives the command, it looks for the peer using the peer name @D201005050001

    3. Asterisk needs an IP address to communicate with the peer thus the host IP address
```