## どこから書き込まれているか調べる
- ps auxf
- lsof
- fuser
    - 引数として渡されたファイルやファイルシステムのプロセスIDを確認
- pwdx PID
    - プロセスの作業ディレクトリを表示
- lsof -p PID

## 集計
- sort
- uniq -c

## serviceが動いているか
- sudo systemctl status postgresql
- sudo systemctl start postgresql

## エラーの確認
- journalctl -u postgresql
    - systemd-journaldが収集したログを表示するためのコマンド
    - -u service名でそのサービスのlogを出力
- journalctl -p err
    - 特定プライオリティ以上でフィルタ
- tail -f /var/log/syslog
    - rsyslogはsyslogより新しい
    - reliable syslog
    - tcp, 暗号化など
    - systemd環境ではjournaldがまずシステム上のログを受け取り、必要に応じてrsyslogへログを転送
- grep -i 'no space left' /var/log/syslog

## ディスク容量
- df -h
    - -hで--human-readable
    - ファイルシステムの使用状況
    - 全体の中でどこが多く使っているか調べる？
- du -sh /opt/pgdata/main
    - ファイルのディスク使用量を推定する
    - どのファイルがディスクを圧迫してるのか調べる？
    - -s ディレクトリの総計を表示
    - `| sort -nr | head -5`とか繋げば良さそう

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
