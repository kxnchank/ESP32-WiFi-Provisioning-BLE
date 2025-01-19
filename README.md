# ESP32-WiFi-Provisioning-BLE
An ESP32 project demonstrating Wi-Fi provisioning using ESP-IDF and BLE, complete with setup instructions and serial output logs.



Hardware Required
Example should be able to run on any commonly available ESP32/ESP32-S2 development board.

Platform : iOS (I have used)
For iOS, a provisioning application along with source code is available on GitHub : esp-idf-provisioning-ios

Configure the project
idf.py menuconfig

Build and Flash
Build the project and flash it to the board, then run monitor tool to view serial output:

idf.py -p PORT flash monitor


Example Output
I (445) app: Starting provisioning
I (1035) app: Provisioning started
I (1045) wifi_prov_mgr: Provisioning started with service name : PROV_261FCC


python esp_prov.py --transport ble --service_name PROV_261FCC --sec_ver 1 --pop abcd1234 --ssid myssid --passphrase mypassword

Above command will perform the provisioning steps, and the monitor log should display something like this :
I (39725) app: Received Wi-Fi credentials
    SSID     : myssid
    Password : mypassword
.
.
.
I (45335) tcpip_adapter: sta ip: 192.168.43.243, mask: 255.255.255.0, gw: 192.168.43.1
I (45345) app: Provisioning successful
I (45345) app: Connected with IP Address:192.168.43.243
I (46355) app: Hello World!
I (47355) app: Hello World!
I (48355) app: Hello World!
I (49355) app: Hello World!
.
.
.
I (52315) wifi_prov_mgr: Provisioning stopped
.
.
.
I (52355) app: Hello World!
I (53355) app: Hello World!
I (54355) app: Hello World!
I (55355) app: Hello World!


Wi-Fi Scanning
python esp_prov.py --transport ble --service_name PROV_261FCC --pop abcd1234

output from esp_prov tool on running above command:
Connecting...
Connected
Getting Services...
Security scheme determined to be : 1

==== Starting Session ====
==== Session Established ====

==== Scanning Wi-Fi APs ====
++++ Scan process executed in 1.9967520237 sec
++++ Scan results : 5

++++ Scan finished in 2.7374596596 sec
==== Wi-Fi Scan results ====
S.N. SSID                              BSSID         CHN RSSI AUTH
[ 1] MyHomeWiFiAP                      788a20841996    1 -45  WPA2_PSK
[ 2] MobileHotspot                     7a8a20841996   11 -46  WPA2_PSK
[ 3] MyHomeWiFiAP                      788a208daa26   11 -54  WPA2_PSK
[ 4] NeighborsWiFiAP                   8a8a20841996    6 -61  WPA2_PSK
[ 5] InsecureWiFiAP                    dca4caf1227c    7 -74  Open

Select AP by number (0 to rescan) : 1
Enter passphrase for MyHomeWiFiAP :

==== Sending Wi-Fi credential to esp32 ====
==== Wi-Fi Credentials sent successfully ====

==== Applying config to esp32 ====
==== Apply config sent successfully ====

==== Wi-Fi connection state  ====
++++ WiFi state: connected ++++
==== Provisioning was successful ====

Sending Custom Data
wifi_prov_mgr_endpoint_create();
wifi_prov_mgr_endpoint_register();

==== Sending Custom data to esp32 ====
CustomData response: SUCCESS

Troubleshooting

Provisioning failed
It is possible that the Wi-Fi credentials provided were incorrect, or the device was not able to establish connection to the network, in which the the esp_prov script will notify failure (with reason). Serial monitor log will display the failure along with disconnect reason :
E (367015) app: Provisioning failed!
    Reason : Wi-Fi AP password incorrect
    Please reset to factory and retry provisioning

Provisioning does not start
If the serial monitor log shows the following :
I (465) app: Already provisioned, starting Wi-Fi STA

