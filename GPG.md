Builders machines

STRETCH:

Decommissioned:

ssh -A builder-5-por1.internal.alphalink.fr -l a.boumezbeur

BULLS-EYE:Decommissioned:

ssh -A builder-7-one.dev.alphalink.fr -l a.boumezbeur

BULLS-EYE: 

Decommissioned:

ssh -A a.boumezbeur@builder-11.admin.alphalink.tech

Used (SSH):

ssh -A a.boumezbeur@builder-11
ssh -A a.boumezbeur@10.44.64.15

L'OS de la machine a.boumezbeur@builder-11 :

$ lsb_release -a
No LSB modules are available.
Distributor ID:    Debian
Description:    Debian GNU/Linux 11 (bullseye)
Release:    11
Codename:    bullseye
}}}

Initial Configuration

Install Git and GPG

First, ensure you have both Git and GPG installed:

sudo apt update
sudo apt install git gnupg

Generate a GPG Key: If you don't already have a GPG key, you can generate one:

gpg --full-generate-key

RSA and RSA (default)keysize: 4096

Key is valid for: 1y or 2y

Nom/Prenom: Christophe Dupont

Email: c.dupont@alphalink.fr

Une passphrase sympathique

List Your GPG Keys:After generating your GPG key, list your keys to get the key ID:

gpg --list-secret-keys

Export key:

Giving KY_ID = 99F0E473

gpg --export -a 99F0E473

Automating GPG Passphrase Entry:

sudo apt install gnupg-agent

Add the following to your ~/.bashrc or ~/.bash_profile:

export GPG_TTY=$(tty)

Then restart your terminal or source the file:

source ~/.bashrc

BuildingVariables d'environnements (.bashrc) utiles à certains scripts (debrsign, debsign):

DEBEMAIL="c.dupont@alphalink.fr"
DEBFULLNAME="Christophe Dupont"

Add a user as maintainer

Configuration repository

git clone ssh://gitolite@gold.alphalink.fr/configuration.git -b production
cd configuration.git

Gpg_Uploaders

cd modules/repository_0_0/files/reprepro/data/packages/debian/conf/gpg_uploaders

Add exported gpg key, example:

# key E2961315: "Nicolas Turpault <n.turpault@alphalink.fr>"
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.9 (GNU/Linux)
mQGiBEmVRnwRBACLaEuNPeJUnh+GNvIY3....
-----END PGP PUBLIC KEY BLOCK-----

Uploaders

cd modules/repository_0_0/files/reprepro/data/packages/debian/conf/uploaders

Add exported gpg key, example:

allow * by key E2961315

Publishing changes

git add .
git commit -m "Adding n.turpault as maintainer"
git push

GoldOn gold machine update configuration.

puppet agent -t

Verification (Result: You should see the list of added keys)

gpg --list-keys

Set DEBSIGN_KEYID Environment Variable: Ensure that the DEBSIGN_KEYID environment variable is set to your GPG key ID:

export DEBSIGN_KEYID=<your-gpg-key-id>

Update ~/.devscripts Configuration: In addition to the gbp.conf settings, ensure that your ~/.devscripts file specifies the GPG key ID for signing:

DEBSIGN_KEYID=<your-gpg-key-id>

Update ~/.gitconfig

signingkey = E2961315

Make pinentry-program Persistent: Ensure that the GPG agent uses the correct pinentry program by editing the GPG agent configuration file and restarting the agent. Edit (or create) the GPG agent configuration file (~/.gnupg/gpg-agent.conf) and specify the pinentry program:

echo "pinentry-program /usr/bin/pinentry-curses" >> ~/.gnupg/gpg-agent.conf

Note: Adjust the path to the pinentry program as needed (e.g., /usr/bin/pinentry-tty, /usr/bin/pinentry-gnome3, etc.).

Then, restart the GPG agent:

gpgconf --kill gpg-agent
gpgconf --launch gpg-agent