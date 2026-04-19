# Hysteria 2 一键安装脚本

> 基于 [Misaka-blog/hysteria-install](https://github.com/Misaka-blog/hysteria-install) 二次优化，针对中国移动等运营商封锁场景深度加强。

[![GitHub](https://img.shields.io/badge/GitHub-lgdglgc%2FHysteria-blue?logo=github)](https://github.com/lgdglgc/Hysteria)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Hysteria2](https://img.shields.io/badge/Hysteria-2.x-orange)](https://github.com/apernet/hysteria)

---

## ⚡ 一键安装

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/lgdglgc/Hysteria/main/hysteria.sh)
```

或使用 wget：

```bash
bash <(wget -qO- https://raw.githubusercontent.com/lgdglgc/Hysteria/main/hysteria.sh)
```

> **需要 root 权限**，支持 Debian / Ubuntu / CentOS / Fedora 及其衍生发行版。

---
> **自用精简Clash模板**，
```bash
https://raw.githubusercontent.com/lgdglgc/Hysteria/main/my-clash.ini

```
---
## ✨ 优化特性

相比原版脚本，本脚本包含以下增强：

### 🛡️ 抗封锁优化（针对中国移动）

| 特性 | 说明 |
|------|------|
| **Salamander 混淆** | 自动启用 obfs salamander，混淆 QUIC 流量特征，绕过运营商 DPI 识别 |
| **高端口随机分配** | 默认随机端口范围 20000–50000，避开低端口封锁 |
| **端口跳跃（Port Hopping）** | 支持配置 UDP 端口跳跃范围，防止单端口持续曝光被封 |
| **跳跃间隔缩短** | `hopInterval` 从 30s 缩短至 10s，减少单端口暴露时间 |
| **QUIC 窗口调优** | 接收窗口从 16MB 扩展至 25MB/64MB，改善高延迟移动网络性能 |
| **带宽声明** | 服务端声明 1Gbps，客户端声明 80/200Mbps，防止 BBR 超报触发 QoS |
| **连接保活** | 新增 `keepAlivePeriod: 10s`，防止移动网络 NAT 超时断连 |

### 🔐 安全修复

- 移除危险的 `chmod -R 777 /root` 操作
- 自签证书权限修正：证书 `644`，私钥 `600`
- `changepasswd` 使用 `openssl rand` 生成高熵随机密码
- 修复 `changeproxysite` 误修改 Caddyfile 的 Bug

### 🔗 分享链接优化

- 端口跳跃模式：URI 使用 `mport` 参数携带端口范围，兼容主流客户端
- 混淆参数自动写入分享链接（`obfs=salamander&obfs-password=...`）

---

## 📋 功能菜单

```
#############################################################
#                  Hysteria 2 一键安装脚本优化版                  #
# 作者: SheepKeeper'S Blog                                   #
# 博客: https://blog.kqsdw.me                                #
# GitHub 项目: https://github.com/lgdglgc/Hysteria           #
# GitLab 项目: https://gitlab.com/lgdglgc/hysteria           #
#############################################################

 1. 安装 Hysteria 2
 2. 卸载 Hysteria 2
 -------------
 3. 关闭、开启、重启 Hysteria 2
 4. 修改 Hysteria 2 配置
 5. 显示 Hysteria 2 配置文件
 -------------
 6. 更新 Hysteria 2 内核
 -------------
 0. 退出脚本
```

---

## 🖥️ 支持系统

| 系统 | 版本要求 |
|------|----------|
| Debian | 9 及以上 |
| Ubuntu | 18.04 及以上 |
| CentOS | 7 及以上 |
| Fedora | 最新稳定版 |
| Amazon Linux | 2 及以上 |

---

## 📦 证书模式

安装时可选以下三种证书方式：

1. **必应自签证书**（默认）— 零配置，客户端需开启 `insecure: true`
2. **Acme 自动申请**（推荐）— 需要域名已解析到服务器 IP，自动续期
3. **自定义证书路径** — 使用已有证书文件

---

## 📁 生成的配置文件

安装完成后，以下文件保存于 `/root/hy/`：

| 文件 | 说明 |
|------|------|
| `hy-client.yaml` | Hysteria 2 客户端 YAML 配置 |
| `hy-client.json` | Hysteria 2 客户端 JSON 配置 |
| `clash-meta.yaml` | Clash Meta / Mihomo 配置 |
| `url.txt` | 节点分享链接（含端口跳跃） |
| `url-nohop.txt` | 节点分享链接（单端口） |

---

## 🔌 客户端推荐

| 平台 | 客户端 |
|------|--------|
| Windows | [NekoBox](https://github.com/MatsuriDayo/nekoray) / [v2rayN](https://github.com/2dust/v2rayN) |
| macOS | [ClashX Meta](https://github.com/MetaCubeX/ClashX.Meta) |
| Android | [NekoBox](https://github.com/MatsuriDayo/NekoBoxForAndroid) / [Clash Meta](https://github.com/MetaCubeX/ClashMetaForAndroid) |
| iOS | [Stash](https://apps.apple.com/app/stash/id1596063349) / [Shadowrocket](https://apps.apple.com/app/shadowrocket/id932747118) |
| 路由器 | [OpenClash](https://github.com/vernesong/OpenClash) |

---

## 📖 服务端配置示例

安装后生成的 `/etc/hysteria/config.yaml`：

```yaml
listen: :PORT

tls:
  cert: /path/to/cert.crt
  key: /path/to/private.key

obfs:
  type: salamander
  salamander:
    password: <随机生成>

quic:
  initStreamReceiveWindow: 26843545
  maxStreamReceiveWindow: 26843545
  initConnReceiveWindow: 67108864
  maxConnReceiveWindow: 67108864
  maxIdleTimeout: 30s
  keepAlivePeriod: 10s

bandwidth:
  up: 1 gbps
  down: 1 gbps

auth:
  type: password
  password: <随机生成>

masquerade:
  type: proxy
  proxy:
    url: https://maimai.sega.jp
    rewriteHost: true
```

---

## 🤝 致谢

- [apernet/hysteria](https://github.com/apernet/hysteria) — Hysteria 2 核心项目
- [Misaka-blog/hysteria-install](https://github.com/Misaka-blog/hysteria-install) — 原版一键安装脚本

---

## 📄 License

MIT License © 2024 [lgdglgc](https://github.com/lgdglgc)
