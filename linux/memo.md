## どこから書き込まれているか調べる
- ps auxf
- lsof
- fuser
- pwdx PID
- lsof -p PID

## 集計
- sort
- uniq -c

## serviceが動いているか
- sudo systemctl status postgresql
- sudo systemctl start postgresql

## エラーの確認
- journalctl -u postgresql
- journalctl -p err
- tail -f /var/log/syslog
- grep -i 'no space left' /var/log/syslog

## ディスク容量
- df -h
- du -sh /opt/pgdata/main

## ローカルファイアウォールの確認
- iptables -L
    - 現在のルールを表示
- iptables -F
    - ルールのflush

## ファイルの権限
- chown www-data:/var/www/html/index.html
    - /var/www/html/index.htmlの所有者、所有グループをwww-dataというユーザーに変更

## systemdでstatus確認
- systemctl status nginx

## nginx設定ファイル
- nginx -t

## プロセスが開くことのできるファイル数を確認
```
# ps axf | grep nginx
・・・
120161 ? S 0:02 \_ nginx: worker process

# cat /proc/120161/limits

Limit                     Soft Limit           Hard Limit           Units     
Max cpu time              unlimited            unlimited            seconds   
Max file size             unlimited            unlimited            bytes     
Max data size             unlimited            unlimited            bytes     
Max stack size            8388608              unlimited            bytes     
Max core file size        0                    unlimited            bytes     
Max resident set          unlimited            unlimited            bytes     
Max processes             15543                15543                processes 
Max open files            1024                 4096                 files
・・・
```

## limitを更新
```
# mkdir /etc/systemd/system/nginx.service.d
# vi /etc/systemd/system/nginx.service.d/limit.conf

[Service]
LimitNOFILE=32768

# systemctl daemon-reload
# systemctl restart nginx
```
