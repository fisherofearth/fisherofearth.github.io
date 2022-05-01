---
title: 设置git全局代理
date: 2022-04-30 22:26:15
tags: git
---

```
#!/bin/bash

# ------ User zone ---------
proxy_protocol="socks" # http/socks
proxy_server="your_proxy_server_address" 
proxy_port="10086"
# --------------------------

help="Fail: Bad argument. \n 
  Usage: ${0} <command> \n
  Command: \n \t set / unset \n"

if test $# -lt 1
then 
    echo -e ${help}
    exit 1
fi

if test $1 = "set"
then    
    proxy=${proxy_protocol}://${proxy_server}:${proxy_port}
    git config --global http.proxy ${proxy}
    git config --global https.proxy ${proxy}
    echo "http.proxy = "$(git config --global --get http.proxy)  
    echo "https.proxy = "$(git config --global --get https.proxy)
    exit 0
elif test $1 = "unset"   
then
    git config --global --unset http.proxy
    git config --global --unset https.proxy
    echo "Unset git proxy."
    exit 0
else
    echo -e ${help} 
    exit 1
fi
```