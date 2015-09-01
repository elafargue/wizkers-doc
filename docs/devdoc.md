#Developer documentation

## Building Wizkers

Refer to the [build instructions](building.md) to do a check out and initial build of the code. Wizkers mostly being a javascript application, there is no "compilation" per se, but rather automated code optimization, download of dependencies, etc.

## Wizkers architecture

Due to its multiple supported run modes, the structure of Wizkers is split between a “front-end” and a “back-end” part, even when it runs 100% in a browser.

The diagram below shows how the the various components of Wizkers interact with each other. If you want to add new instruments, this will be a good reference, though you will usually not need to go deep into those modules, and only implement instrument views and the instruments’s front-end/back-end API.

![Wizkers architecture](dev/img/Wizkers-arch-201502.png)

### Global Wizkers services

The Wizkers front-end exposes a couple of global objects/services throughout the application:

* The Instrument manager
* The Output manager
* The link manager

All communication between the various parts of Wizkers, whether they are located in the browser or on the server, are done using those three services.

Wizkers also exposes a couple of utility services:

* The statistics service, which enables the connection of Wizkers to analytics services
* The global settings


#### The link manager

The link manager is the interface between the front-end and the back-end of Wizkers. In particular, the front-end only talks to instruments through the link manager.

Even when Wizkers is running on its own (not in server mode), it makes use of the link manager, which makes it possible to use a single architecture for both in-app and server modes.

In server mode, the link manager uses socket.io to communicate with the server for all commands and instrument data - the HTML and javascript resources are loaded directly using HTTP.

In Chrome or Cordova mode, the link manager talks to the `chromeSocket.js` object, which emulates the API of socker.io but implements the backend on the app directly.

#### The Instrument manager

The instrument manager loads new instruments, switches between instruments.


#### The Output manager

The output manager subscribes to all data coming in from instruments, is in charge or starting/stopping outputs depending on what is enabled for a particular instrument, and forwards instrument data to all active outputs.


## Source tree organization

The Wizkers source tree is organized as follows:

```no-highlight
README.md            # Top level Readme, for the benefit of Github
build-tools          # A couple of build scripts used to optimize the javascript for the Chrome version
cordova              # Cordova-specific files used to build Wizkers
documentation        # The documentation you are reading right now
server               # Files used by Wizkers in server mode
chrome               # Files used by Wizkers in Chrome mode
gulpfile.js          # The toplevel gulpfile
merges               # er....
package.json         # npm toplevel package file
www                  # The complete javascript app - frontend only in server mode, complete app in Chrome/Cordova mode
```

### www tree

Most of the code of Wizkers is contained in the `www` directory, which is organized as follows:

```no-highlight
www/css             # Stylesheets used by the app
www/fonts           # Fonts used be the app
www/img             # Images used by the app
www/js              # The core of Wizkers, see below for organization
...                 # A couple of extra files used for examples
```

The `js` directory is then structured in a modular fashion, to make it easy to extend Wizkers:

```no-highlight
www/js/app              # The main Wizkers app
www/js/app/connections  # Used in Chrome/Cordova mode: low-level drivers for serial/bluetooth/etc
www/js/app/instruments  # All instruments supported by Wizkers
www/js/app/lib          # Wizkers shared libraries
www/js/app/models       # Wizkers Backbone models
www/js/app/outputs      # Output plugins
www/js/app/views        # Wizkers views (screens)
www/js/lib              # All third party libraries used by Wizkers
www/js/tpl              # All HTML templates used by the various Wizkers screens/views.
```

### Run-mode specific trees

The `cordova`, `server` and `chrome` directories contain all the files that are specific to those run-modes. When [building](building.md) Wizkers, Gulp will add the contents of those directories to the `dist` directory to end up with a working version of Wizkers for those various run modes.