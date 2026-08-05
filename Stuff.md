# Miscellaneous

## Empty /var/log/ (safe)

```
sudo truncate -s 0 /var/log/syslog
```

## Blocaque IP

1. Il faut vérifier d'abord si l'IP est configurée sur l'extranet pour le compte en question
```
0990000424864
```

2. Si elle est configurée il faut la supprimer pour que le script peut la virer
3. Une fois l'IP supprimer ou si elle est déja supprimée, il faut la bloquer en exécutant le script.
4. Le script
    ```
    configuration/tools/ban_ip_openvno.rb
    ```
    * Syntax
    ```
    ruby ban_ip_openvno.rb <<ACTION>> <<IP-ADDRESS>>
    ```
Where :

<<ACTION>> can be add or del

add = means you would like to add the ip address in the blocked list (so you block traffic coming from this ip address at ip level)

del = means you would like to remove the ip address from the blocked list (so you unblock traffic comming from this ip address at ip level)

<<IP-ADDRESS>> is the ip address to be blocked or unblocked

You must specify 1 ip address.
In theory, you can specifiy many ip addresses separated by space (never tested)
5. Utilisation
    * Exemple d'IP a bloquée:
```
212.46.52.231
```
    * Exécution
```
ruby ban_ip_openvno.rb del 212.46.52.231
```
6. Vérification si le ban a fonctionné
```
### Checking an IP that is not in the list:
ipset test offenders 212.46.52.231
  >> 212.46.52.231 is NOT in set offenders.

### Checking an IP that is in the list:
ipset test offenders 212.46.52.231
  >> 212.46.52.231 is in set offenders.
```
## SSH

### Generate SSH keys

1. ED25519: 
```
ssh-keygen -t ed25519 -C "your_email@example.com"

```
2. RSA: 
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

### Permissions


```
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub

```

Copy the SSH publique key to the remote server.

## GIT
### Install

```
sudo apt update
sudo apt install git

```

### Configuration

```
git --version
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
git config --global init.defaultBranch master
git config --global core.editor nano

git config --global pull.rebase false      # merge on pull (default, explicit is clearer)
git config --global color.ui auto          # colored output
git config --global core.autocrlf input    # line-ending handling (Linux/Mac)
```

### Check configuration

```
git config --list

git config --list --global
```

### Edit configuration

```
git config --global --edit
```

## GPG


## Bash completion