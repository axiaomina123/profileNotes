github代理配置与取消

配置代理

```
git config --global http.proxy 'socks5://127.0.0.1:1080' 
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

代理取消：

```
git config --global --unset http.proxy
git config --global --unset https.proxy
```

查看代理配置情况

```
git config --global --get http.proxy
git config --global --get https.proxy
```

