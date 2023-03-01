# 微信小程序监控

![](https://img.shields.io/badge/node-16.x-green) ![](https://img.shields.io/badge/npm-8.x-brightgreen) ![](https://img.shields.io/badge/code%20size-105%20kb-blue)

## 简介

## 功能介绍

### 用户行为分析
### 页面渲染速率分析
### 用户操作分析
### JS 错误监控
### 用户体验评分
### 地域运营商监控
### 操作系统信息分析
### 用户网络情况监控
### 
## 使用
### 创建 Custom Application
- 开始创建
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
const DYNATRACE = require('./utils/dynatrace-monitor-wx.js');
DYNATRACE('applicationName', 'applicationID', 'BeaconURL', '小程序当前版本').init();
```
根据实际的 applicationName 与 applicationID、BeaconURL 替换对应内容。可参考 [创建 Custom Application](#创建-custom-application)
#### 配置 request 合法域名
可根据 [创建 Custom Application](#创建-custom-application) 中获取的 BeaconURL 的域名地址，在微信小程序后台的 `开发设置` `>` `服务器域名` `>` `request合法域名` 中加入 BeaconURL 域名。

#### 定义用户属性与操作属性
- 编辑 Application 并打开 `Session and user action properties`
![](./docs/images/session%20and%20user%20action%20properties.jpg)

## API
### `init`
初始化 描点
```javascript
const DYNATRACE = require('./utils/dynatrace-monitor-wx.js');

const Monitor = DYNATRACE('applicationName', 'applicationID', 'BeaconURL', '小程序当前版本');

Monitor.init();
```
### `excludeApis(apis[])`
去除不需要监控的 Api
```javascript
const Monitor = DYNATRACE('applicationName', 'applicationID', 'BeaconURL', '小程序当前版本');
Monitor.excludeApis(['/api/v1/xxx', '/api/v1/xxx']);
Monitor.init();
```
### `identifyUser(userId)`
```javascript
getApp().identifyUser('xxxxx');
```
示例: 通过获取 openid 作为用户标识 
```javascript
wx.login({
    success: function(response){
        getApp().identifyUser(response.data.openid);
    }
})
```

### `reportValue(name, value)`
可在当前 Page action 里定义 reportValue 
```javascript
wx.GetCurrentPM().reportValue('xxxxx');
```
示例: 上报用户登录耗时
```javascript
let startTime = new Date().getTime();
wx.login({
    success: function(response){
        let endTime = new Date().getTime();
        wx.GetCurrentPM().reportValue('用户登录耗时', (endTime - startTime) / 1000);//time .s
    }
})
```

## 示例