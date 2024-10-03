# Arduino networking API implementations

## Contents

* [Overview](#overview)
* [Ethernet/WiFi object](#ethernetwifi-object)
* [Client class](#client-class)
* [Server class](#server-class)
* [UDP class](#udp-class)

## Overview

|library       | year | TCP/IP | network interfaces | maintainer |
|---|---|---|---|---|
|[Ethernet][101]| 2008 | in fw  | Ethernet (Wiznet W5x00) | Arduino
|[WiFi][102] (1)| 2011 | in fw  | WiFi STA (AT32UC3 with wifiHD fw) | Arduino
|[WiFi101][104] | 2015 | in fw  | WiFi STA and local AP (ATWINC1500) | Arduino
|[WiFiNINA][103]| 2018 | in fw  | WiFi STA and SoftAP (ESP32 with nina-fw) | Arduino
|[WiFiS3][105] | 2023 | in fw  | WiFi STA and SoftAP (ESP32S3 with Uno R4 WiFi fw) |Arduino
|[Mbed core][106] | 2018 | LwIP   | Ethernet/WiFi (any HW with Mbed EMAC driver) | Arduino
|[C33 core][108] | 2023 | LwIP   | Ethernet (Eth peripheral), WiFi STA and SoftAP (ESPHost) | Arduino
|[ESP8266 core][110] | 2014 | LwIP   | WiFi STA and SoftAP, Ethernet (W5500, W5100, ENC28J60), PPP  | community
|[ESP32 core][112] | 2016 | LwIP   | WiFi STA and SoftAP, Ethernet (Ethernet peripheral, W5500, ENC28J60, ...)  | Espressif
|[WiFiEspAT][114] | 2019 | in fw  | WiFi STA and SoftAP (ESP8266 or ESP32 with AT fw) | Juraj A
|[EthernetENC][115] | 2013 | uIP    | Ethernet (ENC28J60) | Juraj A
|[STM32Ethernet][116] | 2017 | LwIP   | Ethernet (STM32 Eth peripheral) | STM
|[QNEthernet][117]  | 2021 | LwIP   | Ethernet (Teensy 4.1 Eth peripheral, W5500) | Shawn S
|[RP2040 Core][118] | 2022 | LwIP | WiFi STA and SoftAP (Pico W, ESPHost, ATWINC1500), Ethernet (W5500, W5100, ENC28J60) | Earle P |

(1) The Arduino WiFi library is obsolete. It is included in the research only because it was the first WiFi library.

Legend for the tables in this document:

* `✓` library has the function
* `*` the official Arduino library with the first introduction of the API function(s). 
* `+` the function was added in API unification effort after the research
* `✗` library will not support the function

## Ethernet/WiFi object

### Module control
| library | *hw setup* | *detection with* | [firmwareVersion](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.firmwareversion/) | [end](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.end/) | *power save* |
|---|:---:|:---:|:---:|:---:|:---:| 
|[Ethernet][1] |init(CS) | [hardwareStatus](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.hardwarestatus/) | | | |
|[WiFi][2] | |status* (1)| | | |
|[WiFi101][4] |setPins |status| ✓* | ✓* | lowPowerMode| 
|[WiFiNINA][3] | defines |status| ✓ | ✓ | lowPowerMode| 
|[WiFiS3][5] | |status| ✓ | ✓ | | 
|[Mbed WiFi][6] | |status| ✓ | ✓ | lowPowerMode
|[Mbed Ethernet][7] |init(CS) | hardwareStatus | | ✓ | |
|[C33 Wifi][8] | |status| returns a constant | ✓ | lowPowerMode
|[C33 Ethernet][9] | | hardwareStatus | |  | |
|[esp8266 WiFi][10] | | status|||sleepMode
|[esp8266 LwipIntfDev][11] (2) |constructor | hardwareStatus (3), status | | ✓ |
|[esp32 WiFi][12] | | status|||sleep |
|[esp32 Ethernet][13] | defines | | | ✓ | |
|[WiFiEspAT][14] |init(Stream&) | status | ✓ | | sleepMode |
|[EthernetENC][15] |init(CS) | hardwareStatus | | + | |
|[STM32Ethernet][16]| |  | |  | |
|[QNEthernet][17]| | hardwareStatus | | ✓ | |
|[RP2040 WiFi][18] | | status | | | lowPowerMode |
|[RP2040 LwipIntfDev][19] | constructor | hardwareStatus (3), status | | ✓ | |

(1) status() is for the WiFi state, but returns `WL_NO_SHIELD`/`WL_NO_MODULE` if the hardware is not detected

(2) LwipIntfDev is the common class for (wired) network interface libraries

(3) with EthernetCompat from lwIP_Ethernet library

### Ethernet network interface

| library | [linkStatus](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.linkstatus/) | [begin](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.begin/) *DHCP* | *begin static IP* | config (1) | auto dns, gw, mask | [maintain](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.maintain/) |
|---|:---:|:---:|:---:|:---:|:---:|:---:| 
|[Ethernet][1] * | ✓ | ✓ | ✓ | |  ✓ | ✓ |
|[Mbed Ethernet][7] | ✓ | ✓ | ✓ | | ✓ | ✓ |
|[C33 Ethernet][9] | ✓ | ✓ | ✓ | | ✓ | ✓ |
|[esp8266 EthernetCompat][211] | ✓ | ✓ | ✓ |  | + | ✓ |
|[esp8266 LwipIntfDev][11] | ✓ | not blocking | ✗ | ✓ | + | |
|[esp32 Ethernet][13] (2) | | not blocking | ✗ | wrong param. order | | |
|[EthernetENC][15] | ✓ | ✓ | ✓ | | ✓ | ✓ |
|[STM32Ethernet][16]| ✓ | ✓ | ✓ | | ✓ | ✓ |
|[QNEthernet][17]| ✓ | ✓ | ✓ | | ✓ | ✓ |
|[RP2040 EthernetCompat][213] | + | + | + |  | + | + |
|[RP2040 LwipIntfDev][19] | + |  not blocking | ✗ | ✓ | + | |

(1) Modern Ethernet libraries use `config`for static IP

(2) EthernetESP32 library has standard Ethernet API for esp32 platform's LwIP networking

### WiFi station network interface

| library |[status](https://www.arduino.cc/reference/en/libraries/wifi/wifi.status/) | [begin](https://www.arduino.cc/reference/en/libraries/wifi/wifi.begin/) | [beginEnterprise](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.beginenterprise/) | [config](https://www.arduino.cc/reference/en/libraries/wifi/wifi.config/) | auto dns, gw, mask| [disconnect](https://www.arduino.cc/reference/en/libraries/wifi/wifi.disconnect/) | 
|---|:---:|:---:|:---:|:---:|:---:|:---:|
|[WiFi][2] * | ✓ | ✓ | | ✓ | | ✓ |
|[WiFi101][4] | ✓ | ✓ | | ✓ | [PR](https://github.com/arduino-libraries/WiFi101/pull/326)| ✓ |
|[WiFiNINA][3] | ✓ | ✓ | ✓* | ✓ |+ v2 <del>[PR](https://github.com/arduino-libraries/WiFiNINA/pull/219)</del>| ✓ |
|[WiFiS3][5] | ✓ | ✓ | | ✓ | ✓ | ✓ |
|[Mbed WiFi][6] | ✓ | ✓ | | ✓ <del>[PR](https://github.com/arduino/ArduinoCore-mbed/pull/816)</del> | + | ✓ |
|[C33 Wifi][8] | ✓ | ✓ | | ✓ | ✓ | ✓ |
|[esp8266 WiFi][210] | ✓ |not blocking | | ✓ | + | ✓ |
|[esp32 WiFi][212] | ✓ |not blocking | | ✓ <del>[PR](https://github.com/espressif/arduino-esp32/pull/9425)</del> | + | ✓ |
|[WiFiEspAT][14] | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
|[RP2040 WiFi][18] | ✓ | ✓ | | ✓ | + | ✓ |

### Network interface getters and setters

All libraries have [localIP()](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.localip/), gatewayIP() and subnetMask() getters. Many Ethernet libraries now have the getters and setters introduced in WiFi libraries.

| library | [setHostname](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.sethostname/) | [setDNS](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.setdns/) | dnsIP(n) | [macAddress](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.macaddress/)
|---|:---:|:---:|:---:|:---:|
|[Ethernet][1]  | [PR](https://github.com/arduino-libraries/Ethernet/pull/233) | [PR](https://github.com/arduino-libraries/Ethernet/pull/231) | PR | PR
|[WiFi][2] | | ✓* |  | ✓* R
|[WiFi101][4] | hostname [PR](https://github.com/arduino-libraries/WiFi101/pull/337) | ✗ | [PR](https://github.com/arduino-libraries/WiFi101/pull/344)| ✓ R|
|[WiFiNINA][3] | ✓*| ✓ | + <del>[PR](https://github.com/arduino-libraries/WiFiNINA/pull/251)</del> | ✓ R
|[WiFiS3][5] | ✓ | ✓ | ✓* | ✓ <del>R [PR](https://github.com/arduino/ArduinoCore-renesas/pull/183)</del>
|[Mbed WiFi][6] |+| ✓ | + | ✓ <del>R</del> |
|[Mbed Ethernet][7]   | + | ✓ | + | ✓ <del>R !!! [issue](https://github.com/arduino/ArduinoCore-mbed/issues/752)</del>|
|[C33 Wifi][8] | ✓ (1) | ✓ | + | ✓ <del>R [PR](https://github.com/arduino/ArduinoCore-renesas/pull/184)</del>
|[C33 Ethernet][9] | [PR](https://github.com/arduino/ArduinoCore-renesas/pull/221) | ✓ | PR  | PR  |
|[esp8266 WiFi][210] | ✓ | + | ✓ | ✓ |
|[esp8266 LwipIntfDev][11]  | ✓ | + | + | +
|[esp32 WiFi][212] | ✓ | + | ✓ | ✓ |
|[esp32 Ethernet][13] | ✓ | (2) | ✓ | ✓ |
|[WiFiEspAT][14] | ✓ | ✓ | + | ✓ <del>R</del> |
|[EthernetENC][15]  | + | + | + | ✓ |
|[STM32Ethernet][16]| |  |  | 
|[QNEthernet][17] | ✓ |  |  | ✓ |
|[RP2040 WiFi][18] | ✓ | ✓ | + | ✓ |
|[RP2040 LwipIntfDev][19] | ✓ | + | + | + |

Flag R is for "reversed". Arduino WiFi libraries copied the bug of the first WiFi library. The bytes of the MAC address are from `macAddress` getter returned in reversed order.

(1) hostname is not send with DHCP request

(2) EthernetESP32 library has standard Ethernet API for esp32 platform's LwIP networking


### Legacy Ethernet getters and setters

| library | setDnsServerIP | dnsServerIP |  MACAddress |
|---|:---:|:---:|:---:|
|[Ethernet][1]  |   ✓ | ✓ | ✓
|[Mbed Ethernet][7]  | ✓ | ✓ | ✓  <del>R [issue](https://github.com/arduino/ArduinoCore-mbed/issues/752)</del> ||
|[C33 Ethernet][9] |  PR | ✓ | + |
|[esp8266 EthernetCompat][211] | + | + | + 
|[EthernetENC][15]  | ✓ | ✓ | ✓
|[STM32Ethernet][16]| + | ✓ |  <del>is a setter! ([issue](https://github.com/stm32duino/STM32Ethernet/issues/81))</del>
|[QNEthernet][17] | ✓ | ✓ | ✓ |
|[RP2040 EthernetCompat][213] | + | + | + |


### WiFi station getters

| library | SSID | [BSSID](https://www.arduino.cc/reference/en/libraries/wifi/wifi.bssid/) | [encryptionType](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.encryptiontype/) | channel | RSSI | <del>reasonCode</del> |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
|[WiFi][2] * | ✓ | ✓ R | ✓ | | ✓ | |
|[WiFi101][4] | ✓ | ✓ R | ✓ |  | ✓ | |
|[WiFiNINA][3] | ✓ | ✓ R | ✓ | | ✓ | ✓* |
|[WiFiS3][5] | ✓ | ✓ | ✓ | | ✓ | returns 0 |
|[Mbed WiFi][6] | ✓ | ✓ | ✓ | | ✓ | |
|[C33 Wifi][8]  | ✓ | ✓ | ✓ | | ✓ | returns 0 |
|[esp8266 WiFi][210] | ✓ | + | [PR](https://github.com/esp8266/Arduino/pull/9114) | ✓ | ✓ | |
|[esp32 WiFi][212] | ✓ | + | | ✓ | ✓ | |
|[WiFiEspAT][14] | ✓ | ✓ <del>R</del> | | + | ✓ | |
|[RP2040 WiFi][18] | ✓ | ✓ | ✓ | ✓ | ✓ | returns `WL_NO_SHIELD` |

### WiFi AP network interface

For libraries which can only run one WiFi interface, AP ends with `begin` for station.

| library | [beginAP](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.beginap/) | end | configure | 
|---|:---:|:---:|:---:|
|[WiFi][2] |✗ | |
|[WiFi101][4] | ✓* |
|[WiFiNINA][3] | ✓ | 
|[WiFiS3][5] | ✓ |
|[Mbed WiFi][6] | ✓ |
|[C33 Wifi][8] | ✓ |
|[esp8266 WiFi][310] |softAP | softAPdisconnect | softAPConfig | 
|[esp32 WiFi][312] |softAP |softAPdisconnect | softAPConfig | 
|[WiFiEspAT][14] | ✓, softAP | endAP, softAPdisconnect | configureAP, softAPConfig| 
|[RP2040 WiFi][18] | ✓, softAP | softAPdisconnect | softAPConfig | 

### WiFi AP getters

For libraries which can only run one WiFi interface, after `beginAP` standard getters read the AP information.

| library | MAC | SSID | Pass | encryption | ip | gw | mask |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|[esp8266 WiFi][310] | softAPmacAddress | softAPSSID | softAPPSK | | softAPIP |
|[esp32 WiFi][312] | softAPmacAddress | softAPSSID | softAPPSK | | softAPIP |
|[WiFiEspAT][14] | apMacAddress, softAPmacAddress | apSSID, softAPSSID | apPassphrase, softAPPSK | apEncryptionType | apIP, softAPIP | apGatewayIP | apSubnetMask |
|[RP2040 WiFi][18] | softAPmacAddress | softAPSSID | | | softAPIP |

### WiFi station networks scan

All researched libraries have methods [`scanNetworks()`](https://www.arduino.cc/reference/en/libraries/wifi/wifi.scannetworks/), `SSID(n)`, `encryptionType(n)` and `RSSI(n)` defined by the first WiFi library. All libraries except of the old WiFi library have `BSSID(n,bssid)` and `channel(n)`defined by the WiFi101 library. WiFi101 and WiFiNINA have the BSSID in reversed ordering.

Encryption type constant names are in esp32 WiFi library very different from the common set used in all other WiFi libraries.

### Network services

The Ethernet/WiFi objects have some functions for simple services. (Libraries may have these services in other classes or they may be available as separate libraries.)

| library | [hostByName](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.hostbyname/) | ping | getTime |
|---|:---:|:---:|:---:|
|[Ethernet][1] | [PR](https://github.com/arduino-libraries/Ethernet/pull/232) | | |
|[WiFi][2] | ✓* | | |
|[WiFi101][4] | ✓ | ✓* | ✓* |
|[WiFiNINA][3] | ✓ | ✓ | ✓ |
|[WiFiS3][5] | ✓ | + | + |
|[Mbed WiFi][6] | ✓ | + | returns 0 |
|[Mbed Ethernet][7] | ✓ | + | returns 0 |
|[C33 Wifi][8] | ✓ | |returns 0 |
|[C33 Ethernet][9] | PR | | |
|[esp8266 WiFi][10] | ✓ | | |
|[esp8266 LwipIntfDev][11] | | | |
|[esp32 WiFi][12] | ✓ | | |
|[esp32 Ethernet][13] | | | |
|[WiFiEspAT][14] | ✓ | ✓ | ✓ |
|[EthernetENC][15] | + | | |
|[STM32Ethernet][16]| | | |
|[QNEthernet][17] | + | | |
|[RP2040 WiFi][18] | ✓ | ✓ | returns millis() !!! |
|[RP2040 LwipIntfDev][19] | ✓ | ✓ | |

## Client class

### Inherited methods

All Client classes in the researched libraries implement the Arduino `Client` class which forces the essential methods.

### Method connected()

The method `connected` should return true if the Client has data available to read even if the underlying TCP connection is closed. Esp8266 and Esp32 libraries return false if the TCP connection is closed even if there are still data to read.

### Client getters and setters

All libraries have [remoteIP()](https://www.arduino.cc/reference/en/libraries/wifinina/client.remoteip/) and remotePort() with exception of the old WiFi library. A few libraries have localIP() to identify the network interface used for the Client.

| library | class | localIP | localPort | [status](https://www.arduino.cc/reference/en/libraries/wifinina/client.status/) | [setConnectionTimeout](https://www.arduino.cc/reference/en/libraries/ethernet/client.setconnectiontimeout/) |
|---|---|:---:|:---:|:---:|:---:|
|Ethernet |[EthernetClient][21] | | | ✓* | ✓ *2018 |
|WiFi | [WiFiClient][22] | |  | ✓ | |
|WiFi101 |[WiFiClient][24] |  | | returns 0 [PR](https://github.com/arduino-libraries/WiFi101/pull/348) | [PR](https://github.com/arduino-libraries/WiFi101/pull/329) |
|WiFiNINA | [WiFiClient][23] |  | | ✓ | + v2 <del>[PR](https://github.com/arduino-libraries/WiFiNINA/pull/252)</del> |
|WiFiS3 |[WiFiClient][25] | | | ✓ | + |
|Mbed SocketWrapper | [MbedClent][26] (1)| | | returns only 1 or 0 | ✗ |
|C33 lwIpWrapper |[lwipClient][28] (1) | | | ✓ | ✓ |
|ESP8266WiFi |[WiFiClient][30] (2) |  ✓ | ✓ | ✓ | Stream's setTimeout [discussion](https://github.com/esp8266/Arduino/discussions/9004) |
|esp32 Network | [NetworkClient][32] (3) | ✓ | ✓ | | + | 
|WiFiEspAT|[WiFiClient][34] |  | ✓ | ✓ | ✗ |
|EthernetENC |[EthernetClient][35] |  | | + | ✓ |
|STM32Ethernet| [EthernetClient][36] | | | ✓ | + |
|QNEthernet |[EthernetClient][37] | + | ✓ | + | ✓ |
|RP2040 WiFi | [WiFiClient][38] (2) | ✓ | ✓ | ✓ | Stream's setTimeout |

(1) common implementation for WiFiClient and EthernetClient in the platform's libraries

(2) WiFiClient is used for Ethernet interface too. EthernetCompat has EthernetClient as alias to WiFiClient.

(3) WiFiClient is alias for NetworkClient. EthernetClient is not defined

### `client.read(buff, size)` return value

There are differences for return value of `client.read(buff, size)`. According to Arduino documentation return -1 is "no data available" and 0 is "connection is closed". And this is how it is in BSD sockets where there is no other way to get the closed state. But most Arduino libraries return 0 if no data are available and some return -1 if the client is not valid.

Note: The Arduino documentation mixes `read()` with `read(buff, size)`. `read()` should definitely return -1 if no byte is available, because 0 is a valid returned value.

| library | class | no data available | connection is closed (1) |
|---|---|:---:|:---:|
|Ethernet |[EthernetClient][21] | -1 | 0 |
|WiFi101 |[WiFiClient][24] | -1 | 0 | 
|WiFiNINA | [WiFiClient][23] | 0 | |
|WiFiS3 |[WiFiClient][25] | 0 | |
|Mbed SocketWrapper | [MbedClent][26]| -1 | |
|C33 lwIpWrapper |[lwipClient][28] | -1 | |
|ESP8266WiFi |[WiFiClient][30] | 0 | |
|esp32 Network | [NetworkClient][32] | 0 | |
|WiFiEspAT|[WiFiClient][34] | 0 | |
|EthernetENC |[EthernetClient][35] | 0 | -1 |
|STM32Ethernet| [EthernetClient][36] | 0 | -1 |
|QNEthernet |[EthernetClient][37] | 0 | 0 |
|RP2040 WiFi | [WiFiClient][38] (2) | 0 | |

(1) empty field means that the state of the connection is not checked in read(buff, size)


### Secure layer

| library | secure client | lastError() | setInsecure | allowSelfSignedCerts | disableSNI |
|---|---|:---:|:---:|:---:|:---:|
|Ethernet | (1)
|WiFi | 
|WiFi101 |[WiFiSSLClient][84] |
|WiFiNINA | [WiFiSSLClient][83] |
|WiFiS3 | [WiFiSSLClient][85] |
|Mbed SocketWrapper | [MbedSSLClient][86] (2) | | | | ✓*
|C33 lwIpWrapper | [SSLClient][88] (2) | ✓* | ✓* 
|ESP8266WiFi | [WiFiClientSecure][90] |  getLastSSLError | ✓ | ✓
|esp32 Network | [NetworkClientSecure][92] | ✓| ✓
|WiFiEspAT| [WiFiSSLClient][94] |
|EthernetENC | (1)
|STM32Ethernet|
|QNEthernet |
|RP2040 WiFi | [WiFiClientSecure][98] | getLastSSLError | ✓ | ✓ | | |

(1) [SSLClient](https://github.com/OPEnSLab-OSU/SSLClient) library can add secure layer

(2) common implementation for `WiFiSSLClient` and `EthernetSSLClient` in the platform's libraries

Certificates for the connection:

| library |  setPreSharedKey | setCACert | setCertificate | setPrivateKey | load (1) |
|---|:---:|:---:|:---:|:---:|:---:|
|Mbed SocketWrapper |  | appendCustomCACert | | |root CA from default block device |
|C33 lwIpWrapper * | ✓ |  ✓ |  ✓ |  ✓ |  ✓ | 
|ESP8266WiFi |  | setTrustAnchors | setClientRSACert | setClientRSACert | setCertStore |
|esp32 WiFi |  ✓ |  ✓ |  ✓ |  ✓ |  ✓ | 
|RP2040 WiFi |  |  ✓, setTrustAnchors |  ✓, setClientRSACert |  ✓, setClientRSACert |  ✓, setCertStore | 

(1) `loadCACert`, `loadCertificate`, `loadPrivateKey` from a Stream, or other way for stored certificates

Additional properties:

| library | secure client | setKnownKey | setFingerprint | setCiphers | setCiphersLessSecure |
|---|---|:---:|:---:|:---:|:---:|
|Mbed SocketWrapper | [MbedSSLClient][86] |
|C33 lwIpWrapper | [SSLClient][88]  | 
|ESP8266WiFi | [WiFiClientSecure][90] |  ✓ | ✓ | ✓ | ✓ |
|esp32 Network | [NetworkClientSecure][92] | 
|RP2040 WiFi | [WiFiClientSecure][98] | ✓ | ✓ | ✓ | ✓ |


## Server class

### Class

All Server classes have a constructor with parameter `port`. ESP8266, ESP32 and RP2040 WiFi library WiFiServer have additional constructor with IP to identify the network interface on which the server should listen.

Arduino core has `Server` class which inherits from the `Print` class. The idea is to use the methods of `Print` to output to all clients at once. The `Server` class itself doesn't define anything useful. 

Modern server class implementations have constructor without parameters, method `begin(port)`, method `accept(),` method `end()` and bool operator. Modern implementations of the server class do not implement `available()` and print-to-all-clients so they don't inherit from `Server`.

| library | class |  ctor without params | print to all clients | modern |
|---|--- |:---:|:---:|:---:|
|Ethernet |[EthernetServer][41] | PR | ✓ |
|WiFi | [WiFiServer][42] | | ✓ |
|WiFi101 |[WiFiServer][44]  | PR | ✓ |
|WiFiNINA | [WiFiServer][43] | + v2 | ✓ |
|WiFiS3 |[WiFiServer][45] | ✓* | ✓ |
|Mbed SocketWrapper | [MbedServer][46] (2) | + | ✗ (1) | +  <del>[PR](https://github.com/arduino/ArduinoCore-mbed/pull/793)</del> |
|C33 lwIpWrapper |[lwipServer][48] (2) | ✓ | ✓ |
|ESP8266WiFi |[WiFiServer][50] (3) | + | [ArduinoWiFiServer](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ArduinoWiFiServer.h) | ✓ | 
|esp32 Network | [NetworkServer][52] (4)  | ✓ | ✗ (1) |✓ |
|WiFiEspAT|[WiFiServer][54] |  + | ✗ (1) |✓ |
|EthernetENC |[EthernetServer][55] | + |  EthernetServerPrint |✓ |
|STM32Ethernet |[EthernetServer][56] | ✓ | ✓ |
|QNEthernet |[EthernetServer][57] | ✓ | ✓ | ✓ |
|RP2040 WiFi|[WiFiServer][58] (3)| + | ✗ (1) |✓ |

(1) ArduinoWiFiServer in NetApiHelpers library has print-to-all-clients and proper server.available() 

(2) common implementation for WiFiServer and EthernetServer in the platform's libraries

(3) WiFiServer is used for Ethernet interface too. EthernetCompat.h has EthernetServer as alias to WiFiServer.

(4) WiFiServer is alias for NetworkServer. EthernetServer is not defined

### Methods

All Server classes have method [`begin`](https://www.arduino.cc/reference/en/libraries/ethernet/server.begin/) without parameters.

| library | class | begin(port) | end | [op bool](https://www.arduino.cc/reference/en/libraries/ethernet/ifserver/) | <del>status</del> | [available](https://www.arduino.cc/reference/en/libraries/ethernet/server.available/) | [accept](https://www.arduino.cc/reference/en/libraries/ethernet/server.accept/) | 
|---|--- |:---:|:---:|:---:|:---:|:---:|:---:|
|Ethernet |[EthernetServer][41] | PR->| [PR](https://github.com/arduino-libraries/Ethernet/pull/235)  | ✓ *2018 [issue](https://github.com/arduino-libraries/Ethernet/issues/236) | | ✓* | ✓ *2018 |
|WiFi | [WiFiServer][42] |  | | | ✓* | ✓ | |
|WiFi101 |[WiFiServer][44] | [PR](https://github.com/arduino-libraries/WiFi101/pull/346) | [PR](https://github.com/arduino-libraries/WiFi101/pull/347) |  [PR](https://github.com/arduino-libraries/WiFi101/pull/345) | returns 0 [PR](https://github.com/arduino-libraries/WiFi101/pull/348) | ✓ |[PR](https://github.com/arduino-libraries/WiFi101/pull/320) |
|WiFiNINA | [WiFiServer][43] |+ v2 | + v2 <del>[PR](https://github.com/arduino-libraries/WiFiNINA/pull/254)</del> | + <del>[PR](https://github.com/arduino-libraries/WiFiNINA/pull/253)</del> | ✓ | ✓ | + <del>[PR](https://github.com/arduino-libraries/WiFiNINA/pull/204)</del>|
|WiFiS3 |[WiFiServer][45] | ✓*| ✓* | + | | ✓ | + |
|Mbed SocketWrapper | [MbedServer][46] | + | + | + | returns 0 [issue](https://github.com/arduino/ArduinoCore-mbed/issues/730) | ✗(1) | +
|C33 lwIpWrapper |[lwipServer][48] | ✓ | | | | ✓ | |
|ESP8266WiFi |[WiFiServer][50] | ✓ | + | + | ✓ | (1)(2) | ✓ | 
|esp32 Network | [NetworkServer][52] | ✓ | ✓ | ✓ | ✓ | ✗(1) | ✓ |
|WiFiEspAT|[WiFiServer][54] | + | ✓ | ✓ | ✓ | ✗(1) | ✓ |
|EthernetENC |[EthernetServer][55] | + | ✓ | ✓ | | ✓ | ✓ |
|STM32Ethernet |[EthernetServer][56] | ✓ | + | + | | ✓ | ✓ | 
|QNEthernet |[EthernetServer][57] | ✓ | ✓ | ✓ | | ✓ | ✓ | 
|RP2040 WiFi|[WiFiServer][58] | ✓ | + | + | ✓ | ✗(1) | ✓ |

(1) the method called `available` in WiFiServer works like `accept` method 

(2) available() with [ArduinoWiFiServer](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ArduinoWiFiServer.h)

## UDP class

### Class

All UDP classes in the researched libraries implement the Arduino UDP class which forces to implement the essential methods.

| library | class | 
|---|--- |
|Ethernet |[EthernetUDP][61] | 
|WiFi | [WiFiUDP][62] |
|WiFi101 |[WiFiUDP][64] |
|WiFiNINA | [WiFiUDP][63] |
|WiFiS3 |[WiFiUDP][65] | 
|Mbed SocketWrapper | [MbedUDP][66] (1) | 
|C33 lwIpWrapper |[lwipUDP][68] (1) |
|ESP8266WiFi |[WiFiUDP][70] | 
|esp32 Network | [NetworkUDP][72] (3) | 
|WiFiEspAT|[WiFiUDP][74] | 
|EthernetENC |[EthernetUDP][75] | 
|STM32Ethernet |[EthernetUDP][76] | 
|QNEthernet |[EthernetUDP][77] | 
|RP2040 WiFi | [WiFiUDP][78] (2) |

(1) common implementation for WiFiUdp and EthernetUdp in the platform's libraries

(3) WiFiUdp is used for Ethernet interface too. EthernetCompat.h has EthernetUdp as alias to WiFiUdp.

(4) WiFiUdp is alias for NetworkUdp. EthernetUdp is not defined

[1]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L74
[2]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFi.h#L34
[3]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFi.h#L42
[4]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFi101.h#L79
[5]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFi.h#L32
[6]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/WiFi/src/WiFi.h#L51
[7]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/Ethernet/src/Ethernet.h#L55
[8]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFi/src/WiFiC3.h#L8
[9]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/Ethernet/src/EthernetC33.h#L31
[10]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ESP8266WiFiGeneric.h#L62
[11]: https://github.com/esp8266/Arduino/blob/master/cores/esp8266/LwipIntfDev.h#L57
[12]: https://github.com/espressif/arduino-esp32/blob/master/libraries/WiFi/src/WiFiGeneric.h#L154
[13]: https://github.com/espressif/arduino-esp32/blob/master/libraries/Ethernet/src/ETH.h#L59
[14]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFi.h#L57
[15]: https://github.com/JAndrassy/EthernetENC/blob/master/src/Ethernet.h#L75
[16]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/STM32Ethernet.h#L16
[17]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernet.h
[18]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/WiFi/src/WiFiClass.h#L41
[19]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/lwIP_Ethernet/src/LwipIntfDev.h#L57

[210]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ESP8266WiFiSTA.h#L33
[211]: https://github.com/esp8266/Arduino/blob/master/libraries/lwIP_Ethernet/src/EthernetCompat.h
[212]: https://github.com/arduino/arduino-esp32/blob/master/libraries/WiFi/src/WiFiSTA.h#L39
[213]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/lwIP_Ethernet/src/EthernetCompat.h
[310]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ESP8266WiFiAP.h
[312]: https://github.com/arduino/arduino-esp32/blob/master/libraries/WiFi/src/WiFiAP.h#L31

[410]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ESP8266WiFiScan.h
[412]: https://github.com/arduino/arduino-esp32/blob/master/libraries/WiFi/src/WiFiScan.h

[21]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L214
[22]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFiClient.h
[23]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiClient.h
[24]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiClient.h
[25]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiClient.h
[26]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedClient.h
[28]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/lwIpWrapper/src/lwipClient.h
[30]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiClient.h
[32]: https://github.com/espressif/arduino-esp32/blob/master/libraries/Network/src/NetworkClient.h#L38
[34]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiClient.h
[35]: https://github.com/JAndrassy/EthernetENC/blob/master/src/EthernetClient.h
[36]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/EthernetClient.h
[37]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernetClient.h
[38]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/WiFi/src/WiFiClient.h

[41]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L254
[42]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFiServer.h
[43]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiServer.h
[44]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiServer.h
[45]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiServer.h
[46]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedServer.h
[48]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/lwIpWrapper/src/lwipServer.h
[50]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiServer.h
[52]: https://github.com/espressif/arduino-esp32/blob/master/libraries/Network/src/NetworkServer.h
[54]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiServer.h
[55]: https://github.com/JAndrassy/EthernetENC/blob/master/src/EthernetServer.h
[56]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/EthernetServer.h
[57]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernetServer.h
[58]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/WiFi/src/WiFiServer.h
 
[61]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L152
[62]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFiUdp.h
[63]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiUdp.h
[64]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiUdp.h
[65]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiUdp.h
[66]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedUdp.h
[68]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/lwIpWrapper/src/lwipUDP.h
[70]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiUdp.h
[72]: https://github.com/espressif/arduino-esp32/blob/master/libraries/Network/src/NetworkUdp.h#L42
[74]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiUdp.h
[75]: https://github.com/JAndrassy/EthernetENC/blob/master/src/EthernetUdp.h
[76]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/EthernetUdp.h#L47
[77]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernetUDP.h
[78]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/WiFi/src/WiFiUdp.h

[83]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiSSLClient.h
[84]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiSSLClient.h
[85]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiSSLClient.h
[86]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedSSLClient.h
[88]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/SSLClient/src/SSLClient.h
[90]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiClientSecureBearSSL.h
[92]: https://github.com/espressif/arduino-esp32/blob/master/libraries/NetworkClientSecure/src/NetworkClientSecure.h
[94]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiSSLClient.h
[98]: https://github.com/earlephilhower/arduino-pico/blob/master/libraries/WiFi/src/WiFiClientSecureBearSSL.h

[101]: https://github.com/arduino-libraries/Ethernet
[102]: https://github.com/arduino-libraries/WiFi
[103]: https://github.com/arduino-libraries/WiFiNINA
[104]: https://github.com/arduino-libraries/WiFi101
[105]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3
[106]: https://github.com/arduino/ArduinoCore-mbed
[108]: https://github.com/arduino/ArduinoCore-renesas
[110]: https://github.com/esp8266/Arduino
[112]: https://github.com/espressif/arduino-esp32
[114]: https://github.com/JAndrassy/WiFiEspAT
[115]: https://github.com/JAndrassy/EthernetENC
[116]: https://github.com/stm32duino/STM32Ethernet
[117]: https://github.com/ssilverman/QNEthernet
[118]: https://github.com/earlephilhower/arduino-pico
