# Change list

## **1.1.6**
- 新增接口 `reportValue(name, value)` 可用于日志监控。上报自定义内容。有效范围（<font color="red">仅限于 onLoad、onShow、onReady</font>）

## **1.1.0**
- 优化JS描点包体积 目前为（`105kb`）
- 删除 Action 中 SetData 事件。优化为 监控SetData 耗时 以及对应少部分数据捕获
- 新增接口 `excludeApis(apis[])` 用于添加不需要监控的API接口
- 新增 wx.request 的 timeout 配置。默认为 60000

## **1.0.0**
- 简化部署流程，无需要使用NPM 安装扩展依赖插件
- 识别用户操作（Open Page/Tap）
- Action 中 添加 SetData 事件触发记录
- 识别小程序的生命周期 并以 reportValue 方式展现到 Action 中
- 记录操作及页面加载响应时间 onshow、onready 时间
- 识别用户 IP地址及运营商 & 识别用户地理位置
- 识别用户设备信息（手机厂家及型号）
- 识别用户网络连接类型（2G/3G/4G/5G/WIFI）
- 监控服务端API调用URL
- 用户操作自动关联服务端API调用（端到端展示）
- 添加标记用户 Session  API `identifyUser(userId)`