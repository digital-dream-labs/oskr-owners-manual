# Appendix 2: Vector Maintenance Tasks

This covers several tasks used to maintain all Vectors that are not
specific to OSKR.

## Factory Reset

Since you are modifying the contents of Vector there will be times where
you break the operating system and need to reset Vector and return to
the initial factory recovery filesystem.

To do so:

1. Place Vector on its charging station.

2. Hold its backpack button down until it powers down completely and
    keep holding down.

3. After approximately 15 seconds, the white light at the back of the backpack  
will begin to glow. Release the button.

4. Vector will reboot and start at the initial setup screen just like new.

5. You'll need to upload a newer version of the software via the phone
    App or other means.

Note that a factory reset will **NOT** erase user data. You may or may
not need to do this as well depending on how much you want to clean
out old settings.

## Erasing User Data

There may be times where you want to erase the user configuration on a
given robot. This will erase the entire contents of the /data
partition including the unique ssh key and any files or code you have
placed there while developing with OSKR. It will also give the Vector
a new identity and name.

To do so:

1. Place Vector on its charging station.

2. Press his backpack button twice. The normal BLE Pairing screen
    should appear on its screen.

3. Lift Vector's forklift up and down. A administration menu should
    appear on its screen.

4. Remove Vector from its charging station and spin one of the tank
    treads. This will move the **>** arrow pointing at an option.

5. Select **CLEAR USER DATA**. Lift the forklift up and down to
    proceed. A confirmation screen should appear.

6. Spin the tank treads to select **CONFIRM**. Lift the forklift up
    and down. Vector should reboot and start at the initial setup screen.

7. You will now need to re-attach Vector to your account via the Phone
    App or other means and re-download the newly generated SSH key if
    you wish to SSH in to Vector.

## CCIS Screen

The CCIS screen shows additional information about how Vector is
operating. To access it:

1. Place Vector on its charging station.

2. Press his backpack button twice. The normal BLE Pairing screen
    should appear on its screen.

3. Lift Vector's forklift up and down. A administration menu should
    appear on its screen.

4. Lower Vector's head completely then raise it up completely.

5. Press the backpack button once. You should now see a new screen.

6. Continue to press the backpack button to cycle through the screens.

7. You will eventually return to the original admin screen. Left the
    forklift arm up and down to exit.

## Backing Up JDOCS  

**NOTE: This operation is done at your own risk. Modifying JDOCS file values in a robot can break things, and the only way to un-break them is a Clear User Data reset.**  

JDOCS houses all of Vector's "memory"- his lifetime statistics and more are stored in JavaScript files on the robot in `/data/data/com.anki.victor/persistent/jdocs/`. To back these items up via SSH:  

1. Make Vector's partition writable:  
`mount -o remount rw /`  

2. Move to the 'jdocs' folder in the /data partition:  
`cd /data/data/com.anki.victor/persistent/jdocs`  

3. Zip up the files in this directory- you can change the name of the .tar to whatever you want, this is just an example:  
`tar -cvf VectorsBrain.tar *`  

4. Open a new Command Prompt / Terminal window **from your own machine** and transfer the file from Vector to your own computer.  
Syntax: `scp -i <Vector SSH Key> root@<Vector IP Address>:/data/data/com.anki.victor/persistent/jdocs/<tar name> <destination on your local computer>`  
Example: `scp -i C:\Users\robbie\.ssh\id_rsa_Vector-K9W7 root@192.168.50.117:/data/data/com.anki.victor/persistent/jdocs/VectorsBrain.tar C:\Users\robbie\Desktop`  
