该项目参考并基于

https://github.com/Turris-Babel/school_ruijie

项目进行修改

主要添加了service参数的选择，目前service参数用于区分学生和教师认证,再次感谢Turris-Babel的代码支持！

广东东软学院锐捷校园网认证辅助脚本 (Ruijie Portal Auth)

一个用于锐捷校园网 ePortal Web 认证的命令行客户端，支持多平台运行，无需浏览器即可完成认证。

✨ 特性

✅ 多平台支持：Windows、Linux、macOS、OpenWrt (ARMv7/ARMv8)

✅ 双身份认证：支持学生/教师身份选择

✅ 持久化登录：断线自动重连，适合路由器部署

✅ 灵活配置：支持自动检测和手动指定认证地址

✅ 轻量高效：无依赖，单文件运行

# 学生身份登录（默认）
./ruijie -u 学号 -p 密码 -m "认证页面URL"

# 教师身份登录
./ruijie -u 工号 -p 密码 -s Teacher -m "认证页面URL"

# 持久化登录（每10秒检测一次）
./ruijie -u 学号 -p 密码 -m "认证页面URL" -e

Windows 示例

ruijie_windows_amd64.exe -u 20240001 -p 123456 -m "http://172.17.211.2/eportal/index.jsp?wlanuserip=..."

Linux/OpenWrt 示例

./ruijie_linux_amd64 -u 20240001 -p 123456 -m "http://172.17.211.2/eportal/index.jsp?wlanuserip=..."

macOS 示例

./ruijie_macos_amd64 -u 20240001 -p 123456 -m "http://172.17.211.2/eportal/index.jsp?wlanuserip=..."
📋 命令行参数
参数	说明	默认值	必需
-u	认证用户名	-	✅ 是

-p	认证密码	-	✅ 是


-s	服务类型：default(学生) / Teacher(教师)	default	❌ 否
-c	运营商代码	空	❌ 否

-m	完整认证页面URL	-	⚠️ 自动检测失败时必需

-e	启用持久化登录模式	false	❌ 否

-h	显示帮助信息	-	❌ 否

参数详解

-m 认证页面URL

这是最重要的参数。你可以通过以下方式获取：

浏览器访问：在已连接校园网但未认证的设备上，打开浏览器访问任意网站，会自动跳转到认证页面

复制地址栏URL：从地址栏复制完整的认证页面URL（包含 wlanuserip、wlanacname 等参数）

手动输入：如果自动检测失败，程序会提示你手动输入

示例URL格式：

http://172.17.211.2/eportal/index.jsp?wlanuserip=1c654cb25b576d10...&wlanacname=...&ssid=&nasip=...&mac=...

🔧 编译方法
前提条件
安装 Go 1.16 或更高版本

Go 官方下载地址

Windows 编译
batch
@echo off
set GOOS=windows
set GOARCH=amd64
set CGO_ENABLED=0
go build -ldflags="-s -w" -o ruijie_windows_amd64.exe main.go

Linux (x86_64) 编译
bash
GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -ldflags="-s -w" -o ruijie_linux_amd64 main.go

Linux (ARMv8 / 64位) 编译
适用于：树莓派 3/4、RK3399、OpenWrt 64位
bash
GOOS=linux GOARCH=arm64 CGO_ENABLED=0 go build -ldflags="-s -w" -o ruijie_linux_arm64 main.go

Linux (ARMv7 / 32位) 编译
适用于：树莓派 2、旧款路由器、OpenWrt 32位
bash
GOOS=linux GOARCH=arm GOARM=7 CGO_ENABLED=0 go build -ldflags="-s -w" -o ruijie_linux_armv7 main.go

macOS (Intel) 编译
bash
GOOS=darwin GOARCH=amd64 CGO_ENABLED=0 go build -ldflags="-s -w" -o ruijie_macos_amd64 main.go

macOS (Apple Silicon) 编译
bash
GOOS=darwin GOARCH=arm64 CGO_ENABLED=0 go build -ldflags="-s -w" -o ruijie_macos_arm64 main.go


📱 平台部署指南

OpenWrt 路由器部署

1. 确定设备架构

# SSH登录OpenWrt后执行
uname -m
# aarch64 -> 使用 ruijie_linux_arm64
# armv7l   -> 使用 ruijie_linux_armv7
# x86_64   -> 使用 ruijie_linux_amd64

