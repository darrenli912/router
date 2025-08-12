# router
## armbian 
sudo apt-get update  
sudo apt install ipset  
sudo apt install vim  
## naiveproxy[下载linux_arm64，redir模式,listen 0.0.0.0]  
vim /etc/systemd/system/naive.service  

[Unit]   
Description=NaiveProxy Server Service  
After=network-online.target  

[Service]   
Type=simple  
User=nobody  
CapabilityBoundingSet=CAP_NET_BIND_SERVICE  
ExecStart=/usr/local/bin/naive /usr/local/etc/naive/config.json  

[Install]   
WantedBy=multi-user.target  


###启用服务  
systemctl enable naive.service  
systemctl start naive.service  
systemctl status naive.service  

分流  
ipset create cnlist hash:net  
curl -s https://raw.githubusercontent.com/mayaxcn/china-ip-list/master/chnroute.txt | while read ip; do  
  ipset add cnlist $ip  
done  


添加特殊网络ip  
ipset add cnlist 10.0.0.0/8  
ipset add cnlist 100.64.0.0/10   
ipset add cnlist 127.0.0.0/8  
ipset add cnlist 169.254.0.0/16  
ipset add cnlist 172.16.0.0/12  
ipset add cnlist 192.168.0.0/16  
ipset add cnlist 224.0.0.0/4  
ipset add cnlist 240.0.0.0/4  
ipset add cnlist 255.255.255.255/32  

持久化ipset  
sudo ipset save > /etc/ipset.conf  



透明代理  
命令加到rc.local  
/sbin/ipset restore < /etc/ipset.conf  

iptables -t nat -A OUTPUT -d {服务器ip} -j RETURN  
iptables -t nat -A OUTPUT -p tcp -m set --match-set cnlist dst -j RETURN  
iptables -t nat -A OUTPUT -p tcp -j REDIRECT --to-ports 1088  

iptables -t nat -A PREROUTING  -p tcp --dport 22 -j RETURN  
iptables -t nat -A PREROUTING -d {服务器ip} -j RETURN  
iptables -t nat -A PREROUTING -p tcp -m set --match-set cnlist dst -j RETURN  
iptables -t nat -A PREROUTING -s 192.168.50.0/24 -d 192.168.50.0/24 -j RETURN  
iptables -t nat -A PREROUTING -p tcp -j REDIRECT --to-ports 1088  

常用命令  
sudo iptables -t nat -L OUTPUT -n --line-numbers  
sudo iptables -t nat -D OUTPUT 2  
sudo iptables -t nat -D PREROUTING 2  

## dnscrypt-proxy[下载linux_arm64,listen 0.0.0.0]， 按wiki进行安装。
wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.8/dnscrypt-proxy-linux_arm64-2.1.8.tar.gz  
wiki  
https://github.com/DNSCrypt/dnscrypt-proxy/wiki/Installation-linux  
