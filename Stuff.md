# SSH
## Generate SSH keys

1. ED25519: 
`ssh-keygen -t ed25519 -C "your_email@example.com"
`
2. RSA: 
`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
`

## Permissions

chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub

Copy the SSH publique key to the remote server.

# GIT
## Install

`sudo apt update
sudo apt install git`

## Configuration

`git --version
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
git config --global init.defaultBranch master
git config --global core.editor nano

git config --global pull.rebase false      # merge on pull (default, explicit is clearer)
git config --global color.ui auto          # colored output
git config --global core.autocrlf input    # line-ending handling (Linux/Mac)`

## Check configuration

`
# List configuration
git config --list
# or just for global scope:
git config --list --global`

## Edit configuration

`git config --global --edit
`

# GPG


# Bash completion