# WeChat Mini Program Monitoring

![](https://img.shields.io/badge/node-16.x-green) ![](https://img.shields.io/badge/npm-8.x-brightgreen) ![](https://img.shields.io/badge/code%20size-96%20kb-blue)

[中文](./README.md) | [English](./README_EN.md)

## Introduction
    Since the introduction of WeChat Mini Programs in 2017, they have experienced significant growth over the past four years. By the first half of 2021, the total number of Mini Programs across the entire network has surpassed 7 million, showing a continuous upward trend.

    However, Dynatrace's products currently do not support monitoring WeChat Mini Programs, making it challenging to assess their performance. To address this gap, we have developed a front-end Mini Program probe called "Wechat Miniprogram Monitoring" to fulfill the urgent needs of our Chinese customers.

## Features

- ### **User Behavior Analysis**
Visualize the flow of Mini Program traffic and performance, gaining comprehensive insights into user sources, channels, and conversions.
- ### **Page Rendering Speed Analysis**
Monitor the presentation speed of visited pages by real users (including first rendering time, first load completion time, and stuttering occurrences), providing guidance for optimization based on issue identification.
- ### **User Interaction Analysis**
Eliminate the need for tedious manual event tracking. Automatically identify website operation click events to monitor usage frequency, response time, and usability. Additionally, monitor user interaction workflows to ensure stable business operations.
- ### **JS Error Monitoring**
Track the trend of JS error rates and identify the pages or script URLs where JS errors occur, along with information about the problematic lines, columns, and stack traces. The solution also supports SourceMap, enabling the restoration of compressed JS error code lines and columns.
- ### **User Experience Scoring**
Based on multiple core indicators of Mini Program performance (such as onReday, loading time, request time, request errors, JS errors, operation time, and operation usability), conduct multidimensional intelligent scoring to quantitatively measure Mini Program performance.
- ### **Regional and ISP Monitoring**
Analyze critical network performance metrics in different regions (including countries, provinces, cities), ISPs, and access methods (2G/3G/4G/WiFi), enabling rapid network issue identification.
- ### **Operating System Information Analysis**
Retrieve user device information and IP addresses for analysis purposes.
- ### **User Network Condition Monitoring**
Real-time monitoring of the current user's network status.
## Usage
### Create Custom Application
- Click on `Create custom application` to start the creation process.
![](./docs/images/Create%20custom%20application.jpg)
- Enter the Application Name.
![](./docs/images/Create%20custom%20application_1.jpg)
- Obtain `ApplicationID` and `BeaconURL`
![](./docs/images/Create%20custom%20application_2.jpg)
### Deployment and Installation
#### JS Beacon Installation
- Deploy the beacon JS file `dynatrace-monitor-wx.js` to the `utils` directory.
- Add the initialization code to the entry file  `app.js(ts)` of the Mini Program.
```javascript
//Native WeChat Development - Deployment
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});
```
```javascript
//UNI WeChat Development - Deployment (Vue2)
import Monitor from "@/utils/dynatrace-monitor-wx.js"
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});
```
<font color="red" size="4">*UNI Vue3 Installation of Beacon Notice</font>
- The Vue 3 version of UNIapp will no longer support CommonJS syntax and requires configuring commonjs in `vite.config.js`.
- Install `rollup-plugin-commonjs` using npm.
```cmd
npm i rollup-plugin-commonjs -D
```
- Add the following configuration to `vite.config.js`.
```javascript
import { defineConfig } from 'vite';
const commonjs = require('rollup-plugin-commonjs');
import uni from '@dcloudio/vite-plugin-uni';
export default defineConfig({
	plugins: [
		uni(),
		commonjs()
	],
});
```
Replace the corresponding content with the actual applicationName, applicationID, and BeaconURL. You can refer to [Create Custom Application](#create-custom-application) for more details.
#### Configure Request Legal Domains
Based on the BeaconURL domain obtained from [Create Custom Application](#create-custom-application), add it to the `request legal domains` in the `Server Domain` section of the WeChat Mini Program backend's `Development Settings`.

#### Define User and Action Properties
- Edit the Application and open `Session and user action properties`.
![](./docs/images/session%20and%20user%20action%20properties.jpg)
- Click `Add property` to add properties.
![](./docs/images/session%20and%20user%20action%20properties_1.jpg)


## API
### `init`
Initialize the beacon.
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});

monitor.init();
```
### `withApplicationSecureAndKey(key, secure)`

```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});

monitor.withApplicationSecureAndKey('', '');

monitor.init();
```

### `excludeApis(apis[])`
Exclude APIs that do not need to be monitored.
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});

monitor.excludeApis(['/api/v1/xxx', '/api/v1/xxx']);
monitor.init();
```
### `requestTimeout(timeout=60000)`
Set the timeout for all requests made by the current SDK.
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});

monitor.requestTimeout(6000);
monitor.init();
```

## zm （A Global Module）
### `identifyUser(userId)`
Mark the current session with a user ID.
```javascript
zm.identifyUser('xxxxx');
```
Example: Use openid as the user identifier
```javascript
wx.login({
    success: function(response){
        zm.identifyUser(response.data.openid);
    }
})
```

### `reportValue(name, value)`
Define `reportValue` in the current Page action. Refer to [Define User and Action Properties ](#define-user-and-action-properties)
```javascript
zm.reportValue('name', 'value');
```
Example: Report user login time
```javascript
let startTime = new Date().getTime();
wx.login({
    success: function(response){
        let endTime = new Date().getTime();
        zm.reportValue('用户登录耗时', (endTime - startTime) / 1000); //time in seconds
    }
})
```
<font color="red">Note: This API call `scope` is limited to onLoad, onShow, and onReady.</font>

## Dynamic Beacon Installation Example
- First, import the SDK for basic configuration.
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: 'Mini Program Version'
});
```
We did not call the initialization function above.

- Perform initialization

For example, suppose we need to call the backend API to obtain configuration information during `App` `onLaunch`. 

Let's assume that the JSON data returned by our backend looks like this.
```json
{
    "data": {
        "enableZmAgent": true,// Enable ZM Agent
        "license": {
            "key": "xxx",
            "secure": "xxx"
        }
    }
}
```
In the `App.js` code.
```javascript

App({

    onLaunch: function(){
        wx.request({
            success: function(response){
                const { data } = response;
                if(data && data.enableZmAgent){
                    const license = license;
                    if(license){
                        monitor.withApplicationSecureAndKey(license.key, license.secure);
                        monitor.init();
                    }
                }
            }
        })
    }
})

```
## End-to-End Configuration
### Install Dynatrace Beacon
- Log in to the Dynatrace backend management system. Open the menu `Manage` > `Deploy Dynatrace`.
![](./docs/images/deploy%20dynatrace.jpg)
<font color="red">Note: The beacon should be installed on the backend server of the Mini Program.</font>
- Showcase
![](./docs/images/end%20to%20end.png)
## Contact Us
[Shenzhen Zonefound Technology Co., Ltd.](https://www.zonefound.com.cn/)