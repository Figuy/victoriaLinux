```powershell
root@debian:~# apt update -y 
```
### USER : vicky
### MDP : Vicky17!

## Installation du nginx

```powershell
 root@debian:~# sudo apt install nginx
```
### Vérification du statue du site 
```powershell
root@debian:~# sudo systemctl status nginx
```
```powershell
root@debian:~# apt install curl
```
```powershell
root@debian:~# curl http://127.0.0.1/
```
## Config mot de passe

```powershell
root@debian:~# nano /etc/security/pwquality.conf
```
```powershell
# minlen = 12
# minclass = 3
```

## Configurer SSH 

Création d'une clé
```

Modification du fichier : 

```powershell
root@debian:~# nano /etc/ssh/sshd_config
```
- PermitRootLogin no

## Instal de firewall :

```powershell
root@debian:~# apt install firewalld
```
On active le powershell
```powershell
root@debian:~# systemctl enable firewalld
root@debian:~# systemctl start firewalld
```
```powershell
root@debian:~# sudo firewall-cmd --permanent --remove-service dhcpv6-client
success
root@debian:~# sudo firewall-cmd --reload
```
```powershell 
root@debian:~# sudo firewall-cmd --permanent --add-port=22/tcp
root@debian:~# firewall-cmd --reload
```


## Configuration Fail2ban

```powershell
root@debian:~# apt install fail2ban
```
```powershell
root@debian:~# touch /var/log/secure
```

Modification du fichier : /etc/fail2ban/jail.local

```powershell 
[sshd]
enabled = true
port = ssh
logpath = /var/log/secure
maxretry = 5
```

```powershell
root@debian:~# sudo systemctl enable fail2ban
root@debian:~# sudo systemctl start fail2ban
```

## Création d'un script qui vérifie les log de vicky

 ```bash                                                             #!/bin/bash
tar --exclude='.tmp'  --exclude='.log' -czvf /home/vicky/.profile$(date +\%F).tar.gz /mnt/secure_data

max_jours=$(date +"%Y%m%d" -d "today - 7 days")

for file in /backup/ ;do
selection_date=find /backup | rev | cut -c 8-17 | rev

if [[ "$selection_date" -lt "$max_jours" ]]; then
        rm -f "$selection_date"
    fi
done
```

### Automatisation avec cron 

```powershell
root@debian:~# nano crontab -e
```
```powershell
* 3 * * * /backup.
```





