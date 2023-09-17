# adguardhome-ChinaList-updater
用于在OpenWRT上自动更新AdGuardHome的dnsmasq-china-list上游DNS配置文件的脚本
### 具体步骤
新建一个update_list.sh的文件，然后将以下代码复制进去，将代码自定义修改后扔进openwrt
```
#!/bin/sh

# 设置ChinaList.txt的目标路径（可自定义）
DESTINATION="/usr/bin/AdGuardHome/data/filters/ChinaList.txt"

# 下载文件，项目来源：https://github.com/mouyase/ChinaListForAdGuardHome
curl -o "$DESTINATION" https://adguard.yojigen.tech/ChinaList.txt

# 检查下载是否成功
if [ $? -eq 0 ]; then
    echo "ChinaList.txt 下载成功"
    
    # 在文件末尾添加额外DNS用于列表外域名解析（可自定义，建议设置成海外传统DNS，可有效避免DNS泄露）
    cat <<EOL >> "$DESTINATION"

8.8.8.8
1.1.1.1
9.9.9.11
45.11.45.11
208.67.222.222
EOL
    
    # 重新加载AdGuardHome以应用新的白名单
    /etc/init.d/AdGuardHome restart
else
    echo "ChinaList.txt 下载失败"
    exit 1
fi

exit 0

```
请确保将此脚本保存为可执行文件（修改权限），可以使用以下命令：
```
chmod +x update_list.sh
```
如需定时执行脚本，在终端输入
```
crontab -e
```
然后添加以下行，表示每天在凌晨2点运行脚本：
```
0 2 * * * /path/to/update_list.sh
```
记得将/path/to/update_list.sh替换为脚本的实际路径。保存并退出编辑器，cron将在每天的2点自动运行脚本来更新白名单文件。
