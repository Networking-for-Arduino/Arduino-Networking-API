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
|[Mbed core][106] | 2018 | LwIP   | Ethernet/WiFi (any hw supported by Mbed OS) | Arduino
|[C33 core][108] | 2023 | LwIP   | Ethernet (Eth peripheral), WiFi STA and SoftAP (on board ESP32C3) | Arduino
|[ESP8266 core][110] | 2014 | LwIP   | WiFi STA and SoftAP, W5500, ENC28J60, PPP  | community
|[ESP32 core][112] | 2016 | LwIP   | WiFi STA and SoftAP, Ethernet (Eth peripheral)  | Espressif
|[WiFiEspAT][114] | 2019 | in fw  | WiFi STA and SoftAP (ESP8266 or ESP32 with AT fw) | Juraj A
|[EthernetENC][115] | 2013 | uIP    | Ethernet (ENC28J60) | Juraj A
|[STM32Ethernet][116] | 2017 | LwIP   | Erhernet (STM32 Eth peripheral) | STM
|[QNEthernet][117]  | 2021 | LwIP   | Ethernet (Teensy 4.1 Eth peripheral) | Shawn S

(1) The Arduino WiFi library is obsolete. It is included in the research only because it was the first WiFi library.

In following tables * marks the official Arduino library with the first introduction of the API function(s). 


## Ethernet/WiFi object

### Module control
| library | *hw setup* | *detection with* | [firmwareVersion](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.firmwareversion/) | [end](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.end/) | *power save* |
|---|:---:|:---:|:---:|:---:|:---:| 
|[Ethernet][1] |init(CS) | [hardwareStatus](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.hardwarestatus/) | | | |
|[WiFi][2] | |status* (1)| | | |
|[WiFi101][4] |setPins |status| ✓* | ✓* | lowPowerMode| 
|[WiFiNINA][3] | |status| ✓ | ✓ | lowPowerMode| 
|[WiFiS3][5] | |status| ✓ | ✓ | | 
|[Mbed WiFi][6] | |status| ✓ | ✓ | lowPowerMode
|[Mbed Ethernet][7] |init(CS) | hardwareStatus | | ✓ | |
|[C33 Wifi][8] | |status| ✓ | ✓ | lowPowerMode
|[C33 Ethernet][9] | | hardwareStatus | |  | |
|[esp8266 WiFi][10] | | status|||sleepMode
|[esp8266 Ethernet][11] |constructor | status|| ✓ |
|[esp32 WiFi][12] | | status|||sleep |
|[esp32 Ethernet][13] | | | | | |
|[WiFiEspAT][14] |init(Stream&) | status | ✓ | | sleepMode |
|[EthernetENC][15] |init(CS) | hardwareStatus | | | |
|[STM32Ethernet][16]| |  | |  | |
|[QNEthernet][17]| | hardwareStatus | | ✓ | |

(1) status is for WiFi station state, but returns WL_NO_SHIELD/WL_NO_MODULE if the hardware is not detected

### Ethernet network interface

| library | [linkStatus](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.linkstatus/) | [begin](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.begin/) *DHCP* | *begin static IP* | auto dns, gw, mask | [maintain](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.maintain/) |
|---|:---:|:---:|:---:|:---:|:---:| 
|[Ethernet][1] * |✓| ✓ | ✓ | ✓ | ✓ |
|[Mbed Ethernet][7] |✓| ✓ | ✓ |✓| ✓ |
|[C33 Ethernet][9] | ✓ | ✓ | ✓ | ✓ | ✓ |
|[esp8266 Ethernet][11] |✓ | ✓ | config | | |
|[esp32 Ethernet][13] |linkUp | ✓ | config | | |
|[EthernetENC][15] |✓| ✓ | ✓ | ✓ | ✓ |
|[STM32Ethernet][16]|✓ | ✓ | ✓| ✓ | ✓ |
|[QNEthernet][17]| ✓ | ✓ | ✓| ✓ | |

