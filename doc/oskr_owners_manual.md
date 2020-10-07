# Intro

# Unlocking your Robot

The first step in your new adventure is unlocking your robot and
transforming it from a normal Vector to and OSKR enabled Vector where
you will be able to directly access all internals.

## What is Unlocking?

By default the operating system of Vector is secure and
unchangeable. The security starts with encryption keys baked in to the
hardware itself and works its way down to the final operating system
image. In each phase of the boot process the previous system verifies
the next system loads. If even one bit, byte, letter, etc change in
any of the phases then the system will stop loading and come to a
halt. This is done for:

* **Security** We want to make sure a malicious user hasn't modified
    the code to do something that it shouldn't be doing, like sending spam
    emails. Even if a hacker or bot network is somehow able to access
    the Vector they won't be able to make any changes to turn it in to
    the wrong kind of bot.

* **Reliability** We don't want mistakes to break the device. There
    are currently hundreds of thousands of Vectors out there, and we're
    hoping for millions and billions. At that level even small
    problems can add up quickly. Ensuring the Operating System hasn't
    been changed ensures reliability.

The downside of this configuration is that Vector can't be hacked or
modified. *Unlocking* maintains the top levels of security but allows
us to use a modifiable main system so that we can make changes.

The unlock image is build individually for each unique robot so that a
robot can only be unlocked intentionally by its owner, keeping Vectors
that aren't OSKR units safe and reliable.

The unlock image modifies low-level parts of Vector's filesystem that
were expected to never be modified after leaving the factory. Care
must be taken to do everything correctly or you can 'brick' the unit
making it inoperable.

## Step 1: Getting your QSN

The first step in unlocking your Vector is delivering the QSN to Digital Dream
Labs so we can make your custom unlock image. This is a serial number
that is included internally on the unit that is different from the ESN
which you see printed on the bottom of the robot.

To get the QSN:

1. Using the Chrome web browser go to https://vector-setup.ddl.io

2. Follow the instructions to pair with Vector and log in.

3. Click the **Download logs** link located at the top right corner of
    the page.

4. Open the downloaded archive which should extract the contents of
    several logs on to your hard drive.

5. Open the file `factory/log1`. It should look like this:

    ```
    total 13
    dr--r-----    3 888      888           1024 Jan  1  1970 .
    drwxr-xr-x    4 root     root          1024 Jan  1 00:00 ..
    dr--r-----    2 888      888           1024 Jan  1  1970 007084d8
    -r--r-----    1 888      888           1472 Jan  1  1970 AnkiRobotDeviceCert.pem
    -r--r-----    1 888      888           1704 Jan  1  1970 AnkiRobotDeviceKeys.pem
    -r--r-----    1 888      888            792 Jan  1  1970 Info007084d8.json
    QSN=324416252 # ESN=007084d8
    ```

6. Deliver the QSN and ESN from the last line of the file to Digital
    Dream Labs.

If you have trouble extracting the archive you can send the entire
.tar.bz2 file to Digital Dream Labs.

## Step 2: Doing a Test Upgrade

Since the application of the unlock image is sensitive and if done
wrong it can kill Vector, it is highly recommended that you go through
a test run of the upgrade process by installing a normal ota file using
the same process you will use to install the unlock image.

A normal ota is safe. If something goes wrong Vector wlll just revert
to factory mode. The unlock image must update the factory mode itself
so if that goes wrong there is nothing to fall back to.

The test upgrade should be done to ensure the unlock upgrade goes as
expected. If the process is confusing please repeat the test upgrade
again until you feel you have a good understanding of what's going on.

## Step 3: Installing the unlock image

Now with file.

## Step 4: Installing the OSKR image

Next we need to install an up-to-date software image to bring back all
the expected functionality. This is a **safe** update again. If there
are problems the Vector will continue to run. No need to be paranoid
on this update.

## Step 5: Getting your ssh key

Now that Vector is up and running and back to his normal self you'll
want to get the newly created SSH key. Although this isn't strictly
part of the unlock process this key is needed to access Vector's
internals and begin your OSKR adventure! So lets grab it now.

## Congratulations! Vector is unlocked!

# Filesystem layout

A brief overview follows.

## system partition

The system partition contains all the code and configuration to run
the underlying variant of the Android operating system and the
Userspace Anki code that powers Vector.

When you perform an upgrade of the Operating System the system
partition is completely overwritten and all changes are lost.

By default it is read-only and expected to be unmodifiable.

## /anki directory

This is not strictly a partition but it is where all of the Vector
specific code is located. Various services, utiilities, configuration
settings that are not relevant to the Operating System but to Vector
are located here. If you're trying to track down something that you
wouldn't expect to find on a normal Linux operating system it is
probably located here.

Inside you will see some familiar folders such as `/anki/etc/`
`/anki/bin` and others that follow the general configuration of a
Linux operating system as well as a `/data/` folder which holds
various thing that don't quite fit in with the normal file layout.

## /data partition

The `/data` partition is the only partition that is mounted read-write
on a production Vector. All user settings are saved here. When an
system upgrade is installed the data partition remains unmodified and
intact and all old data is available for use by the new version of the
Operating System.

This is where you will want to put any files, scripts, executables,
etc that you want to survive a system upgrade.

## Editing the system partition

Although the system partition is initially mounted read only it is
trivial to mount it in read-write mode since you are the root user. To
do so just run:

    mount -o remount,rw /

