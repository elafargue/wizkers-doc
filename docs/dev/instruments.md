# Adding a new instrument


## The basic structure

Instrument plugins are stored in `wizkers/server/www/js/app/instruments`. Each instrument plugin is made of the following:

In every run mode:

* `icon.png` should be a 350x350px icon representing the instrument
* `instrument.js` . The main plugin entry point: defines what capabilities the instrument has, and the path to the other plugin files.
* `driver_frontend.js` The front-end driver, which runs in the browser
* `settings.js`  Instruments specific settings screen (displayed in the "instrument details" view)
* `display_live.js` The main "live" visualization. Can be as simple as a graph, or as sophisticated as the Elecraft KX3 controller
* `display_numeric.js` The right side visualization
* `display_diag.js` The interface for instrument configuration
* `display_.log.js` Log visualization interface, usually similar to ‘display_live’
* `display_logmanager.js`  Interface to manage and download on-device logs, if supported
* `display_logedit.js` Interface to manage editing log contents directly on Wizkers (optional)

All these files can be optional, depending on the capabilities you want to implement in your instrument.

In Chrome and Cordova mode, you will also need to add the following:

* `driver_backend.js` The back-end driver: the low-level driver for the instrument, which forwards its data to the front-end driver.

In server mode, the backend driver lives in `wizkers/server/server/parsers/` . Usually, the Chrome/Cordova `driver_backend.js` and the `parsers/instrument_backend.js` are similar, though the server-mode low level drivers tend to make use of the NodeJS buffer objects whereas the Chrome version uses pure Javascript ArrayBuffers.

This list might seem a bit overwhelming, but there are already many instruments defined in Wizkers, and you should definitely start a new instrument by 'borrowing' from an existing intrument, rather than restart everything from scratch. Wizkers also contains a `wizkers/server/www/js/app/instruments/sample_instrument` which is a sample instrument scaffolding and can be used to quickly add a new instrument.

## Connecting your instrument plugin to the rest of Wizkers

Once you have implemented those files, you will need to add a reference to the main plugin file (instrument.js) in `server/www/js/app/instruments/instrumentmanager.js`.

In server mode, you will also need to add a reference to the parser in `wizkers/server/server/connectionmanager.js` to make it available on the server.

## Instrument view templates

All templates are stored in `wizkers/server/www/js/tpl`. The best practice for new instruments is to add new templates in `www/js/tpl/instruments/instrument_name/XXX.html`.

Wizkers uses the basic BackboneJS "handlebars" template style.

 

