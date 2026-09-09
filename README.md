该项目参考并基于

https://github.com/Turris-Babel/school_ruijie

项目进行修改

主要添加了service参数的选择，目前service参数用于区分学生和教师认证,再次感谢Turris-Babel的代码支持！

# 广东东软学院锐捷校园网认证辅助脚本 (Ruijie Portal Auth)
一个用于锐捷校园网 ePortal Web 认证的命令行客户端，支持多平台运行，无需浏览器即可完成认证。

## ✨ 特性
- ✅ **多平台支持**：Windows、Linux、macOS、OpenWrt (ARMv7/ARMv8)
- ✅ **双身份认证**：支持学生/教师身份选择
- ✅ **持久化登录**：断线自动重连，适合路由器部署
- ✅ **灵活配置**：支持自动检测和手动指定认证地址
- ✅ **轻量高效**：无依赖，单文件运行

## 📥 下载预编译版本
从 Releases 页面下载对应平台的二进制文件：

| 文件名 | 适用平台 | 架构 |
|--------|----------|------|
| ruijie_windows_amd64.exe | Windows | x86_64 |
| ruijie_linux_amd64 | Linux | x86_64 |
| ruijie_macos_amd64 | macOS | Intel |
| ruijie_macos_arm64 | macOS | Apple Silicon |
| ruijie_linux_arm64 | OpenWrt/树莓派 | ARMv8 (64位) |
| ruijie_linux_armv7 | OpenWrt | ARMv7 (32位) |

## 🚀 快速开始
### 基本用法
```bash
# 学生身份登录（默认）
./ruijie -u 学号 -p 密码 -m "认证页面URL"

# 教师身份登录
./ruijie -u 工号 -p 密码 -s Teacher -m "认证页面URL"

# 持久化登录（每10秒检测一次）
./ruijie -u 学号 -p 密码 -m "认证页面URL" -e

