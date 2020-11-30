# testHtml

    一、安装epel-release，ppp，pptpd软件
    yum install -y epel-release ppp pptpd
    
    二、配置
    1.设置内网IP段
    vi /etc/pptpd.conf
    
    localip 192.168.0.1
    192.168.0.234-238,192.168.0.245
    
    2.更改DNS项
    vi /etc/ppp/options.pptpd
    
    ms-dns 8.8.8.8
    ms-dns 8.8.4.4
    
    3.配置账号
    vi /etc/ppp/chap-secrets
    
    username pptpd password *
    
    4.配置转发
    vi /etc/sysctl.conf
    
    #配置后输入命令生效: sysctl -p
    net.ipv4.ip_forward = 1
    
    三、防火墙
    1.允许防护墙伪装IP
    firewall-cmd --add-masquerade --permanent
    
    2.开放端口
    firewall-cmd --zone=public --add-port=47/tcp --permanent
    firewall-cmd --zone=public --add-port=1723/tcp --permanent
    
    3.允许gre协议
    firewall-cmd --permanent --direct --add-rule ipv4 filter INPUT 0 -p gre -j ACCEPT
    firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 0 -p gre -j ACCEPT
    
    4.设置规则允许数据包由eth0和ppp+接口中进出
    firewall-cmd --permanent --direct --add-rule ipv4 filter FORWARD 0 -i ppp+ -o eth0 -j ACCEPT
    firewall-cmd --permanent --direct --add-rule ipv4 filter FORWARD 0 -i eth0 -o ppp+ -j ACCEPT
    
    5.设置转发规则
    firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o eth0 -j MASQUERADE -s 192.168.0.0/24
    
    四、重启防火墙、启动pptpd
    firewall-cmd --reload
    systemctl start pptpd
    systemctl enable pptpd
