## Checklist 

This is a detailed checklist of the steps to follow in unlocking your robot. I
recommend printing it out for each robot you unlock, and ticking off each
item, in order. The sections after this checklist will describe how to
perform each step in more detail.

### Preparation 1: Getting your QSN and submitting it to Digital Dream Labs
- [ ] 1. Open <https://vector-setup.ddl.io> in a Chrome browser
- [ ] 2. Place Vector in its charging station and press the backpack button twice to enter pairing mode.
- [ ] 3. Click "Pair with Vector" and select Vector from the popup.
- [ ] 4. Click the **Download Logs** link at the top right
- [ ] 5. Find your QSN and ESN in those logs
- [ ] 6. Write down the QSN and ESN on this page -- we'll double check this below.
- [ ] 7. Submit your QSN and ESN to Digital Dream Labs

### Peparation 2: Setting up your computer to upgrade Vector
While you wait for your unlock OTA, you can set up your computer and prepare Vector. 
First, set up a HTTP server to deliver OTA files to your robot:

- [ ] 8. Install python on your computer, if you haven't already
- [ ] 9. Download the last production software from <https://ota.global.anki-services.com/vic/prod/full/latest.ota>
- [ ] 10. Start a simple webserver with the latest.ota
- [ ] 11. Find the IP address for your computer
- [ ] 12. Check that you can connect to this local webserver with Chrome.

### Preparation 3: Prepare Vector for the upgrade 

- [ ] 13. Fully charge your Robot
- [ ] 14. Keep (or put) Vector on the charging dock

Erase Vector's data:
- [ ] 15. Place Vector on its charging station.
- [ ] 16. Press his backpack button twice.
- [ ] 17. Lift Vector's forklift up and down.
- [ ] 18. Remove Vector from its charging station and spin one of the tank  
treads.
- [ ] 19. Move the tread to select **CLEAR USER DATA**.
- [ ] 20. Lift the forklift up and down to proceed.
- [ ] 21. Spin the tank treads to select **CONFIRM**. Lift the forklift up  
and down.

Enter Recovery Mode (aka a Factory Reset):
- [ ] 22. Place Vector on its charging station.
- [ ] 23. Hold Vector's backpack button down for 15 seconds, until you see  
the white light start to glow all the way on the back.
- [ ] 24. Wait for Vector to finish rebooting

Connect to Vector:
- [ ] 25. Open <https://vector-setup.ddl.io> in a Chrome browser
- [ ] 26. Place Vector in its charging station and press the backpack button twice to enter pairing mode.
- [ ] 27. Click "Pair with Vector" and select Vector from the popup.
- [ ] 28. You had to enter the PIN number into the Chrome webapp, right?  If you connect, and it doesn't ask for you to enter your PIN, you  
are (probably) not in recovery mode. Go back to the steps to enter recovery mode.
- [ ] 29. Verify that the emulated console is working by typing `help`

Connect to the Wi-Fi
- [ ] 30. Run, in the emulated console, `wifi-scan` to check that Vector can see your wifi
- [ ] 31. Have Vector connect to your wifi with `wifi-connect ` followed by your Wifi SSID and password  

Do not close the console in Chrome.

### Deploy 1: Test Deploying the latest Production Software OTA image

Deploy the latest production software:
- [ ] 32. Copy the full link for `latest.ota` file from your computer (from step 12)
- [ ] 33. In the emulated console, Type `ota-start ` followed by this link
- [ ] 34. Check that Vector successfully applied the production software

### Deploy 2: Deploying the Unlock OTA image
When you receive your unlock OTA email, go to the next step

- [ ] 35. Download the unlock OTA image from the email into your local server folder
- [ ] 36. Check that your robot has the same ESN serial number as the one you submitted  
earlier, and wrote down above. The serial number is on the bottom. [Here is how to find it.](https://support.digitaldreamlabs.com/article/338-how-do-i-find-vectors-serial-number)  
If the number does not match, you have the wrong bot.

Prepare to server the unlock OTA file:
- [ ] 37. Open <https://vector-setup.ddl.io> in a Chrome browser
- [ ] 38. Place Vector in its charging station and press the backpack button twice to enter pairing mode.
- [ ] 39. Click "Pair with Vector" and select Vector from the popup. Enter the PIN code that appears.
- [ ] 40. Verify that the emulated console is working by typing `help`
- [ ] 41. Put a book or heavy block in front of Vector to keep him from driving  
off the charging station while updating.


Deploy the unlock OTA software:
- [ ] 42. Copy the full link for unlock `.ota` file with the ESN serial number (Example: `0060378a.ota`)
- [ ] 43. Type `ota-start ` followed by this link
- [ ] 44. Check that Vector successfully applied the unlock software

### Deploy 3: Deploying the latest OSKR Software OTA image
- [ ] 45. If installing using a Python server, download the last production software from <https://ota.global.anki-services.com/vic/oskr/full/latest.ota>  
into your local server folder - otherwise skip to Step 46.

Prepare installation of the latest unlocked OSKR OTA:
- [ ] 46. Using a Bluetooth-enabled computer or mobile device, open <https://vector-setup.ddl.io> in a Chrome browser
- [ ] 47. Place Vector in its charging station and press the backpack button twice to enter pairing mode.
- [ ] 48. Click "Pair with Vector" and select Vector from the popup. Enter the PIN code that appears on the bottom of Vector's screen.
- [ ] 49. Press Alt+2 or Alt+3 to open the emulated console. Verify that the emulated console is working by typing `help`.

Deploy the latest OSKR software:
- [ ] 50. Copy the full link for OSKR `.ota` file: <http://ota.global.anki-services.com/vic/oskr/full/latest.ota>
- [ ] 51. Type `ota-start ` followed by this link. The full command should be: `ota-start <http://ota.global.anki-services.com/vic/oskr/full/latest.ota>`
- [ ] 52. Vector will apply the latest update OTA and then restart. Check that Vector successfully applied the OSKR software by  
double-tapping his back button. The software version will appear after "OS:"
- [ ] 53. Using the mobile App, sign into Vector and add your account to him

- Alternate Installation: [Using a Secure Shell](https://oskr.ddl.io/oom/doc/unlock_checklist.html#getting-secure-shell), issue the following command to kick off the update-engine script  
and pull the newest OTA: `UPDATE_ENGINE_DEBUG=true UPDATE_ENGINE_URL=http://ota.global.anki-services.com/vic/oskr/full/latest.ota /anki/bin/update-engine`  
- Vector will reboot into the new firmware.

### Getting Secure Shell

- [ ] 54. Use <https://vector-setup.ddl.io> in a Chrome browser to connect to and pair with your Vector
- [ ] 55. Download logs from Vector using the "Download Logs" button at the top right.
- [ ] 56. Extract the "id_rsa" key from the "/data/ssh" folder
- [ ] 57. Put the RSA key into the right place (the "right place" varies depending on operating system)
- [ ] 58. Get Vector's IP address
- [ ] 59. Use PuTTY or a command line in Terminal to log in using SSH with the key

Congratulations! Vector is unlocked!

### If you need to turn Vector into an Access Point to get the Key
These should only be used as necessary.

- [ ] 60. Use <https://vector-setup.ddl.io> in a Chrome browser to pair your with Vector
- [ ] 61. Issue the 'wifi-ap true' command
- [ ] 62. Have your computer connect to the Vector's wifi access point
- [ ] 63. Test a browser connection to <http://192.168.0.2:8000>