And you will now be able to edit the filesystem.

# Tools

## Text editors

To edit configuration files, change settings, etc, you'll find
yourself using a text editor to perform a variety of tasks. The
following text editors are installed by default. If you don't know
which one to use you probably want to start with **nano**.

* **nano** - The recommended editor for most users. Provides basic
    instructions on the screen.

* **vi** - A very low resource editor that operates in modal mode that
    is confusing to beginners. However it is available on the most
    minimal of server and embedded installs. It is useful to know the
    basics of vi as it will be installed on systems with no other editors.
	
* **mg** - A low footprint editor that mimics the basic keystrokes and
    opeations of the popular text editor emacs.
	
* **emacs** - Is not installed but aliased to the mg program for
    people who keep typing `emacs` out of muscle memory.

## logcat

## das

## webViz

## Offboard Vision Server
    http://192.168.1.111:8888/consolevarset?key=Offboard&value=1
    http://192.168.1.111:8888/consolevarset?key=MirrorMode&value=1
	coretech/vision/tools/offboard_vision_server/offboard_vision_server.py

# Security

By default the system comes in a somewhat locked down state that
prevents random access by most users to the internals.

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
take some steps to harden the ssh connection even more.

### Adding a passphrase to your local ssh key

By default the generated ssh key doesn't have a passphrase and anyone
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

## Unsecuring Development Tools

There are several development tools that live behind a firewall on the
robot as they allow any user with access to do a variety of things on
the robot. Since the tools were originally intended for use in a
development environment only they have lower levels of security than
is ideal.

### Temporary access via SSH Port Forwarding

ssh provides options to do *Port Forwarding* to create tunnels that go
from your computer, over the secure connection, to the system you're
logging in to. This can be used to get temporary access to a developer
tool. Although not as convenient as permenently changing firewall
rules, when you disconnect the ssh connection security is
automatically restored.

Example accessing the Anim process webViz server:

1. ssh in to the device with Port Forwarding options:

    ```
    grant@lord-humungus otas % ssh -L9889:localhost:8889 root@192.168.1.110
    root@Vector-U9D5:~# 
    ```
2. Visit http://localhost:9889 on your computer.

Multiple ports can be forwarded. Here we forward two webViz
connections:

```
ssh  -L9888:localhost:8888 -L9889:localhost:8889  root@<ROBOT_IP>
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

# Example Customization Tasks

## Basic

### Disable Automatic updates

If you're making changes to the system partition you'll want to
disable automatic upgrades so you don't lose your work.

1. Examine logcat contents to see that we are trying to do updates
    every hour:
  
    ```
	root@Vector-U9D5:~# logcat update-engine:* *:S
	--------- beginning of main
	10-07 05:13:09.568  2214  2214 I update-engine: @robot.ota_download_start000025820
	10-07 05:13:10.637  2249  2249 I update-engine: @robot.ota_download_endfail1.7.1.68oskrFailed to open URL: HTTP Error 403: Forbidden20300026887
    ```

2. Examine the contents of the update file located at
    `/anki/etc/update-engine.env`

    ```
    root@Vector-U9D5:~# cat /anki/etc/update-engine.env 
    UPDATE_ENGINE_ENABLED=True
    UPDATE_ENGINE_ALLOW_DOWNGRADE=False
    UPDATE_ENGINE_BASE_URL=https://ota.global.anki-services.com/vic/prod/
    UPDATE_ENGINE_ANKIDEV_BASE_URL=https://ota.global.anki-dev-services.com/vic/master/lo8awreh23498sf/
	```

3. Note that the `UPDATE_ENGINE_ENABLED` is set to `True`.

4. Mount the system filesystem for editing: `mount -o remount,rw /`

5. Open `/anki/etc/update-engine.env` in your text editor of choice
    and change the value to `False`. Save and close.

6. Reboot to ensure changes take effect: `/sbin/rboot`

1. Examine logcat contents to verify we aren't doing updates automatically:
  
    ```
	root@Vector-U9D5:~# logcat update-engine:* *:S
	--------- beginning of main
    ```

    There are no longer entries from update-engine in the logs.

### Change Boot Animation

It's easy to replace the boot animation with one you like
better. You will need to convert an animated gif to raw format and
copy to the robot. For this you will need:

* A working installation of python with the Pillow package installed.

* An animated .gif with a resolution of 184x96 pixels

* The script `gif_to_raw.py` to convert the .gif to a raw image.

Both the script and a sample image called `bootscreen.gif` are located
in the github repository for this document under the
`examples/change_boot_anim` directory.

In this example we'll be operating on your HOST computer and doing
everything from there. We will not be issuing commands directly on the
robot.

1. Convert the .gif to a raw image:

    `python gif_to_raw.py bootscreen.gif`

    This will create a new file `bootscreen.gif.raw`

2. Mount the filesystem for writing. Here we'll do that from the host
    system:

    `ssh root@192.168.1.110 "mount -o remount,rw /"`

3. Use `scp` to copy the file in to place:

    `scp bootscreen.gif.raw
    root@192.168.1.110:/anki/data/assets/cozmo_resources/config/engine/animations/boot_anim.raw`

4. Reboot Vector from the host system:

    `ssh root@192.168.1.110 "mount -o remount,rw /"`

### Console vars via webVIz



# Appendices

## Appendix 1: Error Codes
