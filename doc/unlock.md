# Unlocking your Robot

The first step in your new adventure is unlocking your robot and
transforming it from a normal Vector to and OSKR enabled Vector where
you will be able to directly access all internals.

## What is Unlocking? Why do we do it?

By default the operating system of Vector is secure and unchangeable
with digital signatures to ensure system integrity. The security starts
with encryption keys baked in to the hardware itself and works its way
down to the final operating system image. In each phase of the boot
process the previous system verifies the next system loads. If even
one bit, byte, letter, etc change in any of the phases then the system
will stop loading and come to a halt. This is done for:

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

At boot time the following verification happens.

1. The hardware chip itself verifies the cryptographic signature on
    the low level ABOOT partition. It is for all practical purposes
    impossible to override this check.

2. The ABOOT partition has another security key baked in that it uses
    to verify the BOOT partition and load it. The BOOT partition is a
    skeleton linux operating system to bootstrap the main system.

3. The BOOT partition loads the appropriate SYSTEM partition which is
    the final software to run.

    In the case of a normal Vector the
    SYSTEM partition is protected by an Android-specific system called
	`dm-verity` to ensure that this partition isn't modified.

    Development Vectors used internally by the company, and now OSKR
    Vectors do not enforce `dm-verity` security on the system
    partition allowing development of new features.

In addition to these layers of cryptographic security a new Vector
comes with a **recovery filesystem** installed for reliability
purposes. This has been loaded on at the factory and is the boot
system of last resort when no other software will load.

We then set aside two slots for day-to-day software usage called **A**
and **B**. When you install an OTA update the system:

1. Downloads the update.

2. Performs some basic verification to see if the image 'fits' with
    the current configuration.

3. Decides if the A or B slot is the available one.

4. Copies the new filesystems on to the slot.

5. Flags the new slot as the 'good' one.

6. Reboots and loads the software. If the software fails to load it
    marks the slot as invalid.

    If there is a previously valid slot, such as **A** after an update
    to **B** was installed, it reverts to that.

    If there is now good **A** or **B** slot it reverts back to the
    **recovery filesystem** so you can try to install again. This is
    important. No matter how bad the running system gets messed up we
    should **always** be able to fall back to the **recovery filesystem**
	
So what does all this mean? We've got two conflicting goals:

1. Normal Vectors should have all the security and reliability that
    are expected for a consumer electronic device and need to remain
    locked down.

2. OSKR Vectors should have some ability to lock down the software
    installed but some ability to modify software for development.

Because of this each type of robot need to enforce different
requirements each needs a different set of cryptographic signatures so
that a normal Vector behaves the way it should and a OSKR unit behaves
the way it should.

To accomplish this and unlock a OSKR robot we need to:

1. Reprogram the ABOOT image with a new cryptographic signature.

2. Sign the new BOOT images with this signature.

3. Create new **recovery filesystems** signed by the new keys because
    the old ones will be considered 'bad' when we swap out the signatures.

This is where things get tricky. The normal assumption is that the
ABOOT and recovery filesystems get installed directly at the factory
and physically written directly to the chips before final assembly of
the robot, and never touched again.

But to do this outside of the factory we need to do this with our OTA
(Over The Air) update scripts. And we need to modify software
partitions that were never expected to be written in an environment
much less reliable than a factory with physical connections to the
hardware. So we must be very careful to make sure that we generate
correct images, and the installation process completes without
interruption.

But enough of the theory let's dive in to the process...

## Step 1: Getting your QSN

The first step in unlocking your Vector is delivering the QSN to Digital Dream
Labs so we can make your custom unlock image. This is a serial number
that is included internally on the unit that is different from the ESN
which you see printed on the bottom of the robot.

To get the QSN:

1. Using the Chrome web browser go to <https://vector-setup.ddl.io>

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

