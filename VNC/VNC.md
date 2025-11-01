## Verify Installation

```bash
tightvncserver -version
```

if not 

```bash
sudo apt update
sudo apt install tightvncserver -y
```

## Set VNC password

```bash
vncpasswd
```

## Create the startup script

```bash
nano ~/.vnc/xstartup

```

### For XFCE

```bash
#!/bin/sh
xrdb $HOME/.Xresources
startxfce4 &
```

```bash
chmod +x ~/.vnc/xstartup
```

## Start the VNC Server

```bash
tightvncserver :1

tightvncserver :1 -geometry 1920x1080 -depth 24 # with resolution and bitrate
```

## Stop the VNC server

```bash
tightvncserver -kill :1
ps -ef | grep Xtightvnc
```

## Setup Systemd Service

```bash
sudo nano /etc/systemd/system/tightvncserver@.service
```

```bash
[Unit]
Description=TightVNC remote desktop server for %i
After=network.target

[Service]
Type=forking
User=<your_username>
PAMName=login
PIDFile=/home/<your_username>/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/tightvncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/tightvncserver :%i -geometry 1920x1080 -depth 24
ExecStop=/usr/bin/tightvncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable tightvncserver@1.service
sudo systemctl start tightvncserver@1.service
```

## Secure It

```bash
ssh -L 5901:localhost:5901 user@<remote_ip>
```

## Connect via VNC Viewer

```bash
<remote_ip>:5901
```

