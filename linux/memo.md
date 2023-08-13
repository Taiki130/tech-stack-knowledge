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
