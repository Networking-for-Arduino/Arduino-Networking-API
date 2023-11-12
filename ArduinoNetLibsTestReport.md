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

With listed pull request applied, remaining issues are

* WiFi.h doesn't include WiFiClient.h
* hostname is not sent with DHCP request

pull requests:
* [fix auto dns IP](https://github.com/arduino/ArduinoCore-renesas/pull/173)
* [reversed MAC](https://github.com/arduino/ArduinoCore-renesas/pull/183)


### WiFiEspAT

Issues:

* WiFi.disconnect() clears static IP
* reversed macAddress and BSSID for compatibility with Arduino WiFi libraries.


## Ethernet

The test is [LegacyEthernetTest](https://github.com/JAndrassy/NetApiHelpers/blob/master/examples/LegacyEthernetTest/LegacyEthernetTest.ino) from the NetApiHelpers library. Tested are functions of the `Ethernet` object.

### Ethernet

With listed pull requests applied, remaining issues are

* `hardwareStatus()` and `linkStatus()` don't work before `begin()`
* doesn't have `Ethernet.end()`

pull requests:

* [setHostname](https://github.com/arduino-libraries/Ethernet/pull/233)
* [hostByName](https://github.com/arduino-libraries/Ethernet/pull/232)


### STM32Ethernet

Test was not run. Compilation problems:

* doesn't have `hardwareStatus`
* doesn't have `setDnsServerIP`
* doesn't have `setHostname`
* doesn't have `end()`
* doesn't have `hostByName`


### EthernetENC

As the EthernetENC library is maintained by the author of this document, all problems discovered with the test were solved.


## LwIP WiFi + Ethernet

### ESP8266 WiFi

Differences:

* `begin` is async. `status = WiFi.waitForConnectResult();` can be used to wait

Issues:

* doesn't have auto configuration for DNS, gw, mask for static IP
* doesn't have WiFi.h


### ESP8266 Ethernet

With listed pull request applied, remaining issues are

* hardwareStatus() and linkStatus() don't work before begin()
* doesn't have `hostForName`

pull requests:

* [end() and re-begin](https://github.com/esp8266/Arduino/pull/9023)


### ESP32 WiFi

Differences:

* `begin` is async. `status = WiFi.waitForConnectResult();` can be used to wait
* ScanNetworks - encryptionType enum has different constants than other WiFi libraries
* `disconnect` is asynchronous. Without waiting for status change after it, it can interfere with immediately following `begin`

Issues:

* `config` has wrong ordering of parameters
* doesn't have auto configuration for DNS, gateway, net mask for static IP


### ESP32 Ethernet

ESP32 Arduino platform Ethernet library is too different to run the test. 


### Mbed Core WiFi

Test was not run. Detected problems are:

* doesn't have `setHostname` (MbedSocketClass)
* reversed ordering of bytes returned by `macAddress`, `BSSID` and `BSSID(n)` for compatibility with older Arduino WiFi libraries

pull requests: 

* [`dnsIP(n)`](https://github.com/arduino/ArduinoCore-mbed/pull/756)


### Mbed Core Ethernet

Issues are

* `hardwareStatus()` and `linkStatus()` before `begin` cause `begin` to fail
* `setDnsServerIP` applies the DNS IP only in `begin`. immediate call to `dnsServerIP` returns the previous value used with `begin`
* doesn't have `setHostname` (MbedSocketClass)
* `MACAddress` returns reversed ordering of bytes [issue](https://github.com/arduino/ArduinoCore-mbed/issues/752)

### C33 WiFi

With listed pull requests applied, remaining issues are

* hostname is not sent with DHCP request
* doesn't have WiFi.h
* does scan in `begin` and only joins the network if it is in the scan result
* static IP change ignored after first `begin`

pull requests:

* [can't set static IP](https://github.com/arduino/ArduinoCore-renesas/pull/179)
* [reversed MAC](https://github.com/arduino/ArduinoCore-renesas/pull/184)

### C33 Ethernet

Test was not run. Compilation problems:

* doesn't have `setHostname`
* doesn't have `setDnsServerIP`
* doesn't have `Ethernet.end()`
* doesn't have Ethernet.h
* doesn't have `hostByName`
* `MACAddress` doesn't have the parameter
