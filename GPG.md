# Introduction

Une page qui décrit comment configurer GPG sur un builder.

## Builders machines

1. Decommissioned:
    * STRETCH:
```text
ssh -A builder-5-por1.internal.alphalink.fr -l a.boumezbeur
```
    * BULLS-EYE:
```text
ssh -A builder-7-one.dev.alphalink.fr -l a.boumezbeur

ssh -A a.boumezbeur@builder-11.admin.alphalink.tech
```
2. Used (SSH):
```text
ssh -A a.boumezbeur@builder-11
ssh -A a.boumezbeur@10.44.64.15
```
3. L'OS de la machine a.boumezbeur@builder-11 :
```text
$ lsb_release -a
No LSB modules are available.
Distributor ID:    Debian
Description:    Debian GNU/Linux 11 (bullseye)
Release:    11
Codename:    bullseye
}}}
```text

## Initial Configuration

* Install Git and GPG: First, ensure you have both Git and GPG installed:
```text
sudo apt update
sudo apt install git gnupg
```

* Generate a GPG Key: If you don't already have a GPG key, you can generate one:
```text
gpg --full-generate-key

RSA and RSA (default)keysize: 4096

Key is valid for: 1y or 2y

Nom/Prenom: Christophe Dupont

Email: c.dupont@alphalink.fr

Une passphrase sympathique
```

* List Your GPG Keys:After generating your GPG key, list your keys to get the key ID:
```text
gpg --list-secret-keys
```

* Export key:
```text
Giving KY_ID = 99F0E473

gpg --export -a 99F0E473
```

* Automating GPG Passphrase Entry:
```text
sudo apt install gnupg-agent
```

* Add the following to your ~/.bashrc or ~/.bash_profile:
```text
export GPG_TTY=$(tty)
```

* Then restart your terminal or source the file:
```text
source ~/.bashrc

BuildingVariables d'environnements (.bashrc) utiles à certains scripts (debrsign, debsign):

DEBEMAIL="c.dupont@alphalink.fr"
DEBFULLNAME="Christophe Dupont"
```

* Add a user as maintainer
    * Configuration repository
```text
git clone ssh://gitolite@gold.alphalink.fr/configuration.git -b production
cd configuration.git
```
    * Gpg_Uploaders
```text
cd modules/repository_0_0/files/reprepro/data/packages/debian/conf/gpg_uploaders
```
    * Add exported gpg key, example:
```text
# key E2961315: "Nicolas Turpault <n.turpault@alphalink.fr>"
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.9 (GNU/Linux)
mQGiBEmVRnwRBACLaEuNPeJUnh+GNvIY3....
-----END PGP PUBLIC KEY BLOCK-----
```
    * Uploaders
```text
cd modules/repository_0_0/files/reprepro/data/packages/debian/conf/uploaders
```
    * Add exported gpg key, example:
```text
allow * by key E2961315
```
    * Publishing changes
```text
git add .
git commit -m "Adding n.turpault as maintainer"
git push
```

    * On gold machine update configuration.
```text
puppet agent -t
```
    * Verification (Result: You should see the list of added keys)
```text
gpg --list-keys
```
* Set DEBSIGN_KEYID Environment Variable: Ensure that the DEBSIGN_KEYID environment variable is set to your GPG key ID:
```text
export DEBSIGN_KEYID=<your-gpg-key-id>
```

* Update ~/.devscripts Configuration: In addition to the gbp.conf settings, ensure that your ~/.devscripts file specifies the GPG key ID for signing:
```text
DEBSIGN_KEYID=<your-gpg-key-id>
```
* Update ~/.gitconfig
```text
signingkey = E2961315
```
* Make pinentry-program Persistent: Ensure that the GPG agent uses the correct pinentry program by editing the GPG agent configuration file and restarting the agent. Edit (or create) the GPG agent configuration file (~/.gnupg/gpg-agent.conf) and specify the pinentry program:
```text
echo "pinentry-program /usr/bin/pinentry-curses" >> ~/.gnupg/gpg-agent.conf
```
Note: Adjust the path to the pinentry program as needed (e.g., /usr/bin/pinentry-tty, /usr/bin/pinentry-gnome3, etc.).

* Then, restart the GPG agent:
```text
gpgconf --kill gpg-agent
gpgconf --launch gpg-agent
```