# Introduction


# Configuration

* Add user-name and e-mail: configure Git user information
```text
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

* Configuration GPG key:Configure Git to use your GPG key:
```text
git config --global user.signingkey $GPG_KEY_ID
```

* Enable signing:Enable GPG signing for commits and tags
```text
git config --global commit.gpgSign true
git config --global tag.gpgSign true
```

* Automate passphrase: configure gpg-agent to cache passphrase
```text
echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
source ~/.bashrc
```
* Set the default editorSet the default text editor for Git
```text
git config --global core.editor “nano”
```
* Enable color output for Git commands
```text
git config --global color.ui auto
```
* Verify configuration
```text
git config --list
```