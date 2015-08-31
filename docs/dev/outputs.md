#Outputs

Outputs are plugins which subscribe to 'data' events coming from connected instruments, and are able to forward those events to other services. They extend the capabilities of Wizkers a great deal by turning it into a middleware between your sensors and any number of backend services.

A typical use case for outputs, is using the [safecast](../out/safecast.md) output to connect a Geiger counter to the [Safecast.org](http://safecast.org/) service to send regular radiation readings to the Safecast API.

## General architecture.

Due to the fact Wizkers in server mode supports multiple open devices at once and standalone operation, there are slight differences in the way outputs work in server vs Chrome mode, but a lot of similarities too.

* `wizkers/server/www/js/app/outputs/<output_name>/logo.png` is the output logo.

### Output definition

Outputs are defined in `wizkers/server/www/js/app/outputs/<output_name>/<output_name>.js`. This file defines a couple of high level output characteristics which are used by Wizkers to understand how and when it can be used.

* `this.wantOnly` returns an array of strings that describe the type of data the output will accept. For instance, the `safecast.js` output will return `['radioactivity']` so that it does not show up as an available output to devices which don't sent that sort of reading.
* `this.requestAllData` is true if the output want Wizkers to send it everything in "pass through" mode rather than use the regular/alarm mechanism
* `this.outputFields` is the list of fields required by the output to work. The result can be "variable" in case the output does not have a fixed number of fields or "none" if the output does not support the concept of fields.

### Front-end output config

Outputs always provide a configuration view, which is located in `wizkers/server/www/js/app/outputs/<output_name>/settings.js`. The corresponding template, by convention, lives in `wizkers/server/www/js/tpl/outputs/`.

The front-end view is a Backbone view: refer to the existing outputs to get an idea of how to manage output-specific settings, which are merged with general output settings automatically.

## Output backend drivers


### Chrome/Cordova mode

In this mode, you want to implement `wizkers/server/www/js/app/outputs/<output_name>/backend_driver.js` . The backend driver needs to implement a couple of public methods which are expected by the output manager:

* `setup`
* `onClose`
* `resolveMapping`
* `sendData`


### Server mode

In server mode, output backends live in `wizkers/server/server/outputs/` and implement the same API as in Chrome/Cordova mode. Since the outputs are executed in a NodeJS environment, they often use different low-level APIs which is the reason why they are separate from the 'app mode' output code.

