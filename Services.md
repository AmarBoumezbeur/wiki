# Introduction

How to create a linux .service from a script or an application that has a server.

# Configure the .service

## Script
    * Let's say the script is in : /opt/bin/local/myapp.sh
    
    * In:
```text
nano /etc/systemd/system/myapp.service
```
    * .service configuration:
```text
[Unit]
Description=My Application
After=network.target postgresql.service
Wants=postgresql.service

[Service]
Type=simple
User=myapp
Group=myapp
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/myapp.sh
Restart=on-failure
RestartSec=5
Environment=RACK_ENV=production
EnvironmentFile=-/etc/myapp/env

# Hardening
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=strict
ReadWritePaths=/var/log/myapp

[Install]
WantedBy=multi-user.target
```
    * Activate the .service
```text
sudo systemctl daemon-reload
sudo systemctl enable gollum.service
sudo systemctl start gollum.service
sudo systemctl status gollum.service
```

## Server

    * Gollum for example, after installing it
```text
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
    * Activate the .service
```text
sudo systemctl daemon-reload
sudo systemctl enable gollum.service
sudo systemctl start gollum.service
sudo systemctl status gollum.service
```