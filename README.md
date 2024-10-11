# TP2-Unix

https://www.ipgp.fr/~lecocq/LicencePro-ProjetWeb/serveur-correction.ova


## 1.1



## 1.2

```bash
root@serveur-correction:~# ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -q -N "" && ssh-copy-id root@10.20.0.143
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@10.20.0.143's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@10.20.0.143'"
and check to make sure that only the key(s) you wanted were added.

```
La commande ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -q -N "" && ssh-copy-id root@10.20.0.143 nous permet de générer un couple de clef prive public et de transférer directement la clé publique vers le serveur.


## 1.3

Avec la commande précèdente, le fichier authorized_keys a été créé automatiquement dans le sossier .ssh et ils contient les clés publiques qui permettent la connection avec le serveur.

Pour donner les droits de lecture, d'écriture et d'execution seulement pour le propriétaire:
```bash
root@serveur-correction:~/.ssh# chmod 700 authorized_keys 
root@serveur-correction:~/.ssh# ls -l ~/.ssh/authorized_keys 
-rwx------ 1 root root 1498 11 oct.  14:34 /root/.ssh/authorized_keys
```
## 1.4

```bash
1013782@ppti-24-308-18:/users/nfs/Etu2/1013782$ ssh -i id_rsa.pub root@10.20.0.143
Warning: Identity file id_rsa.pub not accessible: No such file or directory.
root@10.20.0.143's password: 
Linux serveur-correction 6.1.0-12-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.52-1 (2023-09-07) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Oct 11 14:47:25 2024 from 10.20.0.18
root@serveur-correction:~# 
```
Avec la commande ssh -i id_rsa.pub root@10.20.0.143 sa nous permet de nous connecter avec la clé publique.
