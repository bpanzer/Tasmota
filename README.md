## General

All documentation from the upstream [Tasmota](https://github.com/arendst/Tasmota/) project is valid and important. 
Here are just the enhancements described.

## Goal and usage:

The original Tasmota firmware intentionally doesn't offer a standalone-mode which runs an AP permanently. 
Thus I created this fork as I long for such an operation mode (I call it WIFI_PERM_AP).

This fork keeps all original Tasmota functionality intact (including firmware updates, also OTA updates). 
The WIFI_PERM_AP mode is not persisted. It must be enabled every time the device is restarted.

If you like to use it you have to specify your WIFI_AP_PASSPHRASE in file my_user_config.h, compile the project, and flash the built firmware.

At runtime you configure Tasmota (once) as usual by entering one or two wifi networks to connect to. 
When the device starts one of these wifi networks should be availabe. Tasmota will connect as station (client). 
Then you can switch to WIFI_PERM_AP mode by entering "wificonfig 9" on the web console (or via http request, or via mqtt).

Afterwards the device opens an AP with given passphrase and keeps it running. 
You can connect to it and control the device e.g. using http requests.
In order to stop WIFI_PERM_AP mode you can simply restart the device.

## Development

Changes I've made for this fork. They're in feature branch WIFI_PERM_AP.
- In file my_user_config.h added constant EMPTY_STR beneath WIFI_AP_PASSPHRASE.
- In file tasmota.h appended option WIFI_PERM_AP to the list of WifiConfigOptions. Note its value (9). This value will be used with the wificonfig command.
- In function CmndWifiConfig() in file support_command.ino made sure that also the new value WIFI_PERM_AP will be stored in Settings.sta_config.
- In function WifiCheck() in file support_wifi.ino don't decrease Wifi.counter for WIFI_PERM_AP mode. Call WifiConfig() also in WIFI_PERM_AP mode.
- In function WifiConfig() in file support_wifi.ino don't increase Wifi.config_counter and Wifi.counter for WIFI_PERM_AP mode. Instead, execute WifiManagerBegin for WIFI_PERM_AP.
- The function WifiManagerBegin() in file xdrv_01_webserver.ino always calls StartWebserver() with HTTP_MANAGER or HTTP_MANAGER_RESET_ONLY. However, we need to call StartWebserver() with HTTP_ADMIN for WIFI_PERM_AP mode. Therefore we need one more flag in the signature of WifiManagerBegin(). Also, for WIFI_PERM_AP mode the softAP should be started using WIFI_AP_PASSPHRASE, and using empty passphrase otherise.


## License

This program is licensed under GPL-3.0
