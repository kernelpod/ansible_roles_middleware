# ansible_roles_middleware_tutorial

 Use ansible roles to customize the deployment of middleware clusters tutorial

## Offical link

[Offical install nginx]: https://nginx.org/en/linux_packages.html



## Server Planning

| hostname       | IP                | open source name             |
| -------------- | ----------------- | ---------------------------- |
| k8s-master01   | 192.168.2.121     | docker,etcd,nginx,keepalived |
| k8s-master02   | 192.168.2.167     | docker,etcd,nginx,keepalived |
| k8s-master03   | 192.168.2.177     | docker,nginx,keepalived      |
| k8s-node01     | 192.168.2.190     | docker,etcd                  |
| k8s-node02     | 192.168.2.195     | docker                       |
| LoadbalancerIP | 192.168.2.88(VIP) |                              |



## Open Source Version

|            | version                              |
| ---------- | ------------------------------------ |
| System     | CentOS Linux release 7.9.2009 (Core) |
| ansible    | 2.10.15                              |
| nginx      | 1.24.0                               |
| haproxy    | 1.8.27-493ce0b 2020/11/06            |
| Keepalived | v2.1.5                               |



### ansible version

> The handler is not configured, you can learn how to use the handler online

```
[root@node1 ~]# ansible --version
ansible 2.10.15
  config file = None
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.6/site-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 3.6.8 (default, Jun 20 2023, 11:53:23) [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)]
[root@node1 ~]#

```

### nginx version

```
[root@node1 ansible_roles_middleware]# nginx -v
nginx version: nginx/1.24.0
[root@node1 ansible_roles_middleware]#
```



### haproxy version



```
[root@node1 ansible_roles_middleware]# haproxy -v
HA-Proxy version 1.8.27-493ce0b 2020/11/06
Copyright 2000-2020 Willy Tarreau <willy@haproxy.org>

[root@node1 ansible_roles_middleware]#

```

### Keepalived version

```
[root@node1 ansible_roles_middleware]# keepalived -v
Keepalived v2.1.5 (07/13,2020)

Copyright(C) 2001-2020 Alexandre Cassen, <acassen@gmail.com>

Built with kernel headers for Linux 4.18.0
Running on Linux 4.18.0-500.el8.x86_64 #1 SMP Wed Jun 28 00:07:07 UTC 2023

configure options: --build=x86_64-redhat-linux-gnu --host=x86_64-redhat-linux-gnu --program-prefix= --disable-dependency-tracking --prefix=/usr --exec-prefix=/usr --bindir=/usr/bin --sbindir=/usr/sbin --sysconfdir=/etc --datadir=/usr/share --includedir=/usr/include --libdir=/usr/lib64 --libexecdir=/usr/libexec --localstatedir=/var --sharedstatedir=/var/lib --mandir=/usr/share/man --infodir=/usr/share/info --disable-libiptc --disable-ipset --enable-snmp --enable-snmp-rfc --enable-sha1 --with-init=systemd build_alias=x86_64-redhat-linux-gnu host_alias=x86_64-redhat-linux-gnu PKG_CONFIG_PATH=:/usr/lib64/pkgconfig:/usr/share/pkgconfig CFLAGS=-O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -specs=/usr/lib/rpm/redhat/redhat-annobin-cc1 -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection LDFLAGS=-Wl,-z,relro -Wl,-z,now -specs=/usr/lib/rpm/redhat/redhat-hardened-ld

Config options:  LVS VRRP VRRP_AUTH OLD_CHKSUM_COMPAT FIB_ROUTING SNMP_V3_FOR_V2 SNMP_VRRP SNMP_CHECKER SNMP_RFCV2 SNMP_RFCV3

System options:  PIPE2 SIGNALFD INOTIFY_INIT1 VSYSLOG EPOLL_CREATE1 IPV4_DEVCONF IPV6_ADVANCED_API LIBNL3 RTA_ENCAP RTA_EXPIRES RTA_NEWDST RTA_PREF FRA_SUPPRESS_PREFIXLEN FRA_SUPPRESS_IFGROUP FRA_TUN_ID RTAX_CC_ALGO RTAX_QUICKACK RTEXT_FILTER_SKIP_STATS FRA_L3MDEV FRA_UID_RANGE RTAX_FASTOPEN_NO_COOKIE RTA_VIA FRA_OIFNAME FRA_PROTOCOL FRA_IP_PROTO FRA_SPORT_RANGE FRA_DPORT_RANGE RTA_TTL_PROPAGATE IFA_FLAGS IP_MULTICAST_ALL LWTUNNEL_ENCAP_MPLS LWTUNNEL_ENCAP_ILA NET_LINUX_IF_H_COLLISION LIBIPTC_LINUX_NET_IF_H_COLLISION LIBIPVS_NETLINK IPVS_DEST_ATTR_ADDR_FAMILY IPVS_SYNCD_ATTRIBUTES IPVS_64BIT_STATS VRRP_VMAC VRRP_IPVLAN IFLA_LINK_NETNSID CN_PROC SOCK_NONBLOCK SOCK_CLOEXEC O_PATH GLOB_BRACE INET6_ADDR_GEN_MODE VRF SO_MARK SCHED_RESET_ON_FORK
[root@node1 ansible_roles_middleware]#

```



