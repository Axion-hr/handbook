# TPE EC2 instance access

## Client setup
After you get user on TPE EC2 instances, you can connect with ssh to the following servers on port 229.

```
tpe-production: 3.127.58.31
tpe-production-api-support: 3.66.207.120
tpe-staging: 18.196.99.49
```

As a convenience, you can put the following in ~/.ssh/config

```
Host *
    ServerAliveInterval 60


Host tpe-production
    Hostname 3.127.58.31
    User <server_username>
    Port 229

Host tpe-production-api-support
    Hostname 3.66.207.120
    User <server_username>
    Port 229

Host tpe-staging
    Hostname 18.196.99.49
    User <server_username>
    Port 229
```

Above config can contain path to your private key like in the example below:

```
Host tpe-staging
    Hostname 18.196.99.49
    User <server_username>
    Port 229
    IdentityFile ~/.ssh/server_keys/applin23 <-- this needs to be replaced with proper path.
```

Above means you can have different keys for different servers! 


## Server setup

```
sudo adduser <username>
sudo usermod -a -G sudo <username>
sudo usermod -a -G adm <username>
sudo su <username>
cd ~
mkdir .ssh
chmod 700 .ssh
cd .ssh
vi authorized_keys <- paste public key here, exit with :wq!
chmod 600 authorized_keys
```


