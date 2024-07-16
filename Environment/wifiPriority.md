## 配置wifi网络优先级
#### 查看当前所有网络优先级
```
nmcli -f autoconnect-priority,name c
```
#### 调整网络优先级(这里改成10)
```
nmcli c mod "$WifiName$" conn.autoconnect-p 10
```
#### 重启网络服务
```
sudo systemctl restart NetworkManager.service
```