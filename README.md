# Arduino Networking API

A repository to document the Arduino Networking API established by the Arduino Ethernet library, adopted for WiFi with the Arduino WiFi library, evolved with Arduino WiFiNINA library and esp8266 WiFi library.

We can use Issues, Pull Requests and Discussions to collaborate on the API documentation, clarifications and design decisions.

Some parts of the API are defined with classes Client and UDP with pure virtual methods. Other parts of the API are not strictly defined and are copied from library to library.

Sometimes the methods of the API are not implemented to behave the same way in all libraries.

* [Arduino networking API guide for networking library developers](ArduinoNetAPIDev.md)
* The [NetApiHelpers](https://github.com/JAndrassy/NetApiHelpers) library accompanies the Arduino networking API guide
* [Implementation of the API in significant libraries](ArduinoNetAPILibs.md)
* [Report](ArduinoNetLibsTestReport.md) from testing the WiFi/Ethernet object in libraries with test sketches from NetApiHelpers library
* There are multiple open discussions on the [Discussions tab](https://github.com/JAndrassy/Arduino-Networking-API/discussions)

Tip: open Outline for the documents:

![tip outline](open-outline.png)