### WiFi station network interface

| library |[status](https://www.arduino.cc/reference/en/libraries/wifi/wifi.status/) | [begin](https://www.arduino.cc/reference/en/libraries/wifi/wifi.begin/) | [beginEnterprise](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.beginenterprise/) | [config](https://www.arduino.cc/reference/en/libraries/wifi/wifi.config/) | auto dns, gw, mask| [disconnect](https://www.arduino.cc/reference/en/libraries/wifi/wifi.disconnect/) | 
|---|:---:|:---:|:---:|:---:|:---:|:---:|
|[WiFi][2] * |✓|✓| | ✓ | ? |✓|
|[WiFi101][4] | ✓ |✓| |✓| [PR](https://github.com/arduino-libraries/WiFi101/pull/326)|✓|
|[WiFiNINA][3] |✓|✓|✓* |✓|[PR](https://github.com/arduino-libraries/WiFiNINA/pull/219)|✓|
|[WiFiS3][5] | ✓ |✓| |✓| ✓ |✓|
|[Mbed WiFi][6] | ✓ |✓| | ✓ |? |✓|
|[C33 Wifi][8] | ✓ |✓| |✓| ?|✓|
|[esp8266 WiFi][18] |✓|not blocking| |✓| |✓|
|[esp32 WiFi][19] | ✓ |not blocking| | wrong param. order| |✓|
|[WiFiEspAT][14] |✓|✓| ✓ |✓|✓|✓|

### Network interface getters and setters

All libraries have [localIP()](https://www.arduino.cc/reference/en/libraries/ethernet/ethernet.localip/), gatewayIP() and subnetMask() getters.

| library | [setHostname](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.sethostname/) | [setDNS](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.setdns/) | dnsIP | [macAddress](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.macaddress/)
|---|:---:|:---:|:---:|:---:|
|[Ethernet][1] | [PR](https://github.com/arduino-libraries/Ethernet/pull/223) or [PR](https://github.com/arduino-libraries/Ethernet/pull/233) | setDnsServerIP [PR](https://github.com/arduino-libraries/Ethernet/pull/231) | dnsServerIP PR | MACAddress PR
|[WiFi][2] | | ✓* | | ✓*
|[WiFi101][4] | hostname [PR](https://github.com/arduino-libraries/WiFi101/pull/337) |  | [PR](https://github.com/arduino-libraries/WiFi101/pull/344)| ✓|
|[WiFiNINA][3] | ✓*| ✓ | [PR](https://github.com/arduino-libraries/WiFiNINA/pull/251) | ✓
|[WiFiS3][5] | ✓| ✓ | ✓* | ✓
|[Mbed WiFi][6] |✓| ✓ | dnsServerIP [issue](https://github.com/arduino/ArduinoCore-mbed/issues/732) | ✓ |
|[Mbed Ethernet][7] | ✓ | ✓ | dnsServerIP | ✓|
|[C33 Wifi][8] | ✓ | ✓ | [issue](https://github.com/arduino/ArduinoCore-renesas/issues/155) | ✓
|[C33 Ethernet][9] | | ✓ | dnsServerIP | MACAddress|
|[esp8266 WiFi][18] | ✓ | | ✓ | ✓ |
|[esp8266 Ethernet][11] | ✓ |
|[esp32 WiFi][19] | ✓ | | ✓ | ✓ |
|[esp32 Ethernet][13] |✓ | | ✓ | ✓|
|[WiFiEspAT][14] | ✓| ✓ | ✓ | ✓ |
|[EthernetENC][15] | ✓ | ✓ | ✓ |✓ |
|[STM32Ethernet][16]| | | dnsServerIP | MACAddress
|[QNEthernet][17]| ✓ | setDnsServerIP | dnsServerIP | ✓ |

### WiFi station getters

| library | SSID | [BSSID](https://www.arduino.cc/reference/en/libraries/wifi/wifi.bssid/) | [encryptionType](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.encryptiontype/) | channel | RSSI | <del>reasonCode</del> |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
|[WiFi][2] * |✓ | ✓ |✓ | |✓ | |
|[WiFi101][4] | ✓ | ✓ |✓ |✓ |✓ | |
|[WiFiNINA][3] | ✓ | ✓ |✓ | |✓ | ✓* |
|[WiFiS3][5] | ✓ | ✓ |✓ | |✓ | returns 0 |
|[Mbed WiFi][6] | ✓ | ✓ |✓ | |✓ | |
|[C33 Wifi][8] | ✓ | ✓ |✓ | |✓ | returns 0 |
|[esp8266 WiFi][18] |✓ |without param [PR](https://github.com/esp8266/Arduino/pull/9008) | | |✓ | |
|[esp32 WiFi][19] |✓ | without param| | |✓ | |
|[WiFiEspAT][14] |✓ |✓ | ||✓ | |

### WiFi AP network interface

For libraries which can only run one WiFi interface, AP ends with `begin` for station.

| library | [beginAP](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.beginap/) | end | configure | 
|---|:---:|:---:|:---:|
|[WiFi][2] |✗ | |
|[WiFi101][4] | ✓* |
|[WiFiNINA][3] | ✓ | 
|[WiFiS3][5] | ✓ |
|[Mbed WiFi][6] |✓ |
|[C33 Wifi][8] |✓ |
|[esp8266 WiFi][31] |softAP | softAPdisconnect | softAPConfig | 
|[esp32 WiFi][33] |softAP |softAPdisconnect | softAPConfig | 
|[WiFiEspAT][14] |✓ | endAP | configureAP | 

### WiFi AP getters

For libraries which can only run one WiFi interface, after `beginAP` standard getters read the AP information.

| library | MAC | SSID | Pass | encryption | ip | gw | mask |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|[esp8266 WiFi][31] | softAPmacAddress | softAPSSID | softAPPSK | | softAPIP |
|[esp32 WiFi][33] | softAPmacAddress | softAPSSID | softAPPSK | | softAPIP |
|[WiFiEspAT][14] | apMacAddress | apSSID | apPassphrase | apEncryptionType | apIP | apGatewayIP | apSubnetMask |

### WiFi station networks scan

All researched libraries have method [`scanNetworks()`](https://www.arduino.cc/reference/en/libraries/wifi/wifi.scannetworks/) defined by the first WiFi library.

To get the result of networks scan, all libraries have methods defined by the first WiFi library: `SSID(n)`, `encryptionType(n)` and `RSSI(n)`. Additionally all libraries except of the old WiFi library have `channel(n)`.

All libraries except of the old WiFi library have [BSSID](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.bssid/), but the esp8266 and the esp32 WiFi library have `BSSID(n)` without the parameter for the user provided array. ([PR for esp8266](https://github.com/esp8266/Arduino/pull/9008))

### Network services

The Ethernet/WiFi objects have some common names for simple services. (Libraries may have these services in other classes or they may be available as separate libraries.)

| library | [hostByName](https://www.arduino.cc/reference/en/libraries/wifinina/wifi.hostbyname/) | ping | getTime |
|---|:---:|:---:|:---:|
|[Ethernet][1] | [PR](https://github.com/arduino-libraries/Ethernet/pull/232) | | |
|[WiFi][2] | ✓* | | |
|[WiFi101][4] | ✓ |✓* |✓* |
|[WiFiNINA][3] | ✓ |✓ |✓ |
|[WiFiS3][5] | ✓ | |returns 0 |
|[Mbed WiFi][6] | ✓ |✓ |✓ |
|[Mbed Ethernet][7] | ✓ |✓ |✓ |
|[C33 Wifi][8] | ✓ | |✓ |
|[C33 Ethernet][9] | | | |
|[esp8266 WiFi][10] | ✓ | | |
|[esp8266 Ethernet][11] | | | |
|[esp32 WiFi][12] | ✓ | | |
|[esp32 Ethernet][13] | | | |
|[WiFiEspAT][14] | ✓ | ✓ | ✓ |
|[EthernetENC][15] | ✓ | | |
|[STM32Ethernet][16]| | | |
|[QNEthernet][17] | | | |

## Client class

### Inherited methods

All Client classes in the researched libraries implement the Arduino `Client` class which forces the essential methods.

### Method connected()

The method `connected` should return true if the Client has data available to read even if the underlying TCP connection is closed. Some libraries return false if the TCP connection is closed even if there are still data to read.

Libraries with alternative implementation of `connected` are: 

* Arduino Mbed core SocketWrapper ([issue](https://github.com/arduino/ArduinoCore-mbed/issues/729))
* ESP8266WiFi ([issue](https://github.com/esp8266/Arduino/issues/6701))
* ESP32 WiFi

### Method flush()

Some libraries still implement `flush` as discard input. 

Libraries with wrong implementation of `flush` are:

* ESP32 WiFi ([issue](https://github.com/espressif/arduino-esp32/issues/943))
 

### Client getters and setters

All libraries have [remoteIP()](https://www.arduino.cc/reference/en/libraries/wifinina/client.remoteip/) and remotePort() with exception of the old WiFi library. A few libraries have localIP() to identify the network interface used for the Client.

| library | class | localIP | localPort | [status](https://www.arduino.cc/reference/en/libraries/wifinina/client.status/) | [setConnectionTimeout](https://www.arduino.cc/reference/en/libraries/ethernet/client.setconnectiontimeout/) |
|---|---|:---:|:---:|:---:|:---:|
|Ethernet |[EthernetClient][21] | | | ✓* | ✓ *2018 |
|WiFi | [WiFiClient][22] | |  | ✓ | |
|WiFi101 |[WiFiClient][24] |  | | returns 0 [PR](https://github.com/arduino-libraries/WiFi101/pull/348) | [PR](https://github.com/arduino-libraries/WiFi101/pull/329) |
|WiFiNINA | [WiFiClient][23] |  | | ✓ | [PR](https://github.com/arduino-libraries/WiFiNINA/pull/252) |
|WiFiS3 |[WiFiClient][25] | | | ✓ |[PR](https://github.com/arduino/ArduinoCore-renesas/pull/148) |
Mbed SocketWrapper | [MbedClent][26] | | | ✓ | setTimeout !!! [issue](https://github.com/arduino/ArduinoCore-mbed/issues/728) |
C33 lwIpWrapper |[lwipClient][28] | | | ✓ | ✓ |
ESP8266WiFi |[WiFiClient][30] |  ✓ | ✓ | ✓ | Stream's setTimeout [discussion](https://github.com/esp8266/Arduino/discussions/9004) |
|esp32 WiFi | [WiFiClient][32] | ✓ | ✓ | | setTimeout(seconds) !!! [issue](https://github.com/espressif/arduino-esp32/issues/5558) | 
|WiFiEspAT|[WiFiClient][34] |  | ✓ | ✓ | |
|EthernetENC |[EthernetClient][35] |  | | ✓ | ✓ |
|STM32Ethernet| [EthernetClient][36] | | | ✓ | ✓ |
|QNEthernet |[EthernetClient][37] | ✓ | ✓ | ✓ | ✓ |
 

## Server class

### Class

All Server classes have a constructor with parameter `port`. ESP8266 and ESP32 WiFiServer have additional constructor with IP to identify the network interface on which the server should listen.

Arduino core has `Server` class which inherits from the `Print` class. The idea is to use the methods of `Print` to output to all clients at once.

| library | class | inherits from Server | ctor without params | print to all clients |
---|--- |:---:|:---:|:---:|
|Ethernet |[EthernetServer][41] |✓| PR | ✓ |
|WiFi | [WiFiServer][42] |✓| | ✓ |
|WiFi101 |[WiFiServer][44] | ✓ | PR | ✓ |
|WiFiNINA | [WiFiServer][43] |✓| PR | ✓ |
|WiFiS3 |[WiFiServer][45] |✓| ✓* | ✓ |
|Mbed SocketWrapper | [MbedServer][46] |✓| | ✓ |
|C33 lwIpWrapper |[lwipServer][48] |✓| | ✓ |
|ESP8266WiFi |[WiFiServer][50] || | [ArduinoWiFiServer](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ArduinoWiFiServer.h) |
|esp32 WiFi | [WiFiServer][52] | ✓ | ✓ ||✗ |
|WiFiEspAT|[WiFiServer][54] | | ✓| WiFiServerPrint |
|EthernetENC |[EthernetServer][55] || ✓ |  EthernetServerPrint |
|STM32Ethernet |[EthernetServer][56] |✓| ✓ | ✓ |
|QNEthernet |[EthernetServer][57] |✓| ✓ | ✓ |

### Methods

All Server classes have method [`begin`](https://www.arduino.cc/reference/en/libraries/ethernet/server.begin/) without parameters.

| library | class | begin(port) | end | [op bool](https://www.arduino.cc/reference/en/libraries/ethernet/ifserver/) | <del>status</del> | [available](https://www.arduino.cc/reference/en/libraries/ethernet/server.available/) | [accept](https://www.arduino.cc/reference/en/libraries/ethernet/server.accept/) | 
|---|--- |:---:|:---:|:---:|:---:|:---:|:---:|
|Ethernet |[EthernetServer][41] | PR->| [PR](https://github.com/arduino-libraries/Ethernet/pull/235)  | ✓ *2018 [issue](https://github.com/arduino-libraries/Ethernet/issues/236) | |✓* |✓ *2018 |
|WiFi | [WiFiServer][42] |  | | | ✓* |✓ | |
|WiFi101 |[WiFiServer][44] | [PR](https://github.com/arduino-libraries/WiFi101/pull/346) | [PR](https://github.com/arduino-libraries/WiFi101/pull/347) |  [PR](https://github.com/arduino-libraries/WiFi101/pull/345) | returns 0 [PR](https://github.com/arduino-libraries/WiFi101/pull/348) |✓ |[PR](https://github.com/arduino-libraries/WiFi101/pull/320) |
|WiFiNINA | [WiFiServer][43] |PR-> | [PR](https://github.com/arduino-libraries/WiFiNINA/pull/254) |[PR](https://github.com/arduino-libraries/WiFiNINA/pull/253) | ✓ |✓ | [PR](https://github.com/arduino-libraries/WiFiNINA/pull/204)|
|WiFiS3 |[WiFiServer][45] |✓*| ✓* | [PR](https://github.com/arduino/ArduinoCore-renesas/pull/140) | |✓ | [PR](https://github.com/arduino/ArduinoCore-renesas/pull/144)|
|Mbed SocketWrapper | [MbedServer][46] | | [PR](https://github.com/arduino/ArduinoCore-mbed/pull/751) | | returns 0 [issue](https://github.com/arduino/ArduinoCore-mbed/issues/730) | (1) | [PR](https://github.com/arduino/ArduinoCore-mbed/pull/750)
|C33 lwIpWrapper |[lwipServer][48] | | | | |✓ | |
|ESP8266WiFi |[WiFiServer][50] |✓ | stop() -> | [PR](https://github.com/esp8266/Arduino/pull/8995) | ✓ | (1)(2) | ✓ | 
|esp32 WiFi | [WiFiServer][52] |✓ |✓ | ✓ | ✓ | ✗(1) | ✓ |
|WiFiEspAT|[WiFiServer][54] | ✓ |✓ |✓ | ✓ |✓ |✓ |
|EthernetENC |[EthernetServer][55] |✓ | ✓ |✓ | |✓ | ✓ |
|STM32Ethernet |[EthernetServer][56] |✓ | -> | [issue](https://github.com/stm32duino/STM32Ethernet/issues/73) | | ✓ |✓ | 
|QNEthernet |[EthernetServer][57] | ✓ | ✓ | ✓ | | ✓ |✓ | 

1) the method called `available` in WiFiServer works like `accept` method 
2) available() with [ArduinoWiFiServer](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ArduinoWiFiServer.h)

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
|Mbed SocketWrapper | [MbedUDP][66] | 
|C33 lwIpWrapper |[lwipUDP][68] |
|ESP8266WiFi |[WiFiUDP][70] | 
|esp32 WiFi | [WiFiUDP][72] | 
|WiFiEspAT|[WiFiUDP][74] | 
|EthernetENC |[EthernetUDP][75] | 
|STM32Ethernet |[EthernetUDP][76] | 
|QNEthernet |[EthernetUDP][77] | 

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
[17]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernet.h#L62

[18]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ESP8266WiFiSTA.h#L33
[19]: https://github.com/arduino/arduino-esp32/blob/master/libraries/WiFi/src/WiFiSTA.h#L39

[21]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L214
[22]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFiClient.h
[23]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiClient.h
[24]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiClient.h
[25]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiClient.h
[26]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedClient.h
[28]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/lwIpWrapper/src/lwipClient.h
[30]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiClient.h
[32]: https://github.com/espressif/arduino-esp32/blob/master/libraries/WiFi/src/WiFiClient.h
[34]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiClient.h
[35]: https://github.com/JAndrassy/EthernetENC/blob/master/src/EthernetClient.h
[36]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/EthernetClient.h
[37]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernetClient.h

[31]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/ESP8266WiFiAP.h
[33]: https://github.com/arduino/arduino-esp32/blob/master/libraries/WiFi/src/WiFiAP.h#L31

[41]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L254
[42]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFiServer.h
[43]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiServer.h
[44]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiServer.h
[45]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiServer.h
[46]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedServer.h
[48]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/lwIpWrapper/src/lwipServer.h
[50]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiServer.h
[52]: https://github.com/espressif/arduino-esp32/blob/master/libraries/WiFi/src/WiFiServer.h
[54]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiServer.h
[55]: https://github.com/JAndrassy/EthernetENC/blob/master/src/EthernetServer.h
[56]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/EthernetServer.h
[57]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernetServer.h

[61]: https://github.com/arduino-libraries/Ethernet/blob/master/src/Ethernet.h#L152
[62]: https://github.com/arduino-libraries/WiFi/blob/master/src/WiFiUdp.h
[63]: https://github.com/arduino-libraries/WiFiNINA/blob/master/src/WiFiUdp.h
[64]: https://github.com/arduino-libraries/WiFi101/blob/master/src/WiFiUdp.h
[65]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/WiFiS3/src/WiFiUdp.h
[66]: https://github.com/arduino/ArduinoCore-mbed/blob/main/libraries/SocketWrapper/src/MbedUdp.h
[68]: https://github.com/arduino/ArduinoCore-renesas/blob/main/libraries/lwIpWrapper/src/lwipUDP.h
[70]: https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WiFi/src/WiFiUdp.h
[72]: https://github.com/espressif/arduino-esp32/blob/master/libraries/WiFi/src/WiFiUdp.h
[74]: https://github.com/JAndrassy/WiFiEspAT/blob/master/src/WiFiUdp.h
[75]: https://github.com/JAndrassy/EthernetENC/blob/master/src/EthernetUdp.h
[76]: https://github.com/stm32duino/STM32Ethernet/blob/main/src/EthernetUdp.h#L47
[77]: https://github.com/ssilverman/QNEthernet/blob/master/src/QNEthernetUDP.h

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