## install ansible

```
yum -y install ansible
```





```
mkdir -pv ~/ansible_roles_middleware/roles
[root@ansible ~]# mkdir -pv ~/ansible_roles_middleware/roles
mkdir: created directory '/root/ansible_roles_middleware'
mkdir: created directory '/root/ansible_roles_middleware/roles'
[root@ansible ~]# ls
ansible_roles_middleware
[root@ansible ~]#
[root@ansible ~]# cd ansible_roles_middleware/
[root@ansible ansible_roles_middleware]# bash
[root@ansible ansible_roles_middleware]#


```

## Ansible-roles

### Configure Ansible

> roles_path 
>
> /root/ansible/roles：current roles work path
>
> /usr/share/ansible/roles：rpm install rhel-system-roles default path
>
> inventory： inventory files
>
> host_key_checking：check key
>
> privilege_escalation:  root privilege

```
[root@node2 ansible_roles_middleware]# cat ansible.cfg
[defaults]
inventory = /root/ansible_roles_middleware/inventory
roles_path = /root/ansible_roles_middleware/roles:/usr/share/ansible/roles
host_key_checking = False
#remote_user = mars
#[privilege_escalation]
#become = True
#become_method = sudo
#become_user = root
#become_ask_pass = False

[root@node2 ansible_roles_middleware]#
```



### Init roles

```
[root@node1 ~]# cd ansible
[root@node1 ansible_roles_middleware]#
[root@node1 ansible_roles_middleware]# ansible-galaxy init roles/preinstall
- Role roles/preinstall was created successfully
[root@node1 ansible_roles_middleware]#
[root@node1 ansible_roles_middleware]# ansible-galaxy init roles/nginx
- Role roles/nginx was created successfully
[root@node1 ansible_roles_middleware]#
[root@node1 ansible_roles_middleware]# ansible-galaxy init roles/haproxy
- Role roles/haproxy was created successfully
[root@node1 ansible_roles_middleware]#
[root@node1 ansible_roles_middleware]# ansible-galaxy init roles/keepalived
- Role roles/keepalived was created successfully
[root@node1 ansible_roles_middleware]#
```

#### role-preinstall

> Swap selinux has a lot of default roles to modify, so I won’t modify them. 
>
> It’s more convenient to use commands



role-preinstall-task

```
[root@node1 ansible_roles_middleware]# cat roles/preinstall/tasks/main.yml
---
- name: Disable and stop firewalld
  service:
    name: firewalld
    state: stopped
    enabled: no

- name: Set SElinux setenforce 0
  shell: setenforce 0

- name: config selinux
  shell: sed -i 's/ASELINUX=.*/SELINUX=disabled/' /etc/selinux/config

- name: modprobe
  shell: modprobe br_netfilter
  
- name: kubernetes kernel config
  template:
    src: k8s.config.j2
    dest: /etc/sysctl.d/kubernetes.conf

- name: effect kubernetes.conf
  shell: sysctl -p /etc/sysctl.d/kubernetes.conf

[root@node1 ansible_roles_middleware]#
```





