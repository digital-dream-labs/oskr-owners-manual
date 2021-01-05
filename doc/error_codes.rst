******************************
Appendix 1: Vector Error Codes
******************************

Ota Error Codes
###############

=====  =======================================
Code     Reason
=====  =======================================
 1      Switchboard: unknown status
 2      Switchboard: OTA in progress
 3      Switchboard: OTA completed
 4      Switchboard: rebooting
 5      Switchboard: Other OTA error
  10    OS: Unknown system error
 200    Unexpected .tar contents
 201    Unhandled manifest version or feature
 202    Boot Control HAL failure
 203    Could not open URL
 204    URL not a TAR file
 205    Decompressor error
 206    Block error
 207    Imgdiff error
 208    I/O error
 209    Signature validation error
 210    Decryption error
 211    Wrong base version
 212    Subprocess exception
 213    Wrong serial number
 214    Dev / Prod mismatch
 215    Socket Timeout (network stall)
 216    Downgrade not allowed
 217   	
 218   
 219    Other Exception 
=====  =======================================

Runtime Error Codes
###################

These are defined in victor/robot/include/anki/cozmo/shared/factory/fault_codes.h


=====  ===========================================================
Code    Reason
=====  ===========================================================
 700    SHUTDOWN_BUTTON	Shutdown caused by button push
 701    SHUTDOWN_GYRO_NOT_CALIBRATING
 702    SHUTDOWN_BATTERY_CRITICAL_VOLT
 705    SHUTDOWN_BATTERY_CRITICAL_TEMP
 800    NO_ANIM_PROCESS	
 801    DFU_FAILED	
 840    NO_CAMERA_CALIB	(Must run through playpen)
 850    CLOUD_CERT	
 851    CLOUD_TOKEN_STORE	
 852    CLOUD_READ_ESN	
 870    MIC_FR	
 871    MIC_FL	
 872    MIC_BR	
 873    MIC_BL	
 890    CLIFF_FR	
 891    CLIFF_FL	
 892    CLIFF_BR	
 893    CLIFF_BL	
 894    TOF	
 895    TOUCH_SENSOR	
 898    SPINE_SELECT_TIMEOUT	
 899    NO_BODY 
 909    STOP_BOOT_ANIM_FAILED	
 911    AUDIO_FAILURE	
 913    NO_SWITCHBOARD	
 914    NO_ENGINE_PROCESS
 915    NO_ENGINE_COMMS	
 916    NO_ROBOT_PROCESS	
 917    NO_ROBOT_COMMS	
 919    SYSTEMD	
 921    NO_GATEWAY	
 923    CLOUD_PROCESS_DIED
 960    IMU_FAILURE	
 970    WIFI_HW_FAILURE	
 980    CAMERA_FAILURE	
 981    CAMERA_STOPPED	
 990    DISPLAY_FAILURE	
=====  ===========================================================

