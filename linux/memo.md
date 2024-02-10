## どこから書き込まれているか調べる
- ps auxf
- lsof
- fuser
    - 引数として渡されたファイルやファイルシステムのプロセスIDを確認
- pwdx PID
    - プロセスの作業ディレクトリを表示
- lsof -p PID

### プロセスのファイルディスクリプタを確認してみる
```
ls -l /proc/<pid>/fd
```
標準出力を吸い込んで見る
```
cat /proc/<pid>/fd/1
```
プロセスのsyscallやsignalを確認する
```
strace -t -p <pid>
```
設定ファイルを再読み込みさせる（nginxならHUP signal, apacheならUSR1 signal）
どこに設定ファイルがあるかわかるかも
```
kill -HUP <pid>
```

### 他のサーバを確認する
```
## キャッシュ内容を確認する
$ arp -a
ip-172-31-16-1.ap-northeast-1.compute.internal (172.31.16.1) at 06:d0:4e:xx:xx:xx [ether] on eth0
## ARPスキャンする（arp-scan コマンドは別途インストールが必要）
### ARPスキャンの範囲（サブネット）を計算する
$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.24.219  netmask 255.255.240.0  broadcast 172.31.31.255
### 上記よりサブネット(ネットワークアドレス/CIDR) は 172.31.16.0/20

$ sudo arp-scan 172.31.16.0/20
Interface: eth0, datalink type: EN10MB (Ethernet)
Starting arp-scan 1.9.2 with 4096 hosts (http://www.nta-monitor.com/tools-resources/security-tools/arp-scan/)
172.31.16.1 06:d0:4e:xx:xx:xx (Unknown)
172.31.26.132 06:4e:7e:xx:xx:xx (Unknown)
172.31.29.38  06:8b:fe:xx:xx:xx (Unknown)

### TCP コネクションやリッスンしている TCP/UDP ポートを確認
$ sudo netstat -anp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      3214/sshd
tcp        0      0 172.31.24.219:22        xxx.xx.xxx.xxx:58168    ESTABLISHED 32051/sshd: USERNAME
tcp6       0      0 :::22                   :::*                    LISTEN      3214/sshd
tcp6       0      0 :::80                   :::*                    LISTEN      3003/docker-proxy

## Docker コンテナとの通信は iptables(nftables) で NATされている
## NAT の設定を確認してみる
$ sudo iptables -L
Chain FORWARD (policy DROP)
target     prot opt source               destination
DOCKER     all  --  anywhere             anywhere

Chain DOCKER (1 references)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             ip-172-17-0-2.ap-northeast-1.compute.internal  tcp dpt:http

## iptables/nftables によって NAT されている TCP コネクションは、 procfs で ip_conntrack または nf_conntrack から確認出来る
$ sudo cat /proc/net/nf_conntrack
ipv4     2 tcp      6 431997 ESTABLISHED src=xxx.xx.xxx.xxx dst=172.31.24.219 sport=57245 dport=80 src=172.17.0.2 dst=xxx.xx.xxx.xxx sport=80 dport=57245 [ASSURED] mark=0 zone=0 use=2
ipv4     2 tcp      6 103 TIME_WAIT src=172.17.0.2 dst=xx.xx.xx.xxx sport=46684 dport=8080 src=xx.xx.xx.xxx dst=172.31.24.219 sport=8080 dport=46684 [ASSURED] mark=0 zone=0 use=2

## 1行目は、 TCP 80 ポートへのアクセスが Docker コンテナに NAT されているコネクション
## 2行目は、 Docker コンテナ内から外部ホストの TCP 8080 ポートへのコネクションの NAT 
```

## サーバの様子を詳しく見る
### procfs
https://www.kimullaa.com/posts/201912142251/

### /var/log/{syslog,messages}
メモリ不足時にプロセスを強制終了させる OOM Killer が動作すると記録が残る

### free, top, vmstat, ps
ps -L で LWP （スレッド）も表示出来る
```
## PID: プロセス番号, LWP: LWP(スレッド)ID, NLWP: LWP(スレッド)数
$ ps -efL
UID        PID  PPID   LWP  C NLWP STIME TTY          TIME CMD
root      3564     1  3564  0    8 02:24 ?        00:00:00 /usr/libexec/amazon-ecs-init start
root      3564     1  3567  0    8 02:24 ?        00:00:00 /usr/libexec/amazon-ecs-init start
root      3564     1  3568  0    8 02:24 ?        00:00:00 /usr/libexec/amazon-ecs-init start
（省略）
```

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

## ファイルディスクリプタ調べ、プロセスをkillすることなく閉じる
- https://zenn.dev/mom0tomo/articles/1ec9a644daabcf
    - execを使うと、現在のシェルのリダイレクト先を変更できる
    - &-はclose
    - exec 77>&-
        - ファイルディスクリプタ番号77をclose