```
[root@node1 ansible_roles_middleware]# cat roles/preinstall/templates/k8s.config.j2
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
[root@node1 ansible_roles_middleware]#

```

#### role-nginx

```
[root@node1 ansible_roles_middleware]# cat roles/nginx/tasks/main.yml
---
- name: install nginx
  yum:
    name:
      - nginx
      - nginx-mod-stream
    state: present

- name: configure nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/conf.d/kapiserver.conf
  notify: restart nginx

- name: start service nginx
  service:
    name: nginx
    state: started
    enabled: yes
[root@node1 ansible_roles_middleware]#
```



role-nginx-template

```
[root@ansible ansible_roles_middleware]# cat roles/haproxy/handlers/main.yml
---
# handlers file for roles/haproxy
- name: restart haproxy
  service:
    name: haproxy
    state: restarted

[root@ansible ansible_roles_middleware]#
[root@ansible ansible_roles_middleware]#
[root@ansible ansible_roles_middleware]# cat roles/nginx/templates/nginx.conf.j2
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

# L4 ，LB for kubernetes master
stream {
    log_format  main  '$remote_addr $upstream_addr - [$time_local] $status $upstream_bytes_sent';
    access_log  /var/log/nginx/kapiserver-access.log  main;
    upstream kapiserver {
{% for host in groups['nginx'] %}
       server {{ hostvars[host]['inventory_hostname'] }}:6443; #master apiserver port
{% endfor %}
    }
    server {
       listen 1443; # kubernetes master run on 6443,You can customize the port
       proxy_pass kapiserver;
    }
}
[root@ansible ansible_roles_middleware]#
```



role-nginx-handler

```
[root@ansible ansible_roles_middleware]# cat roles/nginx/handlers/main.yml
---
- name: restart nginx
  service:
    name: nginx
    state: restarted

[root@ansible ansible_roles_middleware]#

```



#### role-haproxy

role-haproxy-task

```

[root@node1 ansible_roles_middleware]# cat roles/haproxy/tasks/main.yml
---
- name: install haproxy
  yum:
    name: haproxy
    state: present

- name: configure haproxy
  template:
    src: haproxy.cfg.j2
    dest: /etc/haproxy/haproxy.cfg
  notify: restart haproxy


- name: start service haproxy
  service:
    name: haproxy
    state: started
    enabled: yes

- name: stop service firewalld
  service:
    name: firewalld
    state: stopped
    enabled: no
[root@node1 ansible_roles_middleware]#

```

role-haproxy-template

```
#---------------------------------------------------------------------
# Example configuration for a possible web application.  See the
# full configuration options online.
#
#   https://www.haproxy.org/download/1.8/doc/configuration.txt
#
#---------------------------------------------------------------------

#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    # to have these messages end up in /var/log/haproxy.log you will
    # need to:
    #
    # 1) configure syslog to accept network log events.  This is done
    #    by adding the '-r' option to the SYSLOGD_OPTIONS in
    #    /etc/sysconfig/syslog
    #
    # 2) configure local2 events to go to the /var/log/haproxy.log
    #   file. A line like the following can be added to
    #   /etc/sysconfig/syslog
    #
    #    local2.*                       /var/log/haproxy.log
    #
    log         127.0.0.1 local2

    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon

    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats

    # utilize system-wide crypto-policies
    ssl-default-bind-ciphers PROFILE=SYSTEM
    ssl-default-server-ciphers PROFILE=SYSTEM

#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------
frontend main
    bind *:5000
    acl url_static       path_beg       -i /static /images /javascript /stylesheets
    acl url_static       path_end       -i .jpg .gif .png .css .js

    use_backend static          if url_static
    default_backend             app

#---------------------------------------------------------------------
# static backend for serving up images, stylesheets and such
#---------------------------------------------------------------------
backend static
    balance     roundrobin
    server      static 127.0.0.1:4331 check

#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
backend app
    balance     roundrobin
{% for host in groups['nginx'] %}
    server {{ hostvars[host]['ansible_facts']['hostname'] }} {{ hostvars[host]['ansible_facts']['default_ipv4']['address'] }}:1443 check
{% endfor %}


```



