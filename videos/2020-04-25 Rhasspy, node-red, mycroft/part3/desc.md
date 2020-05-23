# Privacy Aware Voice Assistant, now for real: Mycroft, Rhasspy, and Node-Red - Part 3 (Node-RED Integration)

![Thumbnail of video][thumb.png]

**Resources:**
- Video on Youtube: https://youtu.be/hZwv80gB8f0
- Video on lbry:
- This page: https://github.com/ulno/doc/blob/master/videos/2020-04-25%20Rhasspy%2C%20node-red%2C%20mycroft/part3/desc.md

## Introduction
As you see I am fascinated by voice assistants, but I don't like them sending things into the cloud. Do I really want Google, Amazon, Apple, and Microsoft listen and analyze every word I say in my private four walls? How about voice assistants in an industrial setting - is it OK there to send everything to an external cloud provider? Not sure that such solutions will be easy to sell. And what happens when the Internet is down?

As excited as I initially was with Mycroft (https://mycroft.ai), I was a little disappointed to find out that they use (their own) third party cloud service to do speech to text. However, Mycroft is open source, so it allows me to exchange this service. After discovering Rhasspy (http://rhasspy.org, https://github.com/rhasspy/rhasspy-voltron, a system especially focusing on offline voice assistance).
I still like Mycroft and its community a lot, so what to do?

Hey, I am a software engineer, I thought: "I can glue systems together that don't have explicit interfaces for each other". So, let's use Node-RED (https://nodered.org) as glue!

Follow along in this mini-series. The series has 3 parts and in this third part, we will intgrate Mycroft and Rhasspy with Node-RED to achieve the actually privacy aware voice assistant:

1. Installation Mycroft: https://youtu.be/-fYOf4WBvzk
2. Installation Rhasspy: https://youtu.be/xKgPrn8ukcM
3. Integration with Node-RED: https://youtu.be/hZwv80gB8f0

If you want to see the rest of the documentation with code examples, check this document here: https://github.com/ulno/doc/blob/master/videos/2020-04-25%20Rhasspy%2C%20node-red%2C%20mycroft/part3/desc.md


## Implementation

First, remember to configure the site-id in Rhasspy.

Open your Node-RED (hope you have managed to install it, else revisit https://nodered.org/docs/getting-started/) at http://localhost:1880 (or replace with the computer in your network (instead of localhost) where you installed it.

Create the following Websockets in Node-RED:
- Websocket (in) rhasspy: ws://rhasspy-host:12101/api/events /intent (don't start with /) - name: rhasspy event
- Websocket (in/out) mycroft: ws://mycroft-host:8181/core - names: mycroft in, mycroft out

The post request for text to speech later is: http://rhasspy-host:121-1/api/text-to-speech

The whole flow I built in the movie is here (follow along or import it directly into node-red):
```json
[
    {
        "id": "d07c0ec7.a579f",
        "type": "websocket in",
        "z": "5865ed76.4a5264",
        "name": "rhasspy event",
        "server": "b14940fc.bb7f7",
        "client": "",
        "x": 90,
        "y": 40,
        "wires": [
            [
                "fc6544bf.cc0e78"
            ]
        ]
    },
    {
        "id": "fc6544bf.cc0e78",
        "type": "json",
        "z": "5865ed76.4a5264",
        "name": "",
        "property": "payload",
        "action": "",
        "pretty": false,
        "x": 250,
        "y": 40,
        "wires": [
            [
                "b37f653f.76d798",
                "af1b4a55.bd6198"
            ]
        ]
    },
    {
        "id": "b37f653f.76d798",
        "type": "debug",
        "z": "5865ed76.4a5264",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "x": 540,
        "y": 120,
        "wires": []
    },
    {
        "id": "403cbc60.d025f4",
        "type": "switch",
        "z": "5865ed76.4a5264",
        "name": "pick intent",
        "property": "payload.intent",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "Light",
                "vt": "str"
            },
            {
                "t": "else"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 300,
        "y": 100,
        "wires": [
            [],
            [
                "3d6bd541.34128a"
            ]
        ]
    },
    {
        "id": "e8f13910.ded358",
        "type": "websocket out",
        "z": "5865ed76.4a5264",
        "name": "mycroft out",
        "server": "e8e1e734.bb5e48",
        "client": "",
        "x": 550,
        "y": 160,
        "wires": []
    },
    {
        "id": "aab2309c.88f7e",
        "type": "websocket in",
        "z": "5865ed76.4a5264",
        "name": "mycroft in",
        "server": "e8e1e734.bb5e48",
        "client": "",
        "x": 100,
        "y": 280,
        "wires": [
            [
                "ba9d535a.a07fe"
            ]
        ]
    },
    {
        "id": "17fa1351.be280d",
        "type": "switch",
        "z": "5865ed76.4a5264",
        "name": "select speak",
        "property": "payload.type",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "speak",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 410,
        "y": 280,
        "wires": [
            [
                "b37f653f.76d798",
                "5b063660.97bed8"
            ]
        ]
    },
    {
        "id": "e689af37.1a0b5",
        "type": "template",
        "z": "5865ed76.4a5264",
        "name": "forward utterance",
        "field": "payload",
        "fieldType": "msg",
        "format": "handlebars",
        "syntax": "mustache",
        "template": "{\n    \"type\": \"recognizer_loop:utterance\",\n    \"data\":{\n        \"utterances\": [\"{{{payload.text}}}\"],\n        \"lang\": \"en-us\"\n    },\n    \"context\": {\n        \"client_name\":\"{{{satellite}}}\",\n        \"source\":  \"node-red\",\n        \"destination\": [\"skills\"]\n    }\n}",
        "output": "str",
        "x": 330,
        "y": 160,
        "wires": [
            [
                "e8f13910.ded358"
            ]
        ]
    },
    {
        "id": "3d6bd541.34128a",
        "type": "function",
        "z": "5865ed76.4a5264",
        "name": "del session",
        "func": "delete msg._session;\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 130,
        "y": 160,
        "wires": [
            [
                "e689af37.1a0b5"
            ]
        ]
    },
    {
        "id": "af1b4a55.bd6198",
        "type": "change",
        "z": "5865ed76.4a5264",
        "name": "set id to desktop",
        "rules": [
            {
                "t": "set",
                "p": "satellite",
                "pt": "msg",
                "to": "ulno-desktop",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 120,
        "y": 100,
        "wires": [
            [
                "403cbc60.d025f4"
            ]
        ]
    },
    {
        "id": "ba9d535a.a07fe",
        "type": "json",
        "z": "5865ed76.4a5264",
        "name": "",
        "property": "payload",
        "action": "",
        "pretty": false,
        "x": 250,
        "y": 280,
        "wires": [
            [
                "17fa1351.be280d"
            ]
        ]
    },
    {
        "id": "de0bcae7.ed79b8",
        "type": "http request",
        "z": "5865ed76.4a5264",
        "name": "speak on ulno-desktop",
        "method": "POST",
        "ret": "txt",
        "paytoqs": false,
        "url": "http://ulno-desktop:12101/api/text-to-speech",
        "tls": "",
        "persist": false,
        "proxy": "",
        "authType": "",
        "x": 500,
        "y": 420,
        "wires": [
            []
        ]
    },
    {
        "id": "1ebebd2c.fbba33",
        "type": "switch",
        "z": "5865ed76.4a5264",
        "name": "pick satellite",
        "property": "satellite",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "ulno-desktop",
                "vt": "str"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 270,
        "y": 420,
        "wires": [
            [
                "de0bcae7.ed79b8"
            ]
        ]
    },
    {
        "id": "5b063660.97bed8",
        "type": "function",
        "z": "5865ed76.4a5264",
        "name": "extract satellite and text",
        "func": "msg.satellite = msg.payload.context.client_name;\nmsg.payload = msg.payload.data.utterance;\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 290,
        "y": 340,
        "wires": [
            [
                "1ebebd2c.fbba33"
            ]
        ]
    },
    {
        "id": "b14940fc.bb7f7",
        "type": "websocket-listener",
        "z": "",
        "path": "ws://ulno-desktop:12101/api/events/intent",
        "wholemsg": "false"
    },
    {
        "id": "e8e1e734.bb5e48",
        "type": "websocket-listener",
        "z": "",
        "path": "ws://ulno-desktop:8181/core",
        "wholemsg": "false"
    }
]
```

Make sure, you use the right websockets and URLs and don't forget to deploy ;-)

Now you should have the full integration and can finally ask Mycroft for the weather or the time (as you can use all the other skills of Mycroft, but you will have to extend the Rhasspy grammar to support the phrases you want to ask).

## End Matter

I hope you enjoyed this integration exercise and let me know if you manage to integrate other skills.

Links to all projects:
- Mycroft: https://mycroft.ai)
- Rhasspy: http://rhasspy.org, https://github.com/rhasspy/rhasspy-voltron
- Node-RED: https://nodered.org

And don't forget to check these web-sites and my own Instagram account:
- ulno's site: https://ulno.net
- ulno's Instagram: https://www.instagram.com/ulno_net/
- IoTempire: https://iotempire.net

I am also now on lbry, check my channel there too:
lbry://@ulno#2
 
As usual, stay tuned and see you next time!

#makers #IoT #IoTempire #diy #arduino #raspberrypi #opensource #nodered #Node-RED #rhasspy #mycroft #mycroft.ai #privacy
