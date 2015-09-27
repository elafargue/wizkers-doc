# Wizkers API access in server mode

When running on a server, you can also interact with it using a REST API, so that all the data from the server can be queried/streamed to external systems.


## Live recording

Wizkers exposes a public "live recording" API, which lets you query data that is currently being recorded. The syntax is as follows

`/live/:id/:period` with `id` being the instrument ID, and `period` being in minutes - get the live recording for the last `period` minutes.


## Low level API access

You can access the low level RESTFUL API of Wizkers using the standard authentication mechanism that is used by the Wizkers front-end web app. The simple Shell script below demonstrates how to get a list of instruments in JSON format.

```
#!/bin/sh

## Example of Wizkers authentication then API request

## Save cookies to the "cookies.txt" file
curl --cookie-jar cookies.txt -H "Content-Type: application/json" -X POST http://localhost:8090/login -d "{\"email\": \"admin\", \"password\": \"abc123\"}"

## Now you can do queries:
curl --cookie cookies.txt -X GET http://localhost:8090/instruments
```

### Listing instruments

You can get a list of all instruments registered in a Wizkers instance by doing an HTTP GET to /instruments/ . Note that this returns all instruments contents, not only a list of references. 

Example below:

```
[
    {
    "name":"Piglet",
    "type":"simple_serial",
    "tag":"",
    "uuid":"00000000 (n.a.)",
    "port":"/dev/cu.usbserial-FTGDPEGI",
    "comment":"enter your notes here",
    "icon":"",
    "liveviewspan":600,
    "liveviewperiod":1,
    "liveviewlogscale":false,
    "metadata":{"baudrate":"9600","lines":"40"},
    "_id":"5AD6B170-A8AC-820E-9070-6DB9619661C4",
    "_rev":"19-5199b8924f8388771d32d13f0b26c508"
    },
    {
    "name":"OLED",
    "type":"fcoledv1",
    "tag":"",
    "uuid":"00000000 (n.a.)",
    "port":"/dev/cu.usbmodem14121",
    "comment":"enter your notes here",
    "icon":"",
    "liveviewspan":600,
    "liveviewperiod":1,
    "liveviewlogscale":false,
    "metadata":{},
    "_id":"6C8BCEB8-77C7-DF73-930A-3DEA48992119",
    "_rev":"3-643fc192e8094b885f0b29c4f944e8f3"
    },
    {
    "name":"FCOLED",
    "type":"fcoledv1",
    "tag":"",
    "uuid":"00000000 (n.a.)",
    "port":"/dev/cu.usbmodem1451",
    "comment":"enter your notes here",
    "icon":"",
    "liveviewspan":600,
    "liveviewperiod":1,
    "liveviewlogscale":false,
    "metadata":{},
    "_id":"8E7A801E-8AD1-FB5C-B772-CABA61B8FCC6",
    "_rev":"7-13d2c6309ffeab1bcf87833fae563764"
    },
]
```

### Instruments details

Returns one instrument's details (http GET):

```http://localhost:8090/instruments/6C8BCEB8-77C7-DF73-930A-3DEA48992119```

### Listing logs for an instrument

You can get all the logs for a given instrument by doing a GET on /logs :

```http://localhost:8090/instruments/E1B65550-BEA8-63BB-9AFA-58F94E0D881D/logs```

```
[
    {
    "instrumentid":"E1B65550-BEA8-63BB-9AFA-58F94E0D881D",    
    "name":"Autorecord",
    "description":"Autorecord",
    "logtype":"live",
    "startstamp":1437430818782.795,
    "isrecording":false,
    "datapoints":11,
    "swversion":0,
    "endstamp":1437431082121.264,
    "_id":"4E148536-1F12-8F85-AF9B-6E48401F030C",
    "_rev":"5-ef4df5e4f9a9e85e26252cbfc175706f"
    }
]
```

### Log download

The contents of a log are returned with (http GET):

```http://localhost:8090/logs/4E148536-1F12-8F85-AF9B-6E48401F030C/entries```

Each log record contains two keys:

- timestamp: javascript timestamp (milliseconds since January 1 1970). Time when the entry was added to the log. Sometimes (esp for on-intrument log download), the timestamp in the 'data' key will be more important.
- data : structure depends on the instrument.

```
[
    {
       "timestamp":1437430818782.795,
       "data":{"uniqueID":"00000000 (n.a.)"},
       "_id":"1437430818782.795"
    },
    {
        "timestamp":1437430878351.307,
        "data":{
            "probeid":3001739,
            "timestamp":25544,
            "devicestamp":1437426924000,
            "cpm":{
                "value":0,
                "valid":true
            },
            "cpm2":{
                 "value":0,
                 "valid":true
                }
        },
        "_id":"1437430878351.307"
    },
    {
        "timestamp":1437430878351.7,
        "data":{
           ...
        },
        "_id":"1437430878351.7"
    }

....
    
    ]
```

