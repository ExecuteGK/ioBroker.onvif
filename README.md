![Logo](admin/onvif_logo.png)
# ioBroker.onvif

[![NPM version](http://img.shields.io/npm/v/iobroker.onvif.svg)](https://www.npmjs.com/package/iobroker.onvif)
[![Downloads](https://img.shields.io/npm/dm/iobroker.onvif.svg)](https://www.npmjs.com/package/iobroker.onvif)
![Number of Installations (latest)](http://iobroker.live/badges/onvif-installed.svg)
![Number of Installations (stable)](http://iobroker.live/badges/onvif-stable.svg)
[![Dependency Status](https://img.shields.io/david/Haba1234/iobroker.onvif.svg)](https://david-dm.org/Haba1234/iobroker.onvif)
[![Known Vulnerabilities](https://snyk.io/test/github/Haba1234/ioBroker.onvif/badge.svg)](https://snyk.io/test/github/Haba1234/ioBroker.onvif)

[![NPM](https://nodei.co/npm/iobroker.onvif.png?downloads=true)](https://nodei.co/npm/iobroker.onvif/)

**Tests:**: [![Travis-CI](http://img.shields.io/travis/Haba1234/ioBroker.onvif/master.svg)](https://travis-ci.org/Haba1234/ioBroker.onvif)


### Customization
1. Open Driver Settings
2. Press the scan button (top right)
3. Enter the necessary settings or leave the default: 
startRange - the starting ip address of the scanning range, 
End Range - the ending ip address of the scanning range, 
Port list - comma-separated ports of the onvif service (default: 80, 7575, 8000, 8080, 8081), 
User name - default admin, 
Password - default admin
4. Press START SCAN

If everything is made correctly, then the found cameras will appear in a primary window of settings and in several seconds snapshots will have to be tightened.

### Events
The driver automatically subscribes to events for the configured cameras.
The events generated by the camera will be displayed in the following objects:

```
onvif.0.122_116_220_230_2033.message.ruleengine.cellmotiondetector.motion.IsMotion
onvif.0.122_116_220_230_2033.message.ruleengine.tamperdetector.tamper.IsTamper
```

### Snapshot request
To do this, use the command:
`sendTo('onvif.0', command, message, callback);`

Example of a script for request of the snapshot and sending to Telegram:

```
const fs = require('fs');

function getSnapshot(caption){
    sendTo('onvif.0', 'saveFileSnapshot', {"id":"192_168_1_4_80", "file":"/opt/cameras/snapshot.jpg"}, (data) => {
        console.log('image received: ' + data);
        if (data === "OK")
            sendTo('telegram.0', {text: '/opt/cameras/snapshot.jpg', caption: caption});
    });
}
```

*caption* - is heading for the picture in Telegram
It is possible to cause both on an event, and according to the button/schedule.

The option of loading into an intermediate Buffer in the file location:
```
function getSnapshot(){
    sendTo('onvif.0', 'getSnapshot', {"id":"192_168_1_4_80"}, (result) => {
        if (result.err) log(result);
        if (result.img){
			log('image received: ' + typeof result.img);
            sendTo('telegram.0', {
                user: 'user', 
                text: result.img.rawImage,
                type: 'photo',
                caption: 'Camera 1'
			});
		}
    });
}
```

### Camera Events
To disconnect the subscription to events from the camera, you need to set the status `subscribeEvents = false` and restart the adapter.
When changing in the admin panel, the adapter is restarted automatically.

Events are of type "Object", for example:
```
{
	'Value': false,
	'UtcTime': '2020-04-26T17:35:34.000Z'
}
```
`Value` - value/state,
`UtcTime` - state change time

because the adapter works by subscribing to events, the state time of `state.ts` may not coincide with the real time of the event in the camera.


## Changelog

### 0.4.4 (2020-05-12)
* (haba1234) Added new state

### 0.4.3 (2020-05-08)
* (haba1234) Snapshot preview is squeezed
* (haba1234) Preview is buffered and not requested again
* (haba1234) After a minute, re-subscribe to camera events after 4 errors
* (haba1234) Support digest authentification
* (haba1234) node >= 10

### 0.4.2 (2020-05-03)
* (haba1234) Updated admin panel

### 0.4.1 (2020-04-27)
* (haba1234) States as an Object
* (haba1234) Error control 'pullMessages'. Disconnect if there are more than three errors
* (haba1234) Encryption disabled. Compatibility issues

### 0.3.0 (2020-04-24)
* (haba1234) Added support for the Discovery adapter
* (haba1234) Added password encryption

### 0.2.0 (2020-04-21)
* (haba1234) Added camera settings
* (haba1234) Changes in the structure of objects (ATTENTION! After updating, delete cameras and add again)
* (haba1234) Fixed issue [#9](https://github.com/Haba1234/ioBroker.onvif/issues/9)

### 0.1.2 (2020-04-19)
* (haba1234) Fixed uncaught exception: The \"chunk\" argument must be one of type string or Buffer
* (haba1234) Add state 'subscribeEvents'

### 0.1.1 (2020-04-18)
* (haba1234) Port polling bug fixed

### 0.1.0 (2020-04-15)
* (haba1234) bag fix and different little things
* (haba1234) compact mode
* (haba1234) deprecated 'request' is replaced by class 'http'
* (haba1234) 'onvif-snapshot' is replaced by class 'http'
* (haba1234) Added translate
* (haba1234) Refactoring code

### 0.0.2 (2018-11-20)
* (haba1234) add events and snapshot

### 0.0.1 (2018-02-20)
* (Kirov Ilya) intial commit

## License

The MIT License (MIT)

Copyright (c) 2018-2020 Haba1234 <b_roman@inbox.ru>
