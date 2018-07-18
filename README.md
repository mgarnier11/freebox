# Freebox SDK JS

> Fast and secure Freebox OS requests.

Simplify Freebox login process and build a remote secure HTTPS connection to your Freebox server for your Javascript applications. You just have to do some configuration for your app's first login.

## Contents

- [Prerequisities](#prerequisities)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [License](#license)

## Prerequisities
- Freebox Revolution or newer

## Installation
```bash
$ npm install freebox-sdk-js
```

## Configuration

### For NodeJS

Our goal here is to complete the configuration to be able to connect securely to your Freebox server from anywhere.

1. **Initialize configuration file**  
Create `freebox.config.json` at your project root (same lvl as `package.json`) with the following and fill app properties. Fill `app_token` only if you already have one.
It will be your Freebox's app identity.
```json
// ~/freebox.config.json
{
  "app": {
    "app_id": "fbx.my-node-app-example",
    "app_name": "NodeJS App example",
    "app_version": "1.0.0",
    "device_name": "My PC",
    "app_token": null,
  },
  "connection": {
    "api_domain": null,
    "https_port": null
  }
}
```

2. **Getting `app_token` & `connection`**  
Getting your `app_token` & `connection` part is easy with `login` method !  
- `app_token`  
You just have to confirm your application's access manually on your Freebox Server's screen. It will register your app to your Freebox using your `app` informations in the config file.

- `connection`  
It will also get `connection` values if they are missing. This is used to establish **a secure app HTTPS connection with your Freebox**. It's really recommanded by the **_Freebox OS dev team_.**

```js
const Freebox = require('freebox-sdk-js');

(async () => {
    const freebox = new Freebox();
    await freebox.login();
})();
```

Congratulations, your configuration file is now complete ! Check it out:
```json
// ~/freebox.config.json
{
  "app": {
    "app_id": "fbx.my-node-app-example",
    "app_name": "NodeJS App example",
    "app_version": "1.0.0",
    "device_name": "My PC",
    "app_token": "dhirifbLVvuSNMAVgvKUxG/LwoNMaloNR2HUKwUiFqMrdN402aJTkZeqY1ISCqrP",
  },
  "connection": {
    "api_domain": "jtw4tb1.fbxos.fr",
    "https_port": 3129
  }
}
```

4. **Enjoy !**  
Let's check if our configuration works with an example Freebox request.
```js
const Freebox = require('freebox-sdk-js');

(async () => {
    const freebox = new Freebox();
    await freebox.login();

    // Retrieve a Download task: https://dev.freebox.fr/sdk/os/download/
    const response = await freebox.request({
        method: 'get',
        url: '/api/v4/downloads/'
    });

    const downloads = response.data.result;
    console.log(downloads);
})();
/* Console result:
[{ rx_bytes: 1520000000,
    tx_bytes: 0,
    download_dir: 'L0Rpc3F1ZJBkdXIvKkFMTA==',
    archive_password: '',
    eta: 0,
    status: 'done',
    io_priority: 'normal',
    type: 'http',
    piece_length: 0,
    queue_pos: 1,
    id: 1532,
    info_hash: '',
    created_ts: 1519648735,
    stop_ratio: 0,
    tx_rate: 0,
    name: 'Clean.Bandit.Rather-Be.mp4',
    tx_pct: 10000,
    rx_pct: 10000,
    rx_rate: 0,
    error: 'none',
    size: 1520000000 }, 
    {...}]
*/
```

### For Browser
For security purpose, I recommand to use it **only** if you host your JavaScript application in a **local** web server. You need an `app_token` corresponding at your `app_id` (you can get it by following the NodeJS guide above).

```js
// freebox.js
var Freebox = require('freebox-sdk-js');

// You will have to complete the missing fields
// Check your console for the first login
var config = {
  "app": {
    "app_id": "fbx.my-browser-app-example",
    "app_name": "Browser App example",
    "app_version": "1.0.0",
    "device_name": "My Server",
    "app_token": null
  },
  "connection": {
    "api_domain": null,
    "https_port": null
  }
};

var baseURL = 'http://mafreebox.freebox.fr';

(async () => {
    var freebox = new Freebox({ config, baseURL });
    // Check your console to get app_token and connection part for next connections.
    await freebox.login();

    // Let's check if our configuration works with an example Freebox request
    // Retrieve a Download task: https://dev.freebox.fr/sdk/os/download/
    var response = await freebox.request({
        method: 'get',
        url: '/api/v4/downloads/'
    });

    await freebox.logout();
    
    var downloads = response.data.result;
    console.log(downloads);
})();
```

## Usage
### Using a custom configuration file path
```js
const Freebox = require('freebox-sdk-js');

const configFilePath =  __dirname + '/path/to/config/file.json';

(async () => {
    const freebox = new Freebox({ config: configFilePath });
    await freebox.login();
})();
```

### Using a configuration object
```js
const Freebox = require('freebox-sdk-js');

const config = {
  "app": {
    "app_id": "fbx.my-app-example",
    "app_name": "App example",
    "app_version": "1.0.0",
    "device_name": "My PC",
    "app_token": null
  },
  "connection": {
    "api_domain": null,
    "https_port": null
  }
};

(async () => {
    const freebox = new Freebox({ config });
    await freebox.login();
})();
```

### Using a custom domain to your Freebox Server
```js
const Freebox = require('freebox-sdk-js');

const freeboxURL = 'https://mydomain.freeboxos.fr:3129';

(async () => {
    const freebox = new Freebox({ baseURL: freeboxURL });
    await freebox.login();
})();
```

### Request your Freebox
```js
const Freebox = require('freebox-sdk-js');

(async () => {
    const freebox = new Freebox();
    await freebox.login();

    // Get the current Wi-Fi global configuration: https://dev.freebox.fr/sdk/os/wifi
    const response = await freebox.request({
        method: 'get',
        url: '/api/v2/wifi/config/'
    });

    await freebox.logout();

    const wifiConfig = response.data.result;
    console.log(wifiConfig);
})();
/* Console result:
{
    "enabled": true,
    "mac_filter_state": "blacklist"
}
*/
```

## License
[MIT License](LICENSE) Copyright (c) 2018 Mathieu Schimmerling.

Crafted with ❤️ 