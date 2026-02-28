---
layout: post
title:  "clash+Cloudflare WARP隐藏你的出口ip"
date:   2026-03-01 01:16:48 +0800
categories: network
---

# 前言

简单记录一下，clash+warp隐藏出口ip。

具体效果如下，注意warp是on

```bash

$ curl --socks5-hostname 127.0.0.1:7891 https://cloudflare.com/cdn-cgi/trace

fl=975f10
h=cloudflare.com
ip=104.28.215.67
ts=1772297361.674
visit_scheme=https
uag=curl/8.7.1
colo=HKG
sliver=none
http=http/2
loc=HK
tls=TLSv1.3
sni=plaintext
warp=on
gateway=off
rbi=off
kex=X25519


```



# 准备工具

1、mihomo (Clash Meta)

目前还在维护的版本，更名为mihomo

https://github.com/vernesong/mihomo

2、wgcf

https://github.com/ViRb3/wgcf

3、梯子

# 编辑配置

第一步、生成warp公私钥

```bash
# 1. 注册一个新设备，这会生成一个 wgcf-account.toml 文件
./wgcf register

# 2. 生成WireGuard格式的配置文件，这会生成一个 wgcf-profile.conf 文件
./wgcf generate
```

打开wgcf-profile.conf 准备后面使用。



第二步、找到自己的clash配置文件config.yaml。



```yaml
# 编辑proxies
# 注意，前置节点，尽量选一个稳定的。而且一定要支持udp的。

- name: warp
    type: wireguard
    server: engage.cloudflareclient.com
    port: 2408
    ip: "172.16.0.2/32"
    ipv6: "2606:x/128" #自行修改
    private-key: "private-key" #自行修改
    public-key: "public-key" #自行修改
    udp: true
    #reserved: "18l3"
    mtu: 1280
    dialer-proxy: "前置节点" #自行修改
    remote-dns-resolve: true
    dns:
      - https://dns.cloudflare.com/dns-query


```



最后保存，重启。然后切换到新增的warp节点。即可。











