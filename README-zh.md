# v2rayA DNS 缓存 macOS 测试包

源码提交：987bd00300ba07f0ff446e49826315ea61f97e99
服务和核心版本：0.0.0-dns-cache.987bd003

## 包含内容

- v2raya：内嵌完整网页界面的服务
- v2raya_core：同版本的核心
- assets/geoip.dat 与 assets/geosite.dat：已校验的规则数据
- start.command：本机 lite 模式启动脚本
- LICENSE 与 CORE-LICENSE：项目许可证
- SHA256SUMS.txt：包内文件的校验值

这是 Linux 云端交叉编译的测试包，尚未在真实 macOS 机器运行。
Apple Silicon 使用 arm64 包；Intel 使用 amd64 包。最低系统版本为 macOS 12 Monterey。

## 启动

1. 解压整个目录，保留两个程序和 assets 文件夹。
2. 在终端进入解压目录，执行：

   ```sh
   shasum -a 256 -c SHA256SUMS.txt
   bash ./start.command
   ```

   也可以双击 start.command。不要使用 sudo。
3. 浏览器打开 http://127.0.0.1:2017，创建测试管理员账户。
4. 打开“设置”页的“DNS 设置”部分，可查看并调整缓存、预取、负响应缓存、缓存大小和最小/最大 TTL，点击“保存并应用”。
5. Ctrl+C 停止。再次启动会保留测试设置。

包未经 Apple 公证。若 macOS 拦截程序，在核对下载包及内部校验值后，可在“系统设置 → 隐私与安全性”中允许程序运行；或仅对本包两个已验证的程序移除下载隔离标记：

```sh
xattr -d com.apple.quarantine ./v2raya ./v2raya_core
```

测试配置默认保存在：

```text
~/Library/Application Support/v2raya-dns-cache-test-987bd003/
```

持久化数据库是该目录下的 v2raya.db，不使用现有正式实例的配置目录。
如端口 2017 已被占用，请先退出其他实例或在 start.command 中改为另一端口。

## 测试范围

本包支持普通用户运行服务、界面、DNS 缓存设置，以及配置节点后的 SOCKS/HTTP 代理测试。
要做真实 DNS 缓存查询，需先配置可用节点和 DNS 上游并启动核心。
默认 DNS 模块在未开启端口共享和 IP 转发时监听 127.0.0.1:52353；以启动日志和生成配置为准。
macOS 自带 dig，可针对已配置上游支持的域名重复查询：

```sh
dig @127.0.0.1 -p 52353 example.com A +stats
dig @127.0.0.1 -p 52353 example.com A +stats
dig @127.0.0.1 -p 52353 example.com A +stats
```

比较 Query time，同时确认回复正确；时间缩短本身不能证明缓存命中。
默认 DNS 规则里的 localhost 需要本机有 DNS 解析器监听；没有时应使用可用上游。
本包以 lite 模式启动；TUN 透明代理需要单独的管理员权限和网络配置，不属于此次测试。

## 已完成验证

- GUI lint、typecheck、i18n-check、226 项测试、生产构建
- Linux 服务和核心 build、vet
- Linux 浏览器：桌面/手机宽度及浅色/深色主题
- 独立测试实例中通过设置界面及 API 持久化三个开关，保留大小及 TTL
- 服务测试仅剩仓库已注明的非 root 权限失败项 TestResolvHijackerConcurrentResetRemove
- macOS 二进制的架构和内嵌版本静态检查；真实 macOS 执行待用户测试

源码和依赖版本见 GitHub 此测试发布对应提交；从源码构建需 Node 24、Yarn Classic 和 Go 1.26。
