PS C:\WINDOWS\system32> ssh root@100.121.119.6
Last login: Sat Feb  7 18:35:01 2026 from 100.65.31.106
[root@localhost ~]# ping 100.121.119.6
PING 100.121.119.6 (100.121.119.6) 56(84) バイトのデータ
64 バイト応答 送信元 100.121.119.6: icmp_seq=1 ttl=64 時間=0.050ミリ秒
64 バイト応答 送信元 100.121.119.6: icmp_seq=2 ttl=64 時間=0.080ミリ秒
64 バイト応答 送信元 100.121.119.6: icmp_seq=3 ttl=64 時間=0.077ミリ秒
64 バイト応答 送信元 100.121.119.6: icmp_seq=4 ttl=64 時間=0.104ミリ秒
64 バイト応答 送信元 100.121.119.6: icmp_seq=5 ttl=64 時間=0.105ミリ秒
64 バイト応答 送信元 100.121.119.6: icmp_seq=6 ttl=64 時間=0.115ミリ秒
64 バイト応答 送信元 100.121.119.6: icmp_seq=7 ttl=64 時間=0.082ミリ秒
^C
--- 100.121.119.6 ping 統計 ---
送信パケット数 7, 受信パケット数 7, 0% packet loss, time 6174ms
rtt min/avg/max/mdev = 0.050/0.087/0.115/0.020 ms
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# sysctl -a | grep ip_forward
net.ipv4.ip_forward = 1
net.ipv4.ip_forward_update_priority = 1
net.ipv4.ip_forward_use_pmtu = 0
[root@localhost ~]#
[root@localhost ~]# echo 1 > /proc/sys/kernel/osrelease
-bash: /proc/sys/kernel/osrelease: 許可がありません
[root@localhost ~]#
[root@localhost ~]# sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
[root@localhost ~]#
[root@localhost ~]# echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
1
[root@localhost ~]# #1の判定で有効にはなったのでもう1度echo 1 >で入力します。

項目名: [Troubleshooting] カーネルパラメータ変更時の権限エラー

内容: 「root権限でもリダイレクトで失敗した理由と、sysctl -w での解決」

[root@localhost ~]# lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
loop10        7:10   0   100M  0 loop
└─md0         9:0    0    99M  0 raid1
loop11        7:11   0   100M  0 loop
└─md0         9:0    0    99M  0 raid1
sda           8:0    0 476.9G  0 disk
├─sda1        8:1    0   600M  0 part  /boot/efi
├─sda2        8:2    0     1G  0 part  /boot
└─sda3        8:3    0 475.4G  0 part
  ├─rl-root 253:0    0    70G  0 lvm   /var/lib/containers/storage/overlay
  │                                    /
  ├─rl-swap 253:1    0   7.6G  0 lvm   [SWAP]
  └─rl-home 253:2    0 397.8G  0 lvm   /home
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# sudo pvs
  PV         VG Fmt  Attr PSize   PFree
  /dev/sda3  rl lvm2 a--  475.35g    0
[root@localhost ~]#
[root@localhost ~]# sudo vgs
  VG #PV #LV #SN Attr   VSize   VFree
  rl   1   3   0 wz--n- 475.35g    0
[root@localhost ~]#
[root@localhost ~]# sudo lvs
  LV   VG Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home rl -wi-ao---- <397.77g
  root rl -wi-ao----   70.00g
  swap rl -wi-ao----   <7.59g
[root@localhost ~]#先に'lvextend'を行い、'xfs_growfs'で拡張されます。
[root@localhost ~]# sudo mkdir /mnt/test
[root@localhost ~]#
[root@localhost ~]# mount -o ro
mount: bad usage
Try 'mount --help' for more information.
[root@localhost ~]# mount -o noexec
mount: bad usage
Try 'mount --help' for more information.
[root@localhost ~]# mount -o nosuid
mount: bad usage
Try 'mount --help' for more information.
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# mount -o ro /dev/mapper/rl-root
mount: /: /dev/mapper/rl-root already mounted on /.
       dmesg(1) may have more information after failed mount system call.
[root@localhost ~]#
[root@localhost ~]# mount -o noexec /dev/mapper/rl-root
[root@localhost ~]# mount -o nosuid /dev/mapper/rl-root
[root@localhost ~]#

「セキュリティ向上を求められた際、データ保存用のパーティションに noexec や nosuid を設定する理由は何か？」
改変されたり、悪用されないための対策です。

項目名: [Analysis] systemctl が使えない環境でのサービス管理
内容: 「systemd が不在の環境（コンテナ等）では systemctl は失敗する。その場合はバイナリを直接叩くか、ss コマンドでプロセスがポートを掴んでいるかを実測して判断する必要がある。」

firewalld が動いている場合、たとえ Apache が頑張っていても外からは見えません。下記は許可を与えるコマンドです。
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload



