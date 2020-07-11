# whatsapp-api-client library for javascript
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/green-api/whatsapp-api-client/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/v/release/green-api/whatsapp-api-client.svg)](https://github.com/green-api/whatsapp-api-client/releases)
[![npm version](https://badge.fury.io/js/%40green-api%2Fwhatsapp-api-client.svg)](https://www.npmjs.com/package/@green-api/whatsapp-api-client)

This library helps you easily create a javascript application to connect the WhatsApp API using service [green-api.com](https://green-api.com). You need to get ``ID_INSTANCE``and ``API_TOKEN_INSTANCE`` in the [control panel](https://cabinet.green-api.com) in order to use this library.

## Документация на русском языке
Смотрите readme на русском [здесь](README_RUS.md)

## API

The API corresponds with [REST API](https://green-api.com/docs/api/) from green-api since the library wraps own methods as https calls to the service. Therefore using these [docs](https://green-api.com/docs/) is highly encouraged.


## Installing

Library supports both browser environment without package managers and Node/Webpack apps with package manager that can handle ``require`` or ``import`` module expressions.
For webpack and npm based apps:
```
npm i @green-api/whatsapp-api-client
```
For vanilla html-js website  modify index.html:
``` html
<script src="https://unpkg.com/@green-api/whatsapp-api-client/lib/whatsapp-api-client.min.js"></script>
```

## Authentification

Sending WhatsApp message like any other call to the API requires account registered on [green-api.com](https://green-api.com) and authentication completed on mobile WhatsApp app. To register account you have to proceed to the [control panel](https://cabinet.green-api.com). After registering you wll get own unique pair of `ID_INSTANCE` and `API_TOKEN_INSTANCE` keys.

WhatsApp mobile app authentication may be achived by using [control panel](https://cabinet.green-api.com). You need to scan QR-code generated within the control panel.

## Examples

### Send WhatsApp message
Use common javascript

``` js
const whatsAppClient = require('@green-api/whatsapp-api-client')

const restAPI = whatsAppClient.restAPI(({
    idInstance: YOUR_ID_INSTANCE,
    apiTokenInstance: YOUR_API_TOKEN_INSTANCE
}))

restAPI.message.sendMessage(null, 79999999999, "hello world")
.then((data) => {
    console.log(data);
}) ;

```
or use browser js script
``` html
<script src="https://unpkg.com/@green-api/whatsapp-api-client/lib/whatsapp-api-client.min.js"></script>
<script>
    const restAPI = whatsAppClient.restAPI(({
        idInstance: "YOUR_ID_INSTANCE",
        apiTokenInstance: "YOUR_API_TOKEN_INSTANCE"
    }))
    restAPI.message.sendMessage(null, 79999999999, "hello world")
    .then((data) => {
        console.log(data);
    }).catch((reason) =>{
        console.error(reason);
    });
</script>
```
Or use ES6 syntax. For node js app, you propably have to add in ``package.json`` property ``"type": "module"``. Notice that all examples below are ES6 based.

``` js
import whatsAppClient from '@green-api/whatsapp-api-client'

(async () => {
    const restAPI = whatsAppClient.restAPI(({
        idInstance: YOUR_ID_INSTANCE, 
        apiTokenInstance: YOUR_API_TOKEN_INSTANCE
    }))
    const response = await restAPI.message.sendMessage(null, 79999999999, "hello world");
})();
```

### Send WhatsApp file
``` js
import whatsAppClient from '@green-api/whatsapp-api-client'

(async () => {
    const restAPI = whatsAppClient.restAPI(({
        idInstance: YOUR_ID_INSTANCE,
        apiTokenInstance: YOUR_API_TOKEN_INSTANCE
    }))
    const response = await restAPI.file.sendFileByUrl(null, 79999999999, 'https://avatars.mds.yandex.net/get-pdb/477388/77f64197-87d2-42cf-9305-14f49c65f1da/s375', 'horse.png', 'horse');
})();
```

### Send WhatsApp message and receive webhook

Webhooks are event-based callbacks invoked by green-api server as responses to client API calls. Webhooks support node js and express based apps only.

``` js
import whatsAppClient from '@green-api/whatsapp-api-client'
import express from "express";
import bodyParser from 'body-parser';

(async () => {
    try {

        // Set http url, where webhooks are hosted. 
        // Url must have public domain address.
        await restAPI.settings.setSettings({
            webhookUrl: 'MY_HTTP_SERVER:3000/webhooks'
        });

        const app = express();
        app.use(bodyParser.json());
        const webHookAPI = whatsAppClient.webhookAPI(app, '/webhooks')

        // Subscribe to webhook happened when WhatsApp delivered a message
        webHookAPI.onIncomingMessageText((data, idInstance, idMessage, sender, typeMessage, textMessage) => {
            console.log(`outgoingMessageStatus data ${data.toString()}`)
        });

        // Run web server with public domain address
        app.listen(3000, async () => {
            console.log(`Started. App listening on port 3000!`)

            const restAPI = whatsAppClient.restAPI(({
                idInstance: MY_ID_INSTANCE,
                apiTokenInstance: MY_API_TOKEN_INSTANCE
            }));
            // Send test message that triggers webhook
            const response = await restAPI.message.sendMessage(null, 79999999999, "hello world");
    
        });
    } catch (error) {
        console.error(error);
        process.exit(1);
    }
})();

```
## Deploying development environment

Any  help with development and bug fixing is appreciated. In order to deploy test-ready environment please make the steps:

1. Сlone repo with ``git clone``
2. Install dependencies with ``npm install``
3. Install globally libraries ``rollup`` for bundled builds.
4. Add webhooks as new dev express  via npm ``npm isntall express --save-dev``. Dont forget to delete it before making pull request
5. Create .env file in root folder and add environment variables using example file [env.example](env.example)
6. Add ```"type": "module"``` to the package.json

## Build
Compile browser and node|webpack versions with single command:
```
npm run build
```

## Third-party libraries

* [axios](https://github.com/axios/axios) -  http requests
* [express](https://www.npmjs.com/package/express) - app server for webhooks

## License

Licensed on MIT terms. For additional info have look at [LICENSE](LICENSE)
