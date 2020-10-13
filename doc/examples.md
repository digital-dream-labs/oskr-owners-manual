# Example Customization Tasks

## Disable Automatic updates

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

6. Reboot to ensure changes take effect: `/sbin/reboot`

1. Examine logcat contents to verify we aren't doing updates automatically:
  
    ```
    root@Vector-U9D5:~# logcat update-engine:* *:S
    --------- beginning of main
    ```

    There are no longer entries from update-engine in the logs.

## Change Boot Animation

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

    `ssh root@192.168.1.110 "/sbin/reboot"`

## Watch Face recognition via console variables

Here we'll use console variables to test out the face recognition of
Vector.

1. Open up the firewall to provide access to the `vic-engine`
    webserver if you haven't already. To do this quickly `ssh -L
    8888:localhost:8888 root@<ROBOT_IP>`

2. Verify the page is up by going to <http://localhost:8888>

3. Look at all available console variables:
    <http://localhost:8888/consolevarlist>

4. Here we're interested in `MirrorMode`

5. Refer to the main page on the webserver for instructions on how to
    set a console variable.

    You can type this in directly to your web
    browser but most professional developers would use a CLI tool like
    `curl` or `wget`. On your computer, and **NOT** the Vector ssh
    session:

    ```
    curl "http://localhost:8888/consolevarset?key=MirrorMode&value=1"
	```

	Note that in general software developers assume zero is the same
    as **off** and a non-zero value is the same as **on**. Here we use
    1 to turn the setting on and will later turn it off with 0.

6. The screen of Vector should now show what it is seeing. Look at
    Vector and you should see a box drawn around your head. The bottom
    of the screen should show either `UNKNOWN` or your name if you've
    previously identified yourself.

7. Lets assume you haven't identified yourself. Say "Hey Vector... My
    Name is XXXX" Vector will perform his scanning and hopefully
    recognize you and say hi.

    You will now see your name when he sees your face.

8. Now that we've seen that in action lets change Vector back to his
    normal self:

    ```
    curl "http://localhost:8888/consolevarset?key=MirrorMode&value=0"
	```

## Simulate a crash with a console function

1. Open up the firewall to provide access to the `vic-engine`
    webserver if you haven't already. To do this quickly `ssh -L
    8888:localhost:8888 root@<ROBOT_IP>`

2. Refer to the page <http://localhost:8888/consolefunclist> for a
    list of available functions. In this case we'll use
    `IntentionalAbort` to simulate a software crash and verify that
    Vector will do a soft reboot.

    You issue the command in directly from your web browser but most
    professional developers would use a CLI tool like `curl` or `wget`. On
    your computer, and **NOT** the Vector ssh session:

    ```
    curl "http://localhost:8888/consolefunccall?func=IntentionalAbort"
	```
