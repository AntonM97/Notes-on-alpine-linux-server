# Notes-on-alpine-linux-server
[1. Initial setup details and first necessity console commands](#initial-setup-details-and-first-necessity-console-commands)

[2. My case specific details](#my-case-specific-details)


## Initial setup details and first necessity console commands

`poweroff` - to shut down all and make it stop consuming electricity

**home-server login:** `root`


##### Check the connection
`ping 8.8.8.8` - packages from google    
and press Ctrl+C to make it stop


##### Update

`apk update`
`apk upgrade`


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

##### Find your IP
`ip addr show` and in the output look for `eth0` field:`inet`

##### Add basic tools

`apk add bash curl wget nano sudo git`

##### Add user (avoid accessing from root)
`adduser yourname` (and set password secure enough so that some compromised smarthone or fridge won't have an easy win after scanning devices of your home network)

`addgroup yourname wheel`

enable sudo `visudo` and find the group and uncomment it `%wheel ALL=(ALL) ALL`

when the job is done you can leave editor by pressing `esc` and typing `:qw` and then pressing `Enter` 


##### Change user password

If you already accessed as user:
```
passwd
```

If you accessed as root:
```
passwd username
```

##### Access from main PC (The Client)

Open your Terminal.

Type the following command (replace username with your Alpine username and the IP with the one you found earlier): `ssh username@192.168.1.50`

The first time you connect, you’ll see a warning about the "authenticity of the host." Type yes to continue.

Enter your password.


if you have to change password from server `passwd yourname`
if you try to change your username after you accessed through ssh you type just `passwd`


chmod 700 /home/alice/.ssh
chmod 600 /home/alice/.ssh/authorized_keys

Then put the user's public SSH key into authorized_keys, for example:

ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... alice@computer

Check the result

ls -ld /home/alice/.ssh
ls -l /home/alice/.ssh/authorized_keys

You should see permissions approximately like:

drwx------  alice alice  ... .ssh
-rw-------  alice alice  ... authorized_keys

One additional thing: make sure the user's home directory itself is owned by them:

chown alice:alice /home/alice


##### Drop ssh key on the server
`ssh-copy-id -i /path/to/custom/key.pub username@localIP` example: `ssh-copy-id -i ~/.ssh/server_key.pub root@192.168.1.50`

## My case specific details

I use old netbook with x32 one 2-threaded core - so there might be issues with java version that is not supporting x32 after JDK17
so has to check if works with 17 or downgrade to JDK11 or even JDK8

    Spring Boot 3.x,
    Minimum Java version: 17
    Boot 3.x requires Java 17 or later.
    Boot 2.7.x also runs on Java 17 (but is end‑of‑life).

✔ Spring Data JPA 3.x

    Ships with Spring Boot 3.x,
    Uses Hibernate 6 (also requires Java 17),
    Uses jakarta.persistence.\* instead of javax.\*

✔ Spring Security 6.x

    Ships with Spring Boot 3.x,
    Requires Java 17,
    Removes WebSecurityConfigurerAdapter,
    Uses new lambda‑based DSL
    
If you cannot run Java 17 (only Java 11 or Java 8 available in 32‑bit)** →

You are limited to:
    Spring Boot 2.7.x, 
    Spring Security 5.8.x
    Spring Data JPA 2.7.x (Hibernate 5.x)
Boot 2.7.x is end‑of‑life, but still functional.