role-haproxy-handler

```
[root@ansible ansible_roles_middleware]# cat roles/haproxy/handlers/main.yml
---
# handlers file for roles/haproxy
- name: restart haproxy
  service:
    name: haproxy
    state: restarted

[root@ansible ansible_roles_middleware]#
```



#### role-keepalived

role-keepalived-task

```
[root@node1 ansible_roles_middleware]# cat roles/keepalived/tasks/main.yml
---
- name: install keepalived
  yum:
    name: keepalived
    state: present

- name: configure keepalived
  template:
    src: keepalived.conf.j2
    dest: /etc/keepalived/keepalived.conf
  notify: restart keepalived


- name: start service keepalived
  service:
    name: keepalived
    state: started
    enabled: yes

- name: stop service firewalld
  service:
    name: firewalld
    state: stopped
    enabled: no

[root@node1 ansible_roles_middleware]#

```

role-keepalived-template

```
[root@ansible ansible_roles_middleware]# cat roles/keepalived/templates/keepalived.conf.j2
roles/keepalived/templates/keepalived.conf.j2
global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 127.0.0.1
   smtp_connect_timeout 30
   router_id NGINX_MASTER
}

vrrp_instance VI_1 {
    state {{ STATE }}
    interface ens33
    virtual_router_id {{ VRID }}
    priority {{ PRIORITY }}
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    # 虚拟IP
    virtual_ipaddress {
        192.168.2.88/24
    }
}
[root@ansible ansible_roles_middleware]#
```

role-keepalived-handler

```
[root@ansible ansible_roles_middleware]# cat roles/keepalived/handlers/main.yml
---
# handlers file for roles/keepalived
- name: restart keepalived
  service:
    name: keepalived
    state: restarted

[root@ansible ansible_roles_middleware]#
```



Use roles

```
[root@node1 ansible_roles_middleware]# pwd
/root/ansible
[root@node1 ansible_roles_middleware]# cat setup.yml
- hosts: all
  tasks:
    - import_role:
        name: haproxy
      when: inventory_hostname in groups['lb']

    - import_role:
        name: keepalived
      when: inventory_hostname in groups['lb']

    - import_role:
        name: nginx
      when: inventory_hostname in groups['nginx']
[root@node1 ansible_roles_middleware]#
```



## Configure ansible_roles_middleware

> 
>
> |                  | value           | file_path                                     |
> | ---------------- | --------------- | --------------------------------------------- |
> | VIP              | 192.168.2.88/24 | roles/keepalived/templates/keepalived.conf.j2 |
> | ansible_ssh_user | root            | inventory                                     |
> | ansible_ssh_pass | 123456          | inventory                                     |
> | ansible_ssh_port | 22              | inventory                                     |
> | lb               | 192.168.2.211   | inventory                                     |
> | lb               | 192.168.2.116   | inventory                                     |
> | lb               | 192.168.2.190   | inventory                                     |



### Inventory-LB(nginx) address replace

> default_ip: 192.168.2.211
>
> new_ip: 192.168.183.128
>
> sed -i 's/192.168.2.211/192.168.183.128/g' `grep 192.168.2.211 -rl .`



```
sed -i 's/192.168.2.211/new_ip/g' `grep 192.168.2.211 -rl .`
sed -i 's/192.168.2.211/new_ip/g' `grep 192.168.2.211 -rl .`
sed -i 's/192.168.2.211/new_ip/g' `grep 192.168.2.211 -rl .`
```



### Inventory-VIP address replace

> default_ip: 192.168.2.88
>
> new_ip: 192.168.183.128
>
> sed -i 's/192.168.2.288/192.168.183.128/g' `grep 192.168.2.88 -rl .`

```
sed -i 's/192.168.2.88/new_ip/g' `grep 192.168.2.88 -rl .`
```



## Run Ansible-Playbook

> ansible-playbook setup.yml -C:
>
> -C: --syntax-check

```
ansible-playbook setup.yml
```



![](imgs\run-book.png)