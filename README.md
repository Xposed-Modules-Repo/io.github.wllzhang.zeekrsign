# 极氪签到助手

<img src="docs/images/icon.svg" width="96" alt="极氪签到助手图标">

打开极氪 App 时自动确认当天签到，领取可领取的能量球、极值球和已适配奖励。无需后台定时任务，也无需电脑常连。本项目是个人开发的 LSPosed 模块，与极氪官方无关联。

**当前版本：1.4，仅适配极氪 App 5.0.7。**

[下载 APK](https://github.com/wllzhang/zeekr-auto-sign/releases/latest) · [LS 社区仓库](https://github.com/Xposed-Modules-Repo/io.github.wllzhang.zeekrsign) · [源代码](https://github.com/wllzhang/zeekr-auto-sign) · [反馈问题](https://github.com/wllzhang/zeekr-auto-sign/issues)

## 功能

- 打开极氪主界面后自动执行签到和领取。
- 按账号与上海日期缓存已确认签到，跳过模块自身的重复签到请求；当天仍会查询新增奖励。
- 已签到且没有奖励时静默跳过，不显示“加 0”，也不新增成功零领取记录。
- 首次确认当天签到、实际领取或失败时，在极氪界面顶部显示提示条，约 8 秒后收起，也可点击关闭。
- 在 LSPosed 模块设置或桌面入口查看今日和累计领取、签到确认天数、最近 20 次有效执行记录。
- 复查领取结果，遇到未知奖励类型保留该奖励并提示未领完。

## 安装

需要已配置并能正常运行模块的 LSPosed 环境。已实机验证 Android 16、KernelSU 3.3.0、ZygiskNext 1.5.0、LSPosed 2.2.1（7912）与极氪 5.0.7；其他环境尚未验证。

1. 从 [Releases](https://github.com/wllzhang/zeekr-auto-sign/releases/latest) 下载并安装 APK。
2. 在 LSPosed 中启用“极氪签到助手”，作用域仅勾选极氪（`com.zeekrlife.mobile`）。
3. 完全关闭极氪并重新打开，保持登录且网络可用。
4. 在 LSPosed 模块详情打开模块设置，或使用桌面“极氪签到助手”入口查看统计。

公开版包名为 `io.github.wllzhang.zeekrsign`。如果装过包名为 `com.william.zeekrsign` 的测试版，请停用旧模块后启用公开版，避免重复执行；测试版统计不会自动迁移。

停用：在 LSPosed 中关闭模块，随后完全关闭并重新打开极氪。

## 执行与统计

主界面恢复后启动自动流程。同一进程内至少间隔 5 分钟，新进程首次打开会触发。领取最多复查 3 轮，单个奖励最多尝试 2 次，整个流程最多运行 90 秒。

统计保存在模块私有 SQLite 数据库中，覆盖升级保留，卸载删除。统计从安装启用后开始，累计汇总本机所有账号的执行，不按账号分组。成功且全部领取量为零时只保存签到确认日期，不新增执行记录；旧版成功零领取记录也不会展示。

## 实现与适配限制

模块复用极氪原有 WebActivity、网页登录状态、请求客户端与风险 SDK。登录凭证保持在原 App 页面内，不导出或保存；正式版不启用 WebView 调试。自动流程会临时打开透明、不可触摸的官方签到页面，结束后关闭，期间系统状态栏可能短暂变化。

已实机验证绿色能量球与蓝色极值球，测试阶段实际领取过 1236g 能量和 7 极值，并复查待领取列表为空。碎片、七日连签和生日奖励依据当前官方网页代码适配，尚未实机验证。首次跨日签到仍需日常使用验证。

App 版本和网页导出方法会校验；App 或官方网页更新后可能需要重新适配。未登录、断网、接口失败或超时会提示未完成，不记录为成功。官方网页自身初始化仍可能请求签到中心接口，因此模块缓存不等于整个 App 完全不再发送该请求。

## 构建与验证

Windows PowerShell，安装 JDK、Android SDK Platform 35、Build Tools 35.0.1，以及用于离线验证的 Node.js。无需 Gradle。

下载 [Xposed API 82](https://api.xposed.info/de/robv/android/xposed/api/82/api-82.jar) 到 `tools/xposed-api-82.jar`。该依赖仅用于编译，不打包进 APK。

```powershell
node tests/auto-flow.test.js
.\build.ps1 -Sdk 'D:\sdk' -Jdk 'C:\path\to\jdk'
```

输出为 `build/zeekr-sign.apk`。脚本会在本地创建开发签名，请保留 `build/development.jks` 以便覆盖安装自己构建的版本。自己构建的 APK 与公开发行 APK 签名不同，不能直接互相覆盖安装。

离线验证覆盖 10 个场景：空列表、延迟新增奖励、失败重试上限、查询失败、畸形数据、签到失败、未知奖励、七日奖励路由、已签到时跳过签到调用、已签到后仍领取新增奖励。

`analysis/`、`build/`、`tools/`、APK 和签名密钥不会提交到源码仓库。发行 APK 通过 GitHub Releases 提供。

## 打赏支持

如果这个模块帮到了你，欢迎自愿打赏支持维护。微信 / 支付宝：

<img src="docs/images/donate.jpg" width="720" alt="微信和支付宝打赏二维码">

## 许可证

[MIT License](LICENSE)
