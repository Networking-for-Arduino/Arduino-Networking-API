# Testing compatibility of WiFi and Ethernet object

## WiFi

The WiFi tests are [WiFiTest](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/WiFiTest/WiFiTest.ino) and [WiFiScanNetwork](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/WiFiScanNetworks/WiFiScanNetworks.ino) from the NetApiHelpers library. Tested are functions of the `WiFi` object.

### WiFiNINA

With listed pull requests [applied](https://github.com/JAndrassy/WiFiNINA/tree/all_fixes), only issue is the reversed `macAddress` and `BSSID` for compatibility with the older Arduino WiFi libraries.

pull requests:

* [added dnsIP(n)](https://github.com/arduino-libraries/WiFiNINA/pull/251)
* [auto dns, gw, mask](https://github.com/arduino-libraries/WiFiNINA/pull/219)
* [rejoin with DHCP](https://github.com/arduino/nina-fw/pull/91) (nina-fw PR)

### WiFi101
  
With listed pull requests [applied](https://github.com/JAndrassy/WiFi101/tree/all_fixes), remaining issues are

* Doesn't have `setDNS()`. It is not simple to add it.
* Requires `WiFi.end()` to repeat `begin`. With `WiFi.disconnect()` next `begin` hangs.
* reversed ordering of bytes returned by `macAddress`, `BSSID` and `BSSID(n)` for compatibility with the first Arduino WiFi library

pull requests:

* [added dnsIP(n)](https://github.com/arduino-libraries/WiFi101/pull/344)
* [setHostname alias](https://github.com/arduino-libraries/WiFi101/pull/337)
* [auto dns, gw, mask](https://github.com/arduino-libraries/WiFi101/pull/326)
* [added WiFi.h](https://github.com/arduino-libraries/WiFi101/pull/321)
* [`begin` fix](https://github.com/arduino-libraries/WiFi101/pull/324)

### WiFiS3

All problems discovered with the test were solved.

pull requests:

* <del> [WiFi.h include WiFiClient.h](https://github.com/arduino/ArduinoCore-renesas/pull/224)</del>

* <del>[fix auto dns IP](https://github.com/arduino/ArduinoCore-renesas/pull/173)</del>
* <del>[reversed MAC](https://github.com/arduino/ArduinoCore-renesas/pull/183)</del>


### WiFiEspAT

Issues are solved in version 2.

Issues:

* <del>WiFi.disconnect() clears static IP and config(0) doesn't</del>
* <del>reversed macAddress and BSSID for compatibility with Arduino WiFi libraries.</del>


## Ethernet

The test is [LegacyEthernetTest](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/LegacyEthernetTest/LegacyEthernetTest.ino) from the NetApiHelpers library. Tested are functions of the `Ethernet` object.

### Ethernet

With listed pull requests applied, remaining minor issues is that it doesn't have `Ethernet.end()`

pull requests:

* [setHostname](https://github.com/arduino-libraries/Ethernet/pull/233)
* [hostByName](https://github.com/arduino-libraries/Ethernet/pull/232)


### EthernetENC

All problems discovered with the test were solved.

### STM32Ethernet

Issues:

* <del>`MACAddress` is a setter ! ([issue](https://github.com/stm32duino/STM32Ethernet/issues/81))</del>

Minor issues:

* doesn't have `hardwareStatus`
* doesn't have `setHostname`
* doesn't have `end()`
* doesn't have `hostByName`
* doesn't have `setDNS`, `dnsIP(n)`, `macAddress` 
* doesn't have Ethernet.h

pull requests:

* <del>doesn't have `setDnsServerIP` [PR](https://github.com/stm32duino/STM32Ethernet/pull/80)</del>


### QNEthernet

Minor issues:

* doesn't have Ethernet.h
* doesn't have `end()`
* doesn't have `setDNS`, `dnsIP(n)`


## LwIP WiFi + Ethernet

The WiFi tests are [WiFiTest](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/WiFiTest/WiFiTest.ino) and [WiFiScanNetwork](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/WiFiScanNetworks/WiFiScanNetworks.ino). 
The Ethernet tests are [LegacyEthernetTest](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/LegacyEthernetTest/LegacyEthernetTest.ino) and [ModernEthernetTest](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/ModernEthernetTest/ModernEthernetTest.ino).
Tested are functions of the `WiFi` and `Ethernet` object.

### ESP8266 WiFi

Differences:

* `begin` is async. `status = WiFi.waitForConnectResult();` can be used to wait

pull requests:

* <del> [method setDNS](https://github.com/esp8266/Arduino/pull/9021)</del>
* <del>[BSSID with parameter](https://github.com/esp8266/Arduino/pull/9008)</del>
* <del>doesn't have auto configuration for DNS, gw, mask for static IP ([PR](https://github.com/esp8266/Arduino/pull/9031))</dek>
* <del>doesn't have WiFi.h [PR](https://github.com/esp8266/Arduino/pull/9044)</del>


### ESP8266 Ethernet

LwipIntfDev implements the modern Ethernet API draft. EthernetCompat wraps LwipIntfDev for legacy Ethernet API.

Minor issues:

* doesn't have `hostByName`

pull requests:

* <del>[end() and re-begin](https://github.com/esp8266/Arduino/pull/9023)</del>
* <del>[DNS IP getters and setters](https://github.com/esp8266/Arduino/pull/9022)</del>
* <del>doesn't have auto configuration for DNS, gw, mask for static IP ([PR](https://github.com/esp8266/Arduino/pull/9040) and [PR](https://github.com/esp8266/Arduino/pull/9024)) </del>


### ESP32 WiFi

Differences:

* `begin` is async. `status = WiFi.waitForConnectResult();` can be used to wait
* ScanNetworks - encryptionType enum has different constants than other WiFi libraries 

pull requests:

* <del> [method setDNS](https://github.com/espressif/arduino-esp32/pull/8854)</del>
* <del>[BSSID with parameter](https://github.com/espressif/arduino-esp32/pull/8853)</del>
* <del>`config` has wrong ordering of parameters and doesn't have auto configuration for DNS, gateway, net mask for static IP ([PR](https://github.com/espressif/arduino-esp32/pull/8892))</del>
* <del>`disconnect` is asynchronous. Without waiting for status change after it, it can interfere with immediately following `begin` ([PR](https://github.com/espressif/arduino-esp32/pull/9062))</del>


### ESP32 Ethernet

Implements the modern Ethernet API draft.

Issues:

* static IP is not applied with `config` invoked before `begin`([issue](https://github.com/espressif/arduino-esp32/issues/9673))
* doesn't have `linkStatus`,`status`, `hostByName`, `setDNS`
* `config` has wrong ordering of parameters, no default auto values 
* doesn't return to DHCP with `config(INADDR_NONE)`
* `end()` clears static IP ([issue](https://github.com/espressif/arduino-esp32/issues/9674))

### Mbed Core WiFi

Issues:

* doesn't have `setHostname`. Mbed doesn't support hostname 
* reversed ordering of bytes returned by `macAddress`, `BSSID` and `BSSID(n)` for compatibility with older Arduino WiFi libraries

pull requests: 

* <del>[`dnsIP(n)`](https://github.com/arduino/ArduinoCore-mbed/pull/756)</del>
* <del>[scan result BSSID and channel() implementation](https://github.com/arduino/ArduinoCore-mbed/pull/815)</del>
* <del>[make `config` functions work](https://github.com/arduino/ArduinoCore-mbed/pull/816)</del>
* <del>[DNS server IP getters fix](https://github.com/arduino/ArduinoCore-mbed/pull/809)</del>


### Mbed Core Ethernet

Implements the legacy Ethernet API.

Issues are

* doesn't have `setHostname`. Mbed doesn't support hostname 
* <del>can't return to DHCP after using static IP [PR](https://github.com/arduino/ArduinoCore-mbed/pull/823)</del>
* `MACAddress` returns reversed ordering of bytes [issue](https://github.com/arduino/ArduinoCore-mbed/issues/752)
* <del>[DNS server IP getters fix](https://github.com/arduino/ArduinoCore-mbed/pull/809)</del>


### C33 WiFi (WiFiC3)

With listed pull requests applied, remaining issues are

* hostname is not sent with DHCP request
* doesn't have WiFi.h
* `firmwareVersion()` returns a constant

pull requests:

* <del>[can't set static IP](https://github.com/arduino/ArduinoCore-renesas/pull/179)</del>
* <del>[dnsIP(n)](https://github.com/arduino/ArduinoCore-renesas/pull/176)</del>
* <del>[BSSID returns own MAC address](https://github.com/arduino/ArduinoCore-renesas/pull/177)</del>
* <del>[config(ip) doesn't set default DNS IP](https://github.com/arduino/ArduinoCore-renesas/pull/199)</del>
* [wrong static DNS servers IP entries handling](https://github.com/arduino/ArduinoCore-renesas/pull/200)
* <del>[static IP change not applied](https://github.com/arduino/ArduinoCore-renesas/pull/201)</del>
* <del>[reversed MAC](https://github.com/arduino/ArduinoCore-renesas/pull/184)</del>


### C33 Ethernet

With listed pull requests applied, remaining minor issues are

* doesn't have `Ethernet.end()`
* doesn't have Ethernet.h

pull requests:

* `setHostname`,`setDnsServerIP`, `hostByName` [PR](https://github.com/arduino/ArduinoCore-renesas/pull/221)
* [wrong static DNS servers IP entries handling](https://github.com/arduino/ArduinoCore-renesas/pull/200)
* <del>[static IP change not applied](https://github.com/arduino/ArduinoCore-renesas/pull/201)</del>
* <del>`MACAddress`with parameter [PR](https://github.com/arduino/ArduinoCore-renesas/pull/214)</del>


### RP2040 Wifi

All problems discovered with the test were solved.

### RP2040 LwipIntfDev

LwipIntfDev implements the modern Ethernet API draft. EthernetCompat wraps LwipIntfDev for legacy Ethernet API

All problems discovered with the test were solved.