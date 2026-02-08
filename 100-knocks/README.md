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



