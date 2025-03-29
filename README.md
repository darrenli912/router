# router
armbian + naiveproxy[下载linux_arm64,listen 0.0.0.0] 

iptables -t nat -A OUTPUT -d {服务器ip} -j RETURN
iptables -t nat -A OUTPUT -p tcp -j REDIRECT --to-ports 1088
iptables -t nat -A PREROUTING  -p tcp --dport 22 -j RETURN
iptables -t nat -A PREROUTING -d {服务器ip} -j RETURN
iptables -t nat -A PREROUTING -s 192.168.50.0/24 -d 192.168.50.0/24 -j RETURN
iptables -t nat -A PREROUTING -p tcp -j REDIRECT --to-ports 1088

常用命令
sudo iptables -t nat -L OUTPUT -n --line-numbers
sudo iptables -t nat -D OUTPUT 2
sudo iptables -t nat -D PREROUTING 2

dnscrypt-proxy[下载linux_arm64,listen 0.0.0.0]， 按wiki进行安装。
