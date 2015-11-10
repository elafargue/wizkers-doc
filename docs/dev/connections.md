#Connections

Connections are the low-level hardware drivers which connect instrument drivers to the actual instruments. They can be serial ports, Bluetooth, network sockets, etc. depending on the instrument type.

From an architecture standpoint, connections are created and managed by instrument backend drivers (`wizkers/server/www/js/app/instruments/XXX/backend_driver.js` in Chrome/Cordova mode and `wizkers/server/server/parsers` in server mode). The backend driver listens to the various events coming from the low level drivers.

## All modes

All connections implement the following API:

* `open`
* `close`
* `write`
* (optional) `read`
* (optional) `flush`

And connections emit the `data` event whenever there is data ready to be sent to the upper level driver.

### Connection configuration

Each connection type should also provide a configuration view, located in `wizkers/server/www/js/app/views/instruments/`, along with a template in `wizkers/server/www/js/tpl/connections`. You can refer to the structure of existing connections to get a clearer understanding of how they work.

### Making a new connection available to instruments

In Chrome/Cordova mode, a new connection is connected to the rest of the Wizkers codebase by refering to it in `wizkers/server/www/js/app/instruments/connectionmanager.js` as part of the instrument plugin setup.

In server mode, new connections are used directly by instrument backend-drivers (in `wizkers/server/server/parsers`).

## Connection support by mode

| Connection type   | Chrome     | Cordova | Server | Comment  |
|-------------------|------------|---------|--------|----------|
| Serial            | Yes        | Yes     | Yes    | Standard serial ports (incl. serial over USB) |
| TCP/IP Sockets    | Yes        | No      | No     | Serial over TCP sockets |
| USB HID           | Yes        | No      | No     | USB HID communications. |
| Helium            | No         | No      | Yes    | Network connection to the [helium.com](http://helium.com) IoT network. |
| Pinocc.io         | No         | No      | Yes    | Network connection to the Pinocc.io IoT network |
| Bluetooth LE      | Yes        | Yes     | No     | Chrome support on Chromebooks only
| WebRTC            | Yes        | Not tested | No  | Connection over a WebRTC data channel (using the [peerjs.com](http://peerjs.com) library) |

## Serial Connection

The  serial connection is the first that was supported in Wizkers. It works in every mode (Server, Android/Cordova, Chrome) and provides access to any serial device that is connected to the computer.

## Serial over TCP/IP Sockets

This is accessible in Chrome mode only for now. TCP/IP sockets are an extension to the serial connection, and are managed along with the serial connection (this adds an extra entry in the serial port dropdown). You will have to explicitely add support for this on your instrument's backend driver, like you can see here: `www/js/app/instruments/elecraft/backend_driver.js`.

## USB HID Connection

This connection type is only supported in Chrome packaged app mode at the moment (0.9.3). It provides read/write access to generic USB HID devices through the usual connection API.

Due to the nature of USB HID, which is a polling system, you will need to call the “read” method on the driver to get data from the device. You can look at the Sark110 backend driver in `www/js/app/instruments/sark110/backend_driver.js` for an example of how this can be implemented.

## Helium connection

This connection is only supported in server mode. It establishes a 'subscription' to a device on the Helium.com netword, and supports the usual write/open/close methods. Data is automatically sent through the 'data' event.

## Pinocc.io connection

Likewise, the Pinocc.io plugin lets you talk and receive data from devices connected to the Pinocc.io IoT network.

_Note:_ the Pinocc.io network is now obsolete, and a standalone `pinoccio-server.js` alternative makes it possible to use Pinocc.io devices without the Pinocc.io IoT backend.

## Bluetooth LE connection

This connection is supported on Chromebooks only because of a Chrome runtime limitation (August 2015)

## WebRTC

This connection should work on both Chrome and Cordova, but is only tested on Chrome. This is very experimental.
