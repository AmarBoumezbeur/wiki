# Prerequisites

1. Gollum is a Ruby gem that requires Ruby ≥ 2.4, Git, and several build dependencies. 
```
sudo apt update
sudo apt install -y ruby ruby-dev make zlib1g-dev libicu-dev build-essential git cmake libssl-dev pkg-config
```

# Installation

1. Install Gollum
```
sudo gem install gollum
```
2. Extra markup (optional)
```
sudo gem install asciidoctor wikicloth org-ruby RedCloth github-markdown
```
3. Create gollum user
```
# Create a dedicated user (recommended for production)
sudo adduser --shell /bin/bash --gecos 'Gollum wiki' gollum
sudo passwd gollum 
# Enter new password
sudo su - gollum

# Configure Git identity
git config --global user.name "Wiki Server"
git config --global user.email "wiki@example.com"

# Initialize the wiki repo
mkdir ~/wiki && cd ~/wiki
git init .

# Create a home page so Gollum has something to serve
echo "# Home" > Home.md
git add . && git commit -m "Initial commit"

exit
```
4. Test run:
```
sudo su - gollum -c "gollum ~/wiki --host 0.0.0.0 --port 4567"
```

# Service

1.  Find the correct gollum binary path
```
which gollum
```
2. Wiki directory
```
# Create the wiki directory if it doesn't exist
sudo mkdir -p /home/gollum/wiki

# Initialize it as a git repo
cd /home/gollum/wiki
sudo git init .
sudo touch Home.md
sudo git add . && sudo git commit -m "init"

# Fix ownership (replace 'gollum' with your actual username if different)
sudo chown -R gollum:gollum /home/gollum/wiki
```
3. Run Gollum as a Systemd Service
```
sudo tee /etc/systemd/system/gollum.service << 'EOF'
[Unit]
Description=Gollum wiki server
After=network.target

[Service]
Type=simple
User=gollum
Group=gollum
WorkingDirectory=/home/gollum/wiki
ExecStart=/usr/local/bin/gollum --host 0.0.0.0 --port 4567 --allow-uploads dir --css
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```
4. Reload and Start
```
sudo systemctl daemon-reload
sudo systemctl enable gollum.service
sudo systemctl start gollum.service
sudo systemctl status gollum.service
```
5. Launch 
```
http://0.0.0.0:4567:/gollum/wiki/Home
```