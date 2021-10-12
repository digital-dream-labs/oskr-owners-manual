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
	10-07 05:13:10.637  2249  2249 I update-engine: @robot.ota_download_endfail1.7.1.68oskr
	Failed to open URL: HTTP Error 403: Forbidden20300026887
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

7. Examine logcat contents to verify we aren't doing updates automatically:
  
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

   `scp bootscreen.gif.raw root@192.168.1.110:/anki/data/assets/cozmo_resources/config/engine/animations/boot_anim.raw`

4. Reboot Vector from the host system:

    `ssh root@192.168.1.110 "/sbin/reboot"`
   
## Edit Phrases

With this task, we can edit what Vector says in different situations- for example, we can change how he describes each weather condition, how he responds to different situations in BlackJack games, or even when he does (or doesn't) recognize a human face! Let's take a look at how to do this.

You will need:
* SSH Access to Vector

1. Once you've logged in via SSH as root, we'll need to make sure we can change files. Remount the partition as rewritable:
`mount -o remount rw /`

2. Now that we can change files, it's time to see what options we have to change for the localized strings. Move into the directory where the localized strings are contained:
`cd /anki/data/assets/cozmo_resources/assets/LocalizedStrings/en-US`

Once there, you'll see 2 files we can edit:
* BehaviorStrings.json
* BlackJackStrings.json

3. We recommend making backups of each file. Use these commands:
`cp BehaviorStrings.json BehaviorStrings.json.bak`
`cp BlackJackStrings.json BlackJackStrings.json.bak`
(If you ever have to restore a file, use `cp` the other way around. Example: `cp BehaviorStrings.json.bak BehaviorStrings.json`)

4. Of the 2 files available here, let's open a file for editing using `nano`. Example:
`nano BehaviorStrings.json`

5. Once the file is open, you will be able to see text entries that define the speech that should happen when certain weather patterns, recognition phases, or other items play. Here is an example:
```
"BehaviorDisplayWeather.Sunny": {
    "translation": "{0} degrees and sunny"
  },
```

6. Let's say we want to change this to something a bit more personal. Feel free to make your own saying:
```
"BehaviorDisplayWeather.Sunny": {
    "translation": "{0} degrees and great for a motorcycle ride"
  },
```

7. Be sure to change the other sayings for each weather type to match the same "theme" - or mix and match! You can also make edits to any other phrases you wish here, but be sure not to destroy any of the quotation marks around the phrases. Once you are finished making changes, we need to save the file with the following steps:
1. Exit the editor: `Ctrl+X`
2. Confirm the changes: `y`
3. Press `Enter` to confirm that you want to keep the same filename.

8. Reboot the robot:
`sudo reboot`

Now you can test your new phrases!

## Customize Eye Color(s)

You've always wanted to choose the *exact* eye color that Vector uses- now you can! With this example, we're going to edit a current color to match exactly what we want (we can't add new eye colors- yet- but for now we can edit the current ones to be exactly how we want them).

You will need:
* SSH Access to Vector

1. Once you've logged in via SSH as root, we'll need to make sure we can change files. Remount the partition as rewritable:
`mount -o remount rw /`

2. Navigate to the directory where the "eye_color_config.json" is stored:
`cd /anki/data/assets/cozmo_resources/config/engine`

3. Create a backup copy of the "eye_color_config.json" file just in case:
`cp eye_color_config.json eye_color_config.json.bak`

4. Begin editing the "eye_color_config.json" file:
`nano eye_color_config.json`

5. Find the color you want to modify. **Bear in mind you will have to continue using the name of the modified color until further source code is released.**

6. Change the "Hue" and "Saturation" values on a particular color to match what you want. You may have to experiment a little to find the perfect color! Valid values range from 0.00 to 1.00. In this example, I will take orange and turn it into red. I will change this section:
```
  "OVERFIT_ORANGE" :
   { "Hue" : 0.05, "Saturation" : 0.95 },
```
to:
```
  "OVERFIT_ORANGE" :
   { "Hue" : 0.97, "Saturation" : 0.97 },
```

7. Now that we've modified the values to new settings, let's save the changes:
	a. Exit the editor: `Ctrl+X`
	b. Confirm the changes: `y`
	c. Press `Enter` to confirm that you want to keep the same filename.

8. Reboot the robot:
`sudo reboot`

Once Vector reboots, you're ready to test your changes! In this example, I changed the color "Orange", so I will say "Hey Vector, make your eyes orange." Vector's eyes will now actually turn to red.

**_Once further code is released, you'll be able to add your own colors. Escape Pod users can already change the command to fit whatever color they changed!_**

## Watch Face Recognition via Console Variables

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
