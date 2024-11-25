# TP Avancé : "Mission Ultime : Sauvegarde et Sécurisation"

## Étape 1 : Analyse et nettoyage du serveur

### Lister les tâches cron pour détecter des backdoors :
```powershell
[root@localhost tmp]# crontab -u attacker -l
```

###  Identifier et supprimer les fichiers cachés :
```powershell
[root@localhost tmp]# ls -alh
```

```powershell
[root@localhost tmp]# rm .hidden_script malicious.sh .hidden_file
```

```powershell
[root@localhost ~]# ls -alh /var/tmp
```

```powershell
[root@localhost ~]# rm /var/tmp/.nop
```

```powershell
[root@localhost ~]# rm -r /home/attacker
```

### Analyser les connexions réseau actives :

```powershell
[root@localhost ~]# dnf install net-tools
```

```powershell
[root@localhost ~]# netstat -a
```

## Étape 2 : Configuration avancée de LVM

### Créer un snapshot de sécurité pour /mnt/secure_data :

```powhershell
[root@localhost mnt]# lvs
```

```powershell
[root@localhost dev]# lvcreate -L 500M -s -n secure_lvm1 /dev/vg_secure/secure_data
```

### Tester la restauration du snapshot :

```powershell
[root@localhost dev]# rm /mnt/secure_data/sensitive1.txt
```

```powershell 
[root@localhost dev]# umount /mnt/secure_data/
```

```powershell
[root@localhost dev]# lvconvert --mergesnapshot /dev/vg_secure/secure_lvm1
```

```powershell
[root@localhost dev]# mount /mnt/secure_data/
```

### Vérifiez que l’espace supplémentaire est disponible :

```powershell
[root@localhost dev]#vgs
```

```powershell
[root@localhost dev]#  lvextend -L +100M /dev/vg_secure/secure_data
```

## Étape 3 : Automatisation avec un script de sauvegarde

### Créer un script secure_backup.sh :

```powershell
[root@localhost /]# mkdir backup
```

contenue : 
```bash
#!/bin/bash
tar --exclude='.tmp'  --exclude='.log' -czvf /backup/secure_data$(date +\%F).tar.gz /mnt/secure_data
``` 
### Ajoutez une fonction de rotation des sauvegardes :
```powershell
max_jours=$(date +"%Y%m%d" -d "today - 7 days")

for file in /backup/ ;do
selection_date=find /backup | rev | cut -c 8-17 | rev

if [[ "$selection_date" -lt "$max_jours" ]]; then
        rm -f "$selection_date"
    fi
done
```

### Testez le script :
```powershell
[root@localhost /]# /secure_backup.sh /
```

### Automatisez avec une tâche cron :
```powershell
[root@localhost /]# crontab -e
```
Contenue : 
```
* 3 * * * /secure_backup.sh
```

## Étape 4 : Surveillance avancée avec auditd

### Configurer auditd pour surveiller /etc :

```powershell
[root@localhost /]# auditctl -w /etc -p wa -k etc-changes
```

### Tester la surveillance :

```powershell
[root@localhost /]# touch /etc/test_auditd
```

```powershell 
[root@localhost /]# sudo cat /var/log/audit/audit.log | grep test_auditd
```

### Analyser les événements :

```powershell
[root@localhost /]# ausearch -k etc_changes > /var/log/audit_etc.log
```

```powershell
[root@localhost /]# cat var/log/audit_etc.log
```
## Étape 5 : Sécurisation avec Firewalld

### Configurer un pare-feu pour SSH et HTTP/HTTPS uniquement :

```powershell
[root@localhost /]# sudo firewall-cmd --permanent --add-service http
```

```powershell
[root@localhost /]# firewall-cmd --reload
```

Bloquez toutes les autres connexions

```powershell
[root@localhost /]# sudo firewall-cmd --permanent --remove-service dhcpv6-client
```

```powershell
[root@localhost /]# sudo firewall-cmd --permanent --remove-service cockpit
```

```powershell
[root@localhost /]# sudo firewall-cmd --reload
```

