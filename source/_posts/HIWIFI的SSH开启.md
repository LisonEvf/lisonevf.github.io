---
title: HIWIFI的SSH开启
date: 2023-6-7 11:43:42
tags: OpenWRT
---

# HIWIFI的API

## 1.获取uuid

> GET：http://www.4006024680.com/cgi-bin/turbo/proxy/router_info

取`json.data.uuid`

## 2.获取local_token

> GET: http://www.4006024680.com/local-ssh/api?method=get

取`json.data`

## 3.打开SSH

> GET: http://www.4006024680.com/local-ssh/api?method=valid&data=`[cloud_token]`

## 4.关闭SSH

> GET: http://www.4006024680.com/local-ssh/api?method=stop

## 5.检查SSH

> GET: http://www.4006024680.com/local-ssh/api?method=check_connected


---

# cloud_token 的计算

```javascript
// 'RDRFRTA3NjE3NTAwLHNzaCwxNTQwOTMxMzExOTk2LKrM8KmSbnrR8DzGobUdDx6097GH'
const local_token = '';
// 'af930766-ae38-528b-bcd2-d4ee07617500'
const uuid = '';

// 'D4EE07617500,ssh,1540931311996,ªÌð©\x92nzÑð<Æ¡µ\x1D\x0F\x1E´÷±\x87'
let local_token_msg = CryptoJS.enc.Base64.parse(local_token).toString(CryptoJS.enc.Latin1);

// ['D4EE07617500', 'ssh', '1540931311996']
let [mac_address, ssh, timestamp] = local_token_msg.split(',');

timestap = parseInt(timestamp)+1;

// 'D4EE07617500,ssh,1540931311997'
let cloud_token_msg = [mac_address, ssh, timestamp].join(',');

// 'tVyYDiwUvWZ4bIyFBh/EWEc3ndc='
let cloud_token = CryptoJS.enc.Base64.stringify(CryptoJS.HmacSHA1(cloud_token_msg,CryptoJS.SHA1(uuid)));

```

* [CryptoJS 3.1.2 下载](https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/crypto-js/CryptoJS%20v3.1.2.zip)