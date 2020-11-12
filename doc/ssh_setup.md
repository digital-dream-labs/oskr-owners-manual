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

    For Windows 10 users:
    ```bash
    Set-Service -Name ssh-agent -StartupType Manual
    Start-Service ssh-agent
    ssh-add id_rsa_Vector-whatever
    ssh root@192.168.whatever```
    
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