2. 上传文件

3. 赋予执行权限并测试

chmod +x /root/ruijie_linux_arm64
/root/ruijie_linux_arm64 -h

4. 设置开机自启
创建 /etc/init.d/ruijie：

#!/bin/sh /etc/rc.common

START=99
STOP=10

# 请修改以下配置

USERNAME="你的学号"
PASSWORD="你的密码"
SERVICE_TYPE="default"  # 或 Teacher
AUTH_URL="http://172.17.211.2/eportal/index.jsp?wlanuserip=..."

start() {
    echo "Starting Ruijie authentication..."
    /root/ruijie_linux_arm64 -u $USERNAME -p $PASSWORD -s $SERVICE_TYPE -m "$AUTH_URL" -e > /root/ruijie.log 2>&1 &
    echo "Ruijie authentication started"
}

stop() {
    echo "Stopping Ruijie authentication..."
    killall ruijie_linux_arm64
    echo "Ruijie authentication stopped"
}

restart() {
    stop
    sleep 2
    start
}

status() {
    if pgrep -x "ruijie_linux_arm64" > /dev/null; then
        echo "✅ Ruijie authentication is running"
    else
        echo "❌ Ruijie authentication is not running"
    fi
}
启用并启动：

bash
chmod +x /etc/init.d/ruijie
/etc/init.d/ruijie enable
/etc/init.d/ruijie start
/etc/init.d/ruijie status
树莓派部署
与 OpenWrt 类似，下载对应架构的二进制文件即可。

Linux 服务器部署
bash
# 上传并赋予权限
chmod +x ruijie_linux_amd64

# 后台运行
nohup ./ruijie_linux_amd64 -u 学号 -p 密码 -m "认证URL" -e > ruijie.log 2>&1 &

# 查看日志
tail -f ruijie.log
macOS 部署
bash
# 打开终端，进入文件所在目录
chmod +x ruijie_macos_amd64
./ruijie_macos_amd64 -u 学号 -p 密码 -m "认证URL"
🔍 常见问题
Q1: 如何获取认证页面URL？
方法一：在连接校园网但未认证的设备上，用浏览器访问 http://www.baidu.com，地址栏会自动跳转到认证页面，复制完整URL即可。

方法二：直接访问认证服务器IP，通常是 http://172.17.211.2 或 http://10.0.0.1。

方法三：查看网络网关地址（Windows: ipconfig，Linux: route -n）。

Q2: 提示"无法获取认证页面地址"怎么办？
使用 -m 参数手动指定完整的认证页面URL。

Q3: 认证成功但无法上网？
检查 queryString 是否完整（URL中应包含 wlanuserip、wlanacname 等参数）

确认服务类型是否正确 (-s default 或 -s Teacher)

尝试手动刷新DHCP：udhcpc -i br-lan (OpenWrt)

Q4: 不同学校的参数值不同怎么办？
参考各学校的具体配置，主要差异在于：

service 参数值：default/student/stu 等

认证服务器地址

参数名称：userId/username/account 等

建议通过抓包确认具体参数值。

Q5: 如何验证设备架构？
bash
# Linux/OpenWrt
uname -m

# 输出对应关系：
# aarch64  -> ARMv8 (64位)
# armv7l   -> ARMv7 (32位)
# x86_64   -> Intel/AMD 64位
# mips     -> MIPS架构
Q6: OpenWrt 上提示 "not found"？
检查文件架构是否正确：

bash
file /root/ruijie_linux_arm64
# 应显示: ELF 64-bit LSB executable, ARM aarch64
Q7: 需要验证码怎么办？
当前版本不支持验证码。如果你的学校强制要求验证码，建议：

使用浏览器手动认证

或等待后续版本更新

📝 配置示例
常见学校配置速查
学校	认证地址	学生 service	教师 service
示例大学	172.17.211.2	default	Teacher
其他大学1	10.0.0.1	student	teacher
其他大学2	192.168.1.1	stu	tea
如何添加你的学校配置：通过抓包获取认证页面URL和参数值。

📄 许可证
MIT License

🤝 贡献
欢迎提交 Issue 和 Pull Request！

如果你适配了新的学校，欢迎分享配置信息。
