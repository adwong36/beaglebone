Login as root user
```
sudo -i
```

Create new user
```
adduser <name of user>
```

Add user to list of sudoer 
```
usermod -aG sudo <name of user>
```

Add user to dialout group
```
usermod -aG dialout <name of user>
```

Change hostname
```
nano /etc/hostname > change to desired name
nano /etc/hosts > change 127.0.1.1 <desired hostname>
```

Allow ssh into device
```
nano /etc/ssh/sshd_config

uncomment the following
PermitRootLogin yes
PubkeyAuthentication yes
PasswordAuthentication yes
```

Add ssh keys to computer
```
ssh-keygen
ssh-copy-id <username>@<host>

then login...
ssh <username>@<host>
```

Removing duplicate keys
```
nano ~/.ssh/known_hosts
then remove unwanted keys/host
```

change beaglebone broadcast socket port in /lib/systemd/system/bonescript.socket 
```
[Socket]
ListenStream=80

[Install]
WantedBy=sockets.target
```
