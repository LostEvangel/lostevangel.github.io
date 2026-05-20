---
title: 使用 Xray + SSR 多级代理链路访问 ChatGPT
date: 2026-05-21 20:00:00
updated: 2026-05-21 20:00:00
tags: [Xray, SSR, 代理链, ChatGPT]
categories: 科学上网
toc: true
description: 通过本地 → HK(Xray) → US(SSR) 三级代理链路，仅分流 ChatGPT 流量走美国出口，其他流量直连。
---

## 背景

手上有两台服务器：一台香港服务器（低延迟直连），一台美国服务器（原生 IP）。需求很明确——访问 ChatGPT 时走美国 IP，其他流量从香港直出。

本文记录使用 **Xray**（香港） + **SSR**（美国）搭建多级代理链的完整过程。

## 架构

链路流向如下：

```
本地 SSR 客户端
  → HK :9999 (Xray SS inbound)
    → Xray 路由规则匹配
      ├── ChatGPT/OpenAI 域名 → :1081 (SSR local SOCKS5)
      │                         → US :443 (SSR Server)
      │                         → chatgpt.com ✅
      └── 其他流量 → direct (HK 直连)
```

核心思路：香港服务器跑 Xray，作为本地客户端的统一入口；Xray 通过域名规则分流，只有 ChatGPT 相关的域名走 SOCKS5 代理转发到美国 SSR 出口，其余流量直接从香港出。

## 准备工作

- 香港服务器：已安装 Xray 26.x
- 美国服务器：已运行 SSR Server（端口 443）
- 本地：SSR 客户端

## Xray 配置

Xray 的配置文件 `/usr/local/etc/xray/config.json`：

```json
{
  "log": {
    "loglevel": "debug"
  },
  "inbounds": [
    {
      "port": 9999,
      "protocol": "shadowsocks",
      "settings": {
        "method": "chacha20-ietf-poly1305",
        "password": "xray4chatgpt",
        "network": "tcp,udp"
      }
    }
  ],
  "outbounds": [
    {
      "tag": "direct",
      "protocol": "freedom"
    },
    {
      "tag": "us-proxy",
      "protocol": "socks",
      "settings": {
        "servers": [
          {
            "address": "127.0.0.1",
            "port": 1081
          }
        ]
      }
    }
  ],
  "routing": {
    "rules": [
      {
        "type": "field",
        "domain": [
          "chatgpt.com",
          "openai.com",
          "api.openai.com",
          "auth0.openai.com",
          "platform.openai.com",
          "chat.openai.com",
          "oaistatic.com",
          "oaiusercontent.com"
        ],
        "outboundTag": "us-proxy"
      },
      {
        "type": "field",
        "network": "tcp,udp",
        "outboundTag": "direct"
      }
    ]
  }
}
```

### 关键点说明

**inbounds**：Xray 监听 `0.0.0.0:9999`，使用 Shadowsocks 协议。本地 SSR 客户端协议选 `origin`、混淆选 `plain`，即可退化为标准 Shadowsocks 协议对接。

**outbounds**：
- `direct`：freedom 协议，直接出站
- `us-proxy`：SOCKS5 代理指向 `127.0.0.1:1081`，这个端口由 HK 上的 SSR local 客户端监听（该客户端连接到美国 SSR 服务器）

**routing**：域名规则匹配 ChatGPT/OpenAI 相关域名走 `us-proxy`；其余所有流量走 `direct`。

## 美国 SSR 客户端配置

在香港服务器上，需要运行一个 SSR local 客户端，将 SOCKS5 代理指向美国服务器。

配置文件示例（`us_local_config.json`）：

```json
{
    "server": "192.3.59.197",
    "server_port": 443,
    "local_address": "127.0.0.1",
    "local_port": 1081,
    "password": "your_password",
    "method": "aes-256-cfb",
    "protocol": "auth_aes128_md5",
    "obfs": "tls1.2_ticket_auth",
    "obfs_param": "www.qq.com",
    "timeout": 120
}
```

启动命令：

```bash
python3 shadowsocks/local.py -c us_local_config.json
```

## Xray 加密方式注意事项

Xray 26.x **不再支持**以下旧版 Shadowsocks 加密方式：

- `aes-256-cfb` / `aes-128-cfb`
- `aes-256-ctr`
- `rc4-md5`
- `chacha20` / `chacha20-ietf`（非 AEAD 版本）

仅支持 AEAD 加密：

| 加密方式 | 说明 |
|---------|------|
| `aes-256-gcm` | 推荐，性能好 |
| `aes-128-gcm` | 轻量级 |
| `chacha20-ietf-poly1305` | 兼容性较好 |

如果本地 SSR 客户端没有 `aes-256-gcm`，可以选择 `chacha20-ietf-poly1305`，大部分 SSR 客户端都支持。

## 本地客户端配置

本地 SSR 客户端连接香港服务器（Xray 的 SS inbound）：

| 参数 | 值 |
|------|-----|
| 服务器 | `38.47.100.211` |
| 端口 | `9999` |
| 加密方式 | `chacha20-ietf-poly1305` |
| 密码 | `xray4chatgpt` |
| 协议 | `origin` |
| 混淆 | `plain` |

协议必须选 `origin`、混淆选 `plain`，这样 SSR 客户端才能以标准 Shadowsocks 协议与 Xray 通信。

## 验证链路

在香港服务器上测试：

```bash
# 测试直连 ChatGPT（应返回 403 而非超时）
curl -s -o /dev/null -w "%{http_code}" https://chatgpt.com

# 测试通过 SOCKS5 代理请求 ChatGPT
curl -s -o /dev/null -w "%{http_code}" --socks5 127.0.0.1:1081 https://chatgpt.com
```

返回 `403` 是正常的（缺少浏览器请求头），只要不是连接超时或拒绝，说明链路通畅。

## 总结

这套方案的精髓在于 **"只分流需要代理的流量"**——ChatGPT 走美国出口，其他流量保持香港直连，延迟和带宽影响最小化。Xray 的域名路由规则可以随时扩展，如需增加其他走美国出口的服务，只需在 `routing.rules[0].domain` 数组中追加域名即可。
