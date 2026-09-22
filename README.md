# Notes-on-alpine-linux-server
[1. Initial setup details and first necessity console commands](#initial-setup-details-and-first-necessity-console-commands)

[2. My case specific details](#my-case-specific-details)







## Initial setup details and first necessity console commands

`poweroff` - to shut down all and make it stop consuming electricity

**default home-server login for alpine linux:** `root`


#### Check the connection
`ping 8.8.8.8` - packages from google    
and press Ctrl+C to make it stop

#### Update

`apk update`
`apk upgrade`

***
problem with packages might be caused by mistakes in 

`etc/apk/repositories`

It might have duplicates or some repositories are 

You can look into it with `cat /etc/apk/repositories`

do some backup just in case: run `cp etc/apk/repositories etc/apk/repositories.backup`

and run (pay attention to the '>' sign) ```
cat > /etc/apk/repositories <<EOF



EOF



`sudo apk add openssh` install ssh

`sudo rc-service sshd start` start the service

`sudo rc-update add sshd` make it start on boot



#### Find your IP
`ip addr show` and in the output look for `eth0` field:`inet`

#### Add basic tools

`apk add bash curl wget nano sudo git`

#### Add user (avoid accessing from root)
`adduser yourname` (and set password secure enough so that some compromised smarthone or fridge won't have an easy win after scanning devices of your home network)

`addgroup yourname wheel`

enable sudo `visudo` and find the group and uncomment it `%wheel ALL=(ALL) ALL`

when the job is done you can leave editor by pressing `esc` and typing `:qw` and then pressing `Enter` 


#### Change user password

If you already accessed as user:
```
passwd
```

If you accessed as root:
```
passwd username
```

#### Access from main PC (The Client)

Open your Terminal.
Type the following command (replace username with your Alpine username and the IP with the one you found earlier): `ssh username@192.168.1.50`
The first time you connect, you’ll see a warning about the "authenticity of the host." Type 'yes' to continue.

Enter your password.


if you have to change password from server `passwd yourname`
if you try to change your username after you accessed through ssh you type just `passwd`


`chmod 700 /home/username/.ssh` (7-Owner: read+write+execute; 0-Group: no permissions; 0-everyone else: no permissions)
For the .ssh directory, this means only the account owner can access it.

- r = list files in the directory

- w = create/delete/rename files

- x = enter/access the directory


`chmod 600 /home/username/.ssh/authorized_keys`

Then put the user's public SSH key into authorized_keys, for example:

ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... alice@computer

Check the result
```
ls -ld /home/username/.ssh
ls -l /home/username/.ssh/authorized_keys
```
You should see permissions approximately like:
```
drwx------  username username  ... .ssh
-rw-------  username username  ... authorized_keys
```
One additional thing: make sure the user's home directory itself is owned by them:

`chown alice:alice /home/alice` (`chown`-change ownership; `-R`-recursively, means  directory and files/subdirectories inside it; `alice:alice`-fist:owner second:group)



#### Adding key to authorized_keys

If you need first to check if the key is working use `cat >> ~/.ssh/authorized_keys` - it won't replace previous keys

But with one `>` it will replace so -> `cat > ~/.ssh/authorized_keys`  Paste the new public key (usually the entire line beginning with ssh-ed25519, ssh-rsa, etc.), then press Ctrl-D.

#### Drop ssh key on the server
(You actually don't have to do that if you have inserted contents of your public key to the authorized_keys)
`ssh-copy-id -i /path/to/custom/key.pub username@localIP` example: `ssh-copy-id -i ~/.ssh/server_key.pub root@192.168.1.50`






















## My case specific details

I use old netbook with x32 one 2-threaded core - so there might be issues with java version that is not supporting x32 after JDK17

so has to check if works with 17 or downgrade to JDK11 or even JDK8

- Spring Boot 3.x,
- Minimum Java version: 17
- Boot 3.x requires Java 17 or later.
- Boot 2.7.x also runs on Java 17 (but is end‑of‑life).


Spring Data JPA 3.x

- Ships with Spring Boot 3.x,
- Uses Hibernate 6 (also requires Java 17),
- Uses jakarta.persistence.\* instead of javax.\*

Spring Security 6.x
- Ships with Spring Boot 3.x,
- Requires Java 17,
- Removes WebSecurityConfigurerAdapter,
- Uses new lambda‑based DSL

    
If you cannot run Java 17 (only Java 11 or Java 8 available in 32‑bit)** you're limited to:
- Spring Boot 2.7.x, (is end‑of‑life, but still functional)
- Spring Security 5.8.x
- Spring Data JPA 2.7.x (Hibernate 5.x)


### Cloudflared installation
```
cd /tmp
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-386

chmod +x cloudflared-linux-386
sudo mv cloudflared-linux-386 /usr/local/bin/cloudflared

cloudflared --version
```
OR
```
scp cloudflared-linux-386 root@192.168.1.50:/root/

chmod +x cloudflared
mv cloudflared /usr/local/bin/cloudflared

cloudflared --version

```

then run cloudflare secvice while passing a token to it
```
cloudflared service install eyJhIjoi...
```





### PostgreSQL 18 installation 
18 is the latest version since [[1]].

To install PostgreSQL 18,
```
apk add postgresql18 postgresql18-contrib
rc-update add postgresql
rc-service postgresql start
```
more here https://wiki.alpinelinux.org/wiki/Postgresql

### Java Installation
Details here https://wiki.alpinelinux.org/wiki/Installing_Oracle_Java
