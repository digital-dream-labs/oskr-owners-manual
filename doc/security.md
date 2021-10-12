# Security

By default the system comes in a somewhat locked down state that
prevents random access by most users to the internals. This section
describes several security features and settings, as well as ways to
modify the settings to fit your needs.

## BLE

All initial connections to Vector are made via BLE (BlueTooth Low
Energy) connections via the phone app, a client tool, Vector Web
Setup. These all require you to *pair* the device by entering a code
displayed on the Vector screen.

Due to the limited range of BlueTooth signals and the even more
limited range of being able to see the Vector's screen this does a
very good job of assuring that the person accessing the Vector is in
physical control of the device. Malicious users from around the world
simply can't interface with the Vector at this level.

## Securing SSH

By default the Vector generates a unique SSH user key the first time it
boots and every time you perform a User Data Reset.

SSH works by providing a *private key* that performs encryption
operations and a *public key* that allows another person or system to
verify the encryption operations. As the name indicates the *private
key* should only be given to someone who you want to be able sign in
to the computer and the *public key* is safe to distribute to anyone
without compromising the private key's security.

The default configuration is generally secure although you may wish to
take some steps to harden the SSH connection even more.

### Adding a passphrase to your local SSH key

By default the generated SSH key doesn't have a passphrase and anyone
with access to the file can use it. To add a passphrase:

```
ssh-keygen -p -f ~/.ssh/id_rsa-Vector-X1Y2
```

### Removing the private key from Vector

A user with physical access to Vector can get the private key. Once
you've retrieved a copy you no longer need to keep one on the Vector.
Removing the file will prevent a user from being able to download it.

```
ssh root@<ROBOT_IP>
root@Vector-U9D5:~# ls /data/ssh/                  
authorized_keys         id_rsa_Vector-U9D5      id_rsa_Vector-U9D5.pub
root@Vector-U9D5:~# rm /data/ssh/id_rsa_Vector-U9D5
root@Vector-U9D5:~# ls /data/ssh/
authorized_keys         id_rsa_Vector-U9D5.pub
```

If you do this you should backup your local copy of the key. If it is
lost you'll need to reset User Data to generate a new key and lose any
work on the `/data` partition.

### Removing the Digital Dream Labs dev key

Digital Dream Labs includes a static SSH key to make internal
development and testing easier. Although we respect users' privacy and
would never connect to a customer robot there is always the
possibility that the key gets out in the wild where another party can
use it.

To remove the key connect to Vector and open the file
`/data/ssh/authorized_keys` in a text editor. Delete the line that
starts with `ssh-rsa` and ends with `digital_dream_labs_dev_key`.

## Unsecuring Development Tools

There are several development tools that live behind a firewall on the
robot as they allow any user with access to do a variety of things on
the robot. Since the tools were originally intended for use in a
development environment only they have lower levels of security than
is ideal.

### Temporary access via SSH Port Forwarding

SSH provides options to do *Port Forwarding* to create tunnels that go
from your computer, over the secure connection, to the system you're
logging in to. This can be used to get temporary access to a developer
tool. Although not as convenient as permanently changing firewall
rules, when you disconnect the SSH connection security is
automatically restored.

Example accessing the Anim process webViz server:

1. SSH in to the device with Port Forwarding options:

    ```
    grant@lord-humungus otas % ssh -L8889:localhost:8889 root@192.168.1.110
    root@Vector-U9D5:~# 
    ```
2. Visit <http://localhost:8889> on your computer.

Multiple ports can be forwarded. Here we forward two webViz
connections:

```
ssh  -L8888:localhost:8888 -L8889:localhost:8889  root@<ROBOT_IP>
```

### Permanent access via iptables

The firewall rules are contained in the files
`/etc/iptables/iptables.rules` and `/etc/iptables/ip6tables.rules`
Most users won't be running IPv6 will only need to edit the first
file.

The various exposed ports are already listed in the file but are
commented out so they are not loaded by default:

```
# adb-over-tcp
# -A INPUT -p tcp -m tcp -m tcp --dport 5555 -j ACCEPT

# rsync (dev-deployment)
# -A INPUT -p tcp -m tcp -m tcp --dport 1873 -j ACCEPT

# webots
# -A INPUT -p tcp -m tcp --dport 5103 -j ACCEPT
# -A INPUT -p udp -m udp --dport 5103 -j ACCEPT

# dev-webservices
# -A INPUT -p tcp -m tcp -m multiport --dports 8887,8888,8889,8890 -j ACCEPT

# wwise profiler
# -A INPUT -p tcp -m tcp -m multiport --dports 24024,24025,24026 -j ACCEPT
# -A INPUT -p udp --dport 24024 -j ACCEPT
```

To open up a resource permanently:

1. Mark the system filesystem as read/write. `mount -o remount,rw /`

1. Open the file `/etc/iptables/iptables.rules` in your editor of
choice.

2. delete the `#` sign comment from the applicable rule.

3. Restart iptables to load the rules: `systemctl restart iptables`

## Manifest signing keys

By default Vector has security checks that will only allow an .ota
file that is signed by Digital Dream Labs to be installed. As people
in the community make their own modifications they can make their own
.otas. To be able to install a community-created ota you will need to
whitelist the creator's signing key. To do so:

1. On your host computer ensure the appropriate directory is created:
   `sudo root@<ROBOT_IP> mkdir /data/etc/ota_keys`

2. Obtain and verify the public signing key from the creator of the
    OTA.

3. Copy the key to vector: `scp new_key.pub
    root@<ROBOT_IP>:/data/etc/ota_keys`

You will now be able to install OTAs signed with this key manually.

> **WARNING**: An OTA file modifies the entire operating system and has
> full control of Vector, and can perform very destructive actions
> either intentionally or unintentionally. Only install OTAs from
> sources you trust.