it means either the device has been provisioned earlier with or without success (e.g. scenario covered in above section), or that the Wi-Fi credentials were already set by some other application flashed previously onto your device. On setting the log level to DEBUG this is clearly evident :
D (455) wifi_prov_mgr: Found Wi-Fi SSID     : myssid
D (465) wifi_prov_mgr: Found Wi-Fi Password : m********d
I (465) app: Already provisioned, starting Wi-Fi STA

Unsupported platform
==== Esp_Prov Version: v1.0 ====
BLE client is running in console mode
    This could be due to your platform not being supported or dependencies not being met
    Please ensure all pre-requisites are met to run the full fledged client
BLECLI >> Please connect to BLE device `PROV_261FCC` manually using your tool of choice
BLECLI >> Was the device connected successfully? [y/n] y
BLECLI >> List available attributes of the connected device
BLECLI >> Is the service UUID '0000ffff-0000-1000-8000-00805f9b34fb' listed among available attributes? [y/n] y
BLECLI >> Is the characteristic UUID '0000ff53-0000-1000-8000-00805f9b34fb' listed among available attributes? [y/n] y
BLECLI >> Is the characteristic UUID '0000ff51-0000-1000-8000-00805f9b34fb' listed among available attributes? [y/n] y
BLECLI >> Is the characteristic UUID '0000ff52-0000-1000-8000-00805f9b34fb' listed among available attributes? [y/n] y

==== Verifying protocol version ====
BLECLI >> Write following data to characteristic with UUID '0000ff53-0000-1000-8000-00805f9b34fb' :
    >> 56302e31
BLECLI >> Enter data read from characteristic (in hex) :
    << 53554343455353
==== Verified protocol version successfully ====

==== Starting Session ====
BLECLI >> Write following data to characteristic with UUID '0000ff51-0000-1000-8000-00805f9b34fb' :
    >> 10015a25a201220a20ae6d9d5d1029f8c366892252d2d5a0ffa7ce1ee5829312545dd5f2aba057294d
BLECLI >> Enter data read from characteristic (in hex) :
    << 10015a390801aa0134122048008bfc365fad4753dc75912e0c764d60749cb26dd609595b6fbc72e12614031a1089733af233c7448e7d7fb7963682c6d8
BLECLI >> Write following data to characteristic with UUID '0000ff51-0000-1000-8000-00805f9b34fb' :
    >> 10015a270802b2012212204051088dc294fe4621fac934a8ea22e948fcc3e8ac458aac088ce705c65dbfb9
BLECLI >> Enter data read from characteristic (in hex) :
    << 10015a270803ba01221a20c8d38059d5206a3d92642973ac6ba8ac2f6ecf2b7a3632964eb35a0f20133adb
==== Session Established ====

==== Sending Wifi credential to esp32 ====
BLECLI >> Write following data to characteristic with UUID '0000ff52-0000-1000-8000-00805f9b34fb' :
    >> 98471ac4019a46765c28d87df8c8ae71c1ae6cfe0bc9c615bc6d2c
BLECLI >> Enter data read from characteristic (in hex) :
    << 3271f39a
==== Wifi Credentials sent successfully ====

==== Applying config to esp32 ====
BLECLI >> Write following data to characteristic with UUID '0000ff52-0000-1000-8000-00805f9b34fb' :
    >> 5355
BLECLI >> Enter data read from characteristic (in hex) :
    << 1664db24
==== Apply config sent successfully ====

==== Wifi connection state  ====
BLECLI >> Write following data to characteristic with UUID '0000ff52-0000-1000-8000-00805f9b34fb' :
    >> 290d
BLECLI >> Enter data read from characteristic (in hex) :
    << 505f72a9f8521025c1964d7789c4d7edc56aedebd144e1b667bc7c0975757b80cc091aa9f3e95b06eaefbc30290fa1
++++ WiFi state: connected ++++
==== Provisioning was successful ====
