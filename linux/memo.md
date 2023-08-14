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
