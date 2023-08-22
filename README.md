# ansible_roles_middleware_tutorial

 Use ansible roles to customize the deployment of middleware clusters tutorial



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