It is **strongly recommended** that you do a test upgrade with a safe
upgrade file before unlocking your robot. If the process is confusing
the first time it's recommended you do a test upgrade again and again
until you feel comfortable with the process.

Remember that modifying the ABOOT and recovery filesystems are
dangerous operations. If the process is interrupted somehow while
system files are installing it will make it impossible to boot
Vector. Some potential points of failure in a normal OTA upgrade are:

* Vector loses power in the middle of the upgrade.

* Your wifi modem breaks or is unplugged in the process.

* Your service providers connection dies in the middle of the OTA file download.

* Some random part of the internet has issues preventing the OTA from
  downloading completely.

This doesn't matter for normal OTA installs because the process is
designed to be safe and we can always fall back to the recovery
software. But when unlocking we are **REWRITING** the recovery
software. An interruption like this can be
disastrous.

To minimize chances of network problems interfering with the update
process the recommended procedure is serve your unlock image from your
computer to Vector locally. This eliminates any possibility of Wide
Area Network issues affecting the outcome.

The process is not particularly complex if you follow the steps as
written but it can be confusing the first time you do it, particularly
if you don't have much experience with networking. Luckily you can
practice by uploading a normal system upgrade manually. This is *safe*
and can be repeated safely.

### Prepping the robot

Reset your Vector completely. First wipe the user data from Vector
then perform a factory reset. Instructions for both of these tasks
can be found in Appendix 2.

### Hosting the OTA locally

We need to host the OTA locally on your computer we're using to update
since there will be no access to anything else at that point in time.

1. Download a local copy of the latest production firmware from
<http://ota.global.anki-services.com/vic/prod/full/latest.ota>.

2. Create a simple webserver to serve up the files. Here we use
   python because it is installed on many systems and has a
   lightweight webserver built in. However there is nothing special
   about python here so you may substitute with a webserver you're
   more familiar with

    Assuming you saved the file in a Downloads folder:

    ```
    cd ~/Downloads # or other directory
	python3 -m http.server
    ```
	
    Or if you don't have python3 installed:
	
    ```
    cd ~/Downloads # or other directory
	python -m SimpleHTTPServer
    ```

3. Determine the LAN ip of your computer. This is different from the
   external address and will generally start with 192.168, or 10. If
   you don't know how to find the address
   [this LifeHacker article](https://lifehacker.com/how-to-find-your-local-and-external-ip-address-5833108)
   might help.

4. Open the Chrome Browser and verify that you can get to the file. If
   your IP address is 192.168.1.130, the link is
   <http://192.168.1.130:8000/>

    You should see a basic index page listing files including latest.ota.

    ![](./img/directory-listing.png)

5. Click on the `latest.ota` file and verify that the link works and
   your browser asks you to download the file.

### Connecting to the advanced console interface in vector web setup

We will use the simulated terminal interface in Vector Web Setup to access
advanced options that aren't available in the normal interface.

1. Open a new tab in Chrome and go to <https://vector-setup.ddl.io>

2. Place Vector in its charging station and press the backpack button
    twice to enter pairing mode.

3. Click "Pair with Vector" and select Vector from the popup.

4. You're now on the screen to enter the pairing code. Uncheck the box
   for **Enable auto-setup flow**. Enter the pin and click **Enter Pin**.

5. You will now be in an emulated terminal session in chrome. Type
    `help` to verify it's working.

    Keep this console open throughout the process.

    ![](./img/vws-advanced-setup.png)


### Starting the deploy

This is the critical step. We want to do this correctly.

1. Go back to your tab with the file directory listing.  Since Vector
will be requesting the file **DO NOT** use links with `localhost` in
them or it won't be able to find the files.

2. Copy the full link for `latest.ota` to your clipboard.

3. Return to the Chrome window with the Terminal session and run
    `ota-start <PASTED LINK FROM ABOVE>`

If all goes well you should see a status bar update, the file should
upload, Vector will reboot, and you'll have the new version of the
firmware.

If it doesn't go well you may get an error status code in the
window. Some of the more common status codes are:

* **203** Vector couldn't find the file. You either had the wrong
    link, such as using `localhost` or you don't have a webserver running.

* **216** Downgrade not allowed. You didn't do a factory reset.

## Step 3: Installing the unlock image

Now we follow the same procedure with the custom generated image for
your Vector. If you skipped ahead to here *please* go through the test
run with a safe file. Assuming you know what you're doing:

1. Start from an updated vector with updated software. The 0.9.0
    recovery software does not support updating the appropriate
    partitions and it will fail to try to install the unlock image.

    Don't worry, it does this safely and you will not damage your
    Vector if you forget to do so.

2.  Use the link to the custom image provided by Digital Dream Labs
    instead of the normal OTA file. Note that each custom image only
    works on ONE Vector. If you have multiple Vectors each will need
    its own image.

After installation is complete Vector should reboot and you should now
see an introductory screen that says OSKR. This means that your
Vector is working and has the new security keys needed for OSKR
development.

We're out of danger now.

## Step 4: Installing the OSKR image

Next we need to install an up-to-date software image to bring back all
the expected functionality. This is a **safe** update again. If there
are problems the Vector will continue to run. No need to be paranoid
on this update.

Get the file <https://exapmple.org> and apply it to Vector the same
way you applied the test upgrade.

## Step 5: Getting your ssh key

Now that Vector is up and running and back to his normal self you'll
want to get the newly created SSH key. Although this isn't strictly
part of the unlock process this key is needed to access Vector's
internals and begin your OSKR adventure! So lets grab it now.

To get the QSN:

1. Using the Chrome web browser go to <https://vector-setup.ddl.io>

2. Follow the instructions to pair with Vector and log in.

3. Click the **Download logs** link located at the top right corner of
    the page.

4. Open the downloaded archive which should extract the contents of
    several logs on to your hard drive.

5.  The file `data/diagnostics/ssh/id_rsa` is your security key. It
    should be installed so it can be used by your system. On Linux and
    OSX:

    ```bash
    cp data/diagnostics/ssh/id_rsa_Victor-X1Y1 ~/.ssh
    chmod 700 ~/.ssh/id_rsa_Victor-X1Y1
	```

6. Load the key in to your keyring: `ssh-add
    ~/.ssh/id_rsa_Victor-X1Y1`

7. Obtain the IP of the robot from the diagnostics screen.

    * put Vector in charger.
    * double click the backpack button.
	* raise the forklift up and down

    You should now see the diagnostics screen which has the IP.

8. Connect! `ssh root@<ROBOT_IP_FROM_ABOVE>`

9. Type the command `exit` to leave the ssh session.

## Congratulations! Vector is unlocked!

## Advanced Network Configuration: Enabling AP mode

If you have a particularly bad router or wifi connection it's best to
find a better connection to kick off the upgrade. However it is
possible to eliminate your router as a dependency and talk directly to
Vector by turning it in to an Access Point. The downside to this is
that you will lose all normal internet access and only be able to
talk to Vector until the process is complete.

This usually provides a better more reliable connection but some
computers' network cards may have more trouble than not connecting to
Vector's 2.4 Ghz network. Once again it is **strongly recommended**
that you test an AP style deploy with the safe upgrade image as listed
in Step 2 above.

Follow the process until bring up the simulated command terminal. From
there:


1. Issue the command `wifi-ap true`. This will display new credentials
    for the new wifi connection.

    ```
    [v5] P7Z4$
    wifi-ap true
             ssid: Vector P7Z4
       password: 20809135
    ```

2. Connect your computer to this access point with this password.

3. Test that you can access the file index at
   <http://192.168.0.2:8000> which will be your new Local IP while
   connected to Vector.

4. Resume the existing process while still on the access point.

If all goes well Vector will install and reboot, and your computer
will automatically go back to its normal network.

![](./img/ap-update.png)
