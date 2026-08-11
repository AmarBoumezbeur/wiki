Configuration

Add user-name and e-mail

Configure Git user information

git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

Configuration GPG key:Configure Git to use your GPG key:

git config --global user.signingkey $GPG_KEY_ID

Enable signing:Enable GPG signing for commits and tags

git config --global commit.gpgSign true
git config --global tag.gpgSign true

Automate passphrase

Configure gpg-agent to cache passphrase

echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
source ~/.bashrc

Set the default editorSet the default text editor for Git
git config --global core.editor “nano”

Enable color output for Git commands

git config --global color.ui auto

Verify configuration

git config --list