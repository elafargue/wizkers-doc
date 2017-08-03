#Building Wizkers

## Prerequisites (all platforms)

The only prerequisite for building (and running Wizkers in server mode) are NodeJS and npm.

You will need to install a recent version of NodeJS. This used to be a tricky requirement on Linux, but not anymore, since it is now easy to install Node JS using [most package managers](https://nodejs.org/en/download/package-manager/).

Note that NodeJS is a project that evolves very fast, and pretty much 100% of all distributions out there ship with very outdated version of Node. Resist the temptation to use the version of Node that is shipped with
your distribution, it won't work properly. Likewise, do not try to use you distribution's package manager to install Node sub-dependencies, those will also be outdated and will break. Like Python, NodeJS does not play very well with
the classic packaging model, which is unfortunate but beyond the scope of this discussion...

At the time of this writing, Wizkers can be built and run using **Node version 6.9.1** (aka "LTS" on the nodejs.org website).

### Platform-specific prerequisites

#### MacOS

No additional prerequisites.

#### Linux (Debian and Ubuntu based Linux distributions)

We assume here that you are starting with a computer/server/board running on Debian or Ubuntu Linux, or a variant. This includes
for instance most Beaglebone images, Rapsberry Pi, etc. Installation of Linux itself is out of the scope of this document.

On a new installation, you will need to install the basic development toolchains, as well as NodeJS and git:

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get install --yes ntpdate
sudo ntpdate -b -s -u pool.ntp.org
sudo apt-get install --yes build-essential
sudo apt-get install --yes curl
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install --yes nodejs
sudo apt-get install --yes git
```

You can then move on to checking out the code, as described in the next section.

#### Node-serialport issues on Linux

(Note: this might be outdated on current versions of Debian/Ubuntu, to be validated)

You might have to add rules in udev to make sure the right entries are created in `/dev/serial` because Debian does not do it by default, at least on the BeagleBone.

```
debian@aprs:~$ cat /etc/udev/rules.d/60-ed-persistent-serial.rules 
ENV{.ID_PORT}=="", SYMLINK+="serial/by-id/$env{ID_BUS}-$env{ID_SERIAL}-if$env{ID_USB_INTERFACE_NUM}"
ENV{.ID_PORT}=="?*", SYMLINK+="serial/by-id/$env{ID_BUS}-$env{ID_SERIAL}-if$env{ID_USB_INTERFACE_NUM}-port$env{.ID_PORT}"
```

This way, you will be able to list serial ports even on the most recent versions on node-serialport.    

#### Windows

While there is no good reason why building Wizkers on Windows shouldn't work, I have never attempted it. If you would like to contribute instructions, please reach out at [info@wizkers.io](mailto:info@wizkers.io).

## Building steps (all platforms)

### Check out the code

Wizkers is hosted on [github](https://github.com/wizkers/wizkers). These instructions suggest you use "git", but you can actually simply download the source code as a ZIP archive [here](https://github.com/wizkers/wizkers/archive/master.zip).
The upside of using git, though, is that you can easily upgrade to newer version of wizkers at a later stage by typing a simple `git pull` from the `wizkers` directory and follow the `gulp` steps below again.

Assuming you are using git: Create a work directory on your machine and check out the code from Github:

```bash
git clone https://github.com/wizkers/wizkers.git
cd wizkers/wizkers
```

### Build it - step 1

The next step is to install all dependencies using npm:

```bash
sudo npm install -g gulp-cli
npm install
```

This can take from a couple of minutes to quite some time if you are building on a low power system such as a Beaglebone.

Once all dependencies are installed, Wizkers uses 'gulp' as its build system. Gulp automates all build steps which would otherwise be pretty complex.

#### Pick your run mode

Wizkers supports multiple modes: server, nwjs, chrome, cordova, etc. Below is a short description of each mode:

Mode name                     | Description                                             |
:-----------------------------|:--------------------------------------------------------|
server                        | Run in server mode (port 8090 by default)               |
cordova                       | Run as an Android or iOS application. The output of the build will be an Android or iOS app that you have to install on your device.                    |
nsjw                          | Run as a native application using the nwjs.io framework. Instructions will be added below at a later stage on how to run Wizkers in that mode. |
chrome                        | Run as a Chrome app. See below for instructions on how to run in that mode.                                     |

#### Pick your flavor

There are also multiple Flavors of Wizker: Wizkers:Radio, Wizkers:Nuclear, etc. You will need to indicate the build flavor on the command
line by using the "OEM" variable. All build flavors correspond to an actual application that is built using the Wizkers framework, and they are listed in the "oem" directory.
The flavors that are defined at the time of this writing are:

Flavor name              | Description                                             |
:------------------------|:--------------------------------------------------------|
radio                    | [Wizkers:Radio](http://wizkers.io/wizkersradio)         |
nuclear                  | [Wizkers:Nuclear](http://wizkers.io/wizkersnuclear)     |
bench                    | Work in progress, for benchtop instruments              |
safecast                 | Builds Safecast:Drive, the Safecast.org Android app     |
 
#### Build it!

You can now build wizkers in the mode/flavor of your choice. For instance, for Wizkers:Radio in 'server' mode:

```bash
OEM=radio gulp server
```

The results of the build are in the `dist` directory.

## Finalizing and packaging

Once the base code is built through Gulp, the instructions below will show you how to finalize the build for the various run modes and launch Wizkers:

### Chrome app

After building, the Chrome version of Wizkers will be ready in `dist/chrome` and `dist/chrome-debug`. The `chrome-debug` directory can be immediately loaded into Google Chrome by visiting (chrome://extensions) and loading Wizkers from there.

The `chrome` version requires finalizing the compilation by launching `build-toold/build-chrome.sh` which will optimize the javascript code. The resulting Chrome app in `dist/chrome` will run faster but it won't be possible to debug it easily.

### Cordova (Android and/or iOS app)

The Android app version of Wizker uses the Cordova framework to package Wizkers into a native Android application.

The first thing to do is to install Cordova on your machine:

```bash
npm install -g cordova
```

Then cd to the `dist/cordova-debug` directory and add the target Cordova plaforms of your choice, and build, for instance:

```
cordova platform add Android
cordova build
```
For further instructions on how to complete the build and install on your device, please refer to the [Cordova documentation](http//cordova.apache.com/) online.

### NWJS.io

** Work in progress, come back soon! **

### Server

The server build of Wizkers is available in `dist/server`. Simply go to that directory and launch the server:

```bash
cd dist/server
./start_server.sh
```

Wizkers will be available on port 8090 on your machine. If you are running a browser on the same machine you are running Wizkers on, simply visit http://localhost:8090/ and follow the instructions!
