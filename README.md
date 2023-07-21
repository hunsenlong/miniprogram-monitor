# 微信小程序监控

![](https://img.shields.io/badge/node-16.x-green) ![](https://img.shields.io/badge/npm-8.x-brightgreen) ![](https://img.shields.io/badge/code%20size-96%20kb-blue)

[中文](./README.md) | [English](./README_EN.md)

## 简介
    从2017年微信首次推出小程序开始，经过四年多发展，截至2021年上半年，全网小程序数量突破700万个，呈逐年增长的趋势。

    Dynatrace的产品目前还没有支持微信小程序，没办法监控小程序的性能。为了弥补这方面的空白，我们开发了这个前端小程序探针—Wechat Miniprogram Monitoring。以便满足中国客户在这方面迫切的需求。

## 功能介绍

- ### **用户行为分析**
可视化分析小程序流量流向与性能，全面掌控用户来源渠道和用户转化。
- ### **页面渲染速率分析**
通过真实用户监控访问页面的展现速率（包括首次渲染时间、首次加载完成时间、卡顿次数等），基于问题定位指导优化。
- ### **用户操作分析**
告别繁琐埋点，自动识别网站操作点击事件监控操作次数、响应时间和可用性，同时监控用户操作流程，保障业务稳定运行。
- ### **JS 错误监控**
监控JS错误率趋势，同时可代码级定位JS错误所在页面或者脚本URL，出错的行列、堆栈等信息，同时还支持SourceMap，可还原压缩JS错误代码行列。
- ### **用户体验评分**
基于小程序性能的onReday、加载耗时、请求耗时、请求错误、JS错误、操作时间、操作可用性等多项核心指标进行多维度智能化评分，数字化衡量小程序性能。
- ### **地域运营商监控**
可分析不同区域（包括国家、省份、城市）、运营商以及接入方式（包括2G/3G/4G/WiFi）下的各关键网络性能指标，快速定位网络问题。
- ### **操作系统信息分析**
可获取用户设备信息，IP地址用于分析。
- ### **用户网络情况监控**
可实时监控当前用户的网络状态。
## 使用
### 创建 Custom Application
- 点击 `Create custom application` 开始创建
![](./docs/images/Create%20custom%20application.jpg)
- 输入 Application Name
![](./docs/images/Create%20custom%20application_1.jpg)
- 获取 `ApplicationID` 与 `BeaconURL`
![](./docs/images/Create%20custom%20application_2.jpg)
### 部署安装
#### JS描点安装
- 部署描点JS文件 `dynatrace-monitor-wx.js` 到 `utils` 目录下
- 在小程序的 入口文件 `app.js(ts)` 加入初始化代码
```javascript
//原生微信开发-部署
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: '小程序版本'
});
```
```javascript
//UNI微信开发-部署(Vue2)
import Monitor from "@/utils/dynatrace-monitor-wx.js"
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: '小程序版本'
});
```
<font color="red" size="4">*UNI vue3 安装描点 注意事项</font>
- uniapp 的 vue 3版本将不再支持 commonJS 语法，需要在vite.config.js 配置 commonjs
- 使用 npm 安装 rollup-plugin-commonjs
```cmd
npm i rollup-plugin-commonjs -D
```
- 在 vite.config.js 加入下面配置
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


根据实际的 applicationName 与 applicationID、BeaconURL 替换对应内容。可参考 [创建 Custom Application](#创建-custom-application)
#### 配置 request 合法域名
可根据 [创建 Custom Application](#创建-custom-application) 中获取的 BeaconURL 的域名地址，在微信小程序后台的 `开发设置` `>` `服务器域名` `>` `request合法域名` 中加入 BeaconURL 域名。

#### 定义用户属性与操作属性
- 编辑 Application 并打开 `Session and user action properties`
![](./docs/images/session%20and%20user%20action%20properties.jpg)
- 点击 `Add property` 进行添加
![](./docs/images/session%20and%20user%20action%20properties_1.jpg)


## API
### `init`
初始化 描点
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: '小程序版本'
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
  applicationVersion: '小程序版本'
});

monitor.withApplicationSecureAndKey('', '');

monitor.init();
```

### `excludeApis(apis[])`
去除不需要监控的 Api
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: '小程序版本'
});

monitor.excludeApis(['/api/v1/xxx', '/api/v1/xxx']);
monitor.init();
```
### `requestTimeout(timeout=60000)`
设置 当前SDK 的所有请求超时时间
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: '小程序版本'
});

monitor.requestTimeout(6000);
monitor.init();
```

## zm （一个全局模块）
### `identifyUser(userId)`
给当前 session 标记用户
```javascript
zm.identifyUser('xxxxx');
```
示例: 通过获取 openid 作为用户标识 
```javascript
wx.login({
    success: function(response){
        zm.identifyUser(response.data.openid);
    }
})
```

### `reportValue(name, value)`
可在当前 Page action 里定义 reportValue。 参考 [定义用户属性与操作属性](#定义用户属性与操作属性)
```javascript
zm.reportValue('name', 'value');
```
示例: 上报用户登录耗时
```javascript
let startTime = new Date().getTime();
wx.login({
    success: function(response){
        let endTime = new Date().getTime();
        zm.reportValue('用户登录耗时', (endTime - startTime) / 1000); //time .s
    }
})
```
<font color="red">注意：该API 调用 `scope` 仅限于 onLoad、onShow、onReady</font>

## 动态安装描点案例
- 首先引入SDK进行基础性配置
```javascript
const Monitor = require('./utils/dynatrace-monitor-wx.js');
const monitor = Monitor({
  applicationName: 'applicationName',
  applicationID: 'applicationID',
  endpointURL: 'BeaconURL',
  applicationVersion: '小程序版本'
});
```
上面我们没有进行初始化函数调用

- 进行初始化
比如我需要在 `App` `onLaunch` 的时候调用后端API接口获取配置信息
假设我们后端返回的JSON数据如下
```json
{
    "data": {
        "enableZmAgent": true,// 开启 ZM Agent
        "license": {
            "key": "xxx",
            "secure": "xxx"
        }
    }
}
```
在 `App.js` 代码中
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
## 端到端配置
### 安装 Dynatrace 探针
- 登录Dynatrace后台管理系统。打开菜单 `Manage` > `Deploy Dynatrace`
![](./docs/images/deploy%20dynatrace.jpg)
<font color="red">注意：需要安装在 小程序后台服务 操作系统</font>
- 效果展示
![](./docs/images/end%20to%20end.png)
## 联系我们
[深圳市卓越方达科技有限公司](https://www.zonefound.com.cn/)