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

5. Utilisation
    * Exemple d'IP a bloquée:
    ```
    212.46.52.231
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