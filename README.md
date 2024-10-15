
# TP2-Unix


## 1.1

Les éléments de la configuration ssh que nous avons du changer sont: 
- PasswordAuthentication:
  - yes : Permet l'authentification par mot de passe.
  - no : Désactive l'authentification par mot de passe, n'autorisant que l'authentification par clé publique.

    Avantage de no : renforce la sécurité en évitant les attaques par force brute sur les mots de passe.
    Inconvénient de no : Les utilisateurs doivent obligatoirement utiliser une clé publique pour se connecter. 

- PermitRootLogin:
  - yes: Autorise la connexion SSH direct pour root
  - no: Interdit toute connexion SSH direct pour root
  - prohibit-password: Autorise root à se connecter uniquement par la clé publique.

    Avantage de no : Empeche les attaques directes sur le compte root.
    Inconvénient de no: Peut compliquer certaines opérations d'administration nécessitant root.

## 1.2
Sans passphrase, une clé privée volée permettrait à un attaquant d'accéder immédiatement au serveur, car rien ne protège l'accès.

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

## 1.5

Avec la commande nano /etc/ssh/sshd_config on peut changer les éléments du fichier.
On va y changer:
- PermitRootLogin prohibit-password pour empecher l'authnetification root par mot de passe mais autorise l'accès par clé publique.
- PasswordAuthention no pour desactiver l'authentification par mot de passe pour tous les utilisateurs.
- PubkeyAuthentication yes pour s'assurer que l'authentification par clé est activée.


## 2.1
```bash
root@serveur-correction:~# ps -eo user,pid,%cpu,%mem,stat,start,time,command
USER         PID %CPU %MEM STAT  STARTED     TIME COMMAND
```
- Time correspond au temps durant lequel le CPU a été utilisé par le processus.
- Le processus ayant le plus utilisé le processeur sur la machine est :
  ```bash
  root        1423  200  0.0 R+   15:37:36 00:00:00 ps -eo user,pid,%cpu,%mem,stat
  ```
- Le premier processus lancé après le démarrage du système est :
  ```bash
  root           1  0.0  0.1 Ss   14:05:39 00:00:02 /sbin/init
  ```
- La machine a été démarré à 14h05.
- La commande uptime permet de savoir depuis combien de temps le seveur tourne:
  ```bash
  root@serveur-correction:~# uptime
 15:46:55 up  1:41,  2 users,  load average: 0,00, 0,00, 0,00
  ```
- La dernière ligne me renvoie 1423 dans la colonne PID donc depuis le démarrage de la machine il y a eu 1423 processus créés :
```bash
root        1423  200  0.0 R+   15:37:36 00:00:00 ps -eo user,pid,%cpu,%mem,stat
```


## 2.2

Pour affichier le ppid avec la commande ps on peut faire : 
```bash
root@serveur-correction:~# ps -e -o pid,ppid,comm
    PID    PPID COMMAND
      1       0 systemd
      2       0 kthreadd
   1428       2 kworker/8:2-ata_sff
```

Pour obtenir la liste ordonnée de tous les processus ancêtres de la commande ps en cours d'exécution : 
```bash
root@serveur-correction:~# ps -eo pid,ppid --sort=ppid
    PID    PPID
      1       0
      2       0
      3       2
      4       2
      5       2
```
## 2.3

Pour pouvoir installer pstree j'ai exécuter la commande apt install psmisc

```bash
root@serveur-correction:~# pstree
systemd─┬─cron
        ├─dbus-daemon
        ├─dhclient
        ├─login───bash
        ├─sshd───sshd───bash───pstree
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-timesyn───{systemd-timesyn}
        └─systemd-udevd
```

## 2.4

Pour trier la liste de processus par occupation mémoire de façon décroissante on appuit sur 'M':
```bash
      1       0   0:02.23   0,0   0,1  20   0 S  163,8m  11,9m     0 systemd    
   1409    1406   0:00.58   0,0   0,1  20   0 S   17,6m  10,8m     0 sshd       
    623       1   0:00.14   0,0   0,1  20   0 S   18,4m  10,2m     0 systemd    
   1406       1   0:00.01   0,0   0,1  20   0 S   15,1m   8,7m     0 sshd       
    318       1   0:00.56   0,0   0,1  20   0 S   32,2m   8,5m     0 systemd-j+ 
    564       1   0:00.25   0,0   0,1  20   0 S   24,8m   7,6m     0 systemd-l+
```

Pour trouver le processus le plus gourmand sur la machine en occupation mémoire est systemd: 
```bash
PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.     
 1 root      20   0  167544  11924   9048 S   0,0   0,1   0:01.71 systemd 
```
Systemd est le processus de démarrage principal sur linux. C'est le premier processus lancé par le noyau au démarrage de l'ordinateur. Il gère le démarrage et la gestion de tous les autres processus du système.

En appuyant sur la touche "z", celle-ci nous permet d'activer ou désactiver l'affichage en couleur.

En appuyant sur la touche "x", celle-ci nous permet de mettre en surbrillance la colonne sur laquelle les processus sont triés.

En appuyant sur les touches "<" ou ">", celles-ci nous permettent de naviguer entre les colonnes et choisir celle que vous voulez trier.

Avant de pouvoir utiliser la commande htop, j'ai du l'installer avec la commande apt install htop.

Les avantages de la commande htop:
- Meilleur inteface visuel(utilisation de couleur pour mieux différencier les processus.
- La navigation est possible avec les touches fléchées.
- Fontionnalité de recherche de processus par son nom.
- Pouvoir trier les processus en cliquant diretement sur les colonnes.
- htop permet l'affichage complet de la command tandis que top n'affiche que le nom du processus.

Les inconvénients de la commande htop:
- La commande n'est pas installée par défaut.
- Moins fléxible pour les script, htop est plus orienté pour un usage interactif.

## 3.

Création des ficher script:
```bash
root@serveur-correction:~# cat>date.sh
#!/bin/sh
while true; do sleep 1; echo -n 'date '; date +%T; done
```

```bash
root@serveur-correction:~# cat date-toto.sh 
#!/bin/sh
while true; do sleep 1; echo -n 'toto '; date --date '5 hour ago' +%T; done
```

Autoriser l'execution des fichiers :
```bash
root@serveur-correction:~# chmod +x date.sh
root@serveur-correction:~# chmod +x date-toto.sh 
```

En exécutant le premier programme: 
```bash
root@serveur-correction:~# ./date.sh
date 13:57:27
date 13:57:28
```

En exécutant le deuxième script: 
```bash
root@serveur-correction:~# ./date-toto.sh 
toto 08:58:15
toto 08:58:16
```
La commande jobs nous permet d'affciher une liste de processus en arrière-plan:
```bash
root@serveur-correction:~# jobs
[1]-  Stoppé                 ./date.sh
[2]+  Stoppé                 ./date-toto.sh
```

En utilsant la commande fg %1 on va ramener le premier script au premier plan puis on pourra l'arreter avec ctrl+c.
```bash
root@serveur-correction:~# fg %1
./date.sh
date 14:00:57
date 14:00:58
date 14:00:59
date 14:01:00
date 14:01:01
^C
```
Pareil pour le deuxième: 
```bash
root@serveur-correction:~# fg %2
./date-toto.sh
toto 09:02:36
toto 09:02:37
toto 09:02:38
toto 09:02:39
^C
```
Utilisation de la commande "ps" pour trouver les PID des scripts.
```bash
root@serveur-correction:~# ps
    PID TTY          TIME CMD
    645 pts/0    00:00:00 bash
    649 pts/0    00:00:00 date.sh
    658 pts/0    00:00:00 sleep
    659 pts/0    00:00:00 date-toto.sh
    678 pts/0    00:00:00 sleep
    680 pts/0    00:00:00 ps
```

Utilisation de la commande "kill -9 PID" pour forcé la terminaison du processus.
```bash
root@serveur-correction:~# kill -9 649
root@serveur-correction:~# ps
    PID TTY          TIME CMD
    645 pts/0    00:00:00 bash
    659 pts/0    00:00:00 date-toto.sh
    678 pts/0    00:00:00 sleep
    693 pts/0    00:00:00 ps
```

Le script date.sh va afficher la date au format HH:MM:SS tant que le script n'est pas interrompu manuelement.

Le script date-toto.sh affiche l'heure mais cinq heure avang affichera et avec le mot "toto" devant.

Les différences entre cat et tee sont que cat peut combiner plusieurs fichiers et leurs contenus tandis que tee peut écrire la sortie d'une commande dans un fichier tout en affichant la sortie à l'écran.

## 4.

La commande "ls | cat" permet de lister les fichiers et les répertoires dans le répertoire courant puis envoie la sortie à la commande cat.
```bash
root@serveur-correction:~# ls | cat
date.sh
date-toto.sh
```

La commande "ls -l | cat > liste" permet de créer un fichier "liste" et d'y enregistrer avec la commande "cat >" les détails, comme les permissions, le nombres de lien, le groupe, le propriétaire, la taille, la date et le nom, des fichiers que la commande ls -l va afficher dans le répertoire.
```bash
root@serveur-correction:~# ls -l | cat > liste
root@serveur-correction:~# cat liste
total 8
-rwxr-xr-x 1 root root 66 14 oct.  13:49 date.sh
-rwxr-xr-x 1 root root 86 14 oct.  13:53 date-toto.sh
-rw-r--r-- 1 root root  0 14 oct.  16:48 liste
root@serveur-correction:~# ls -l | tee liste
```

La commande ls -l | tee liste va faire la meme chose que la commande précédante cependant le contenu sera affiché à l'écran enregistré en meme temps .
```bash
root@serveur-correction:~# ls -l | tee liste
total 12
-rwxr-xr-x 1 root root  66 14 oct.  13:49 date.sh
-rwxr-xr-x 1 root root  86 14 oct.  13:53 date-toto.sh
-rw-r--r-- 1 root root 158 14 oct.  16:48 liste
```

La commande ls -l | tee liste | wc -l executera la meme chose que la commande précédente mais executera en plus ma commande wc -l qui va compter le nombre total de fichier/répertoires listés.
```bash
root@serveur-correction:~# ls -l | tee liste | wc -l
4
```
## 5 

Le service rsyslog n'était pas installer sur mon système donc je l'ai installer avec la commande "apt install rsyslog".

Pour vérifier l'état du service on peut utiliser la commande :
```bash
root@serveur-correction:~# servive rsyslog status
-bash: servive : commande introuvable
root@serveur-correction:~# service rsyslog status
● rsyslog.service - System Logging Service
     Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; preset: enab>
     Active: active (running) since Mon 2024-10-14 17:02:12 CEST; 41s ago
TriggeredBy: ● syslog.socket
       Docs: man:rsyslogd(8)
             man:rsyslog.conf(5)
             https://www.rsyslog.com/doc/
   Main PID: 861 (rsyslogd)
      Tasks: 4 (limit: 11878)
     Memory: 1.3M
        CPU: 16ms
     CGroup: /system.slice/rsyslog.service
             └─861 /usr/sbin/rsyslogd -n -iNONE

oct. 14 17:02:12 serveur-correction systemd[1]: Starting rsyslog.service - Syst>
oct. 14 17:02:12 serveur-correction rsyslogd[861]: imuxsock: Acquired UNIX sock>
oct. 14 17:02:12 serveur-correction rsyslogd[861]: [origin software="rsyslogd" >
oct. 14 17:02:12 serveur-correction systemd[1]: Started rsysl
```
A partir de la commande précedente, on peut voir que le PID est 861.

Rsyslog enregistre les messages issue des services standards dans le fichier /var/log/syslog.
```bash
root@serveur-correction:/var/log# cat syslog
024-10-15T13:23:02.605491+02:00 serveur-correction systemd[1]: Started user@0.service - User Manager for UID 0.
2024-10-15T13:23:02.607901+02:00 serveur-correction systemd[1]: Started session-1.scope - Session 1 of User root.
2024-10-15T13:23:23.037731+02:00 serveur-correction systemd[1]: Started session-3.scope - Session 3 of User root.
2024-10-15T13:23:23.089899+02:00 serveur-correction systemd[1]: systemd-fsckd.service: Deactivated successfully.
2024-10-15T13:26:55.549689+02:00 serveur-correction dhclient[577]: DHCPREQUEST for 10.20.0.143 on enp0s3 to 10.20.0.253 port 67
2024-10-15T13:26:55.561016+02:00 serveur-correction dhclient[577]: DHCPACK of 10.20.0.143 from 10.20.0.253
2024-10-15T13:26:55.592723+02:00 serveur-correction dhclient[577]: bound to 10.20.0.143 -- renewal in 3307 seconds.

```
La plupart des autres messages sont enregistrés dans auth.log, kern.log, mail.log.

Le service Cron est un utilitaire de planification de taches qui permet d'executer des scripts de manière automatique, il est essentiel pour l'automatisation des tâches répétitives et la gestion de la maintenance du système.

La commande tail -f affiche les dernières lignes d'un fichier et met à jour la sortie en temps réel au fur et à mesure que des nouvelles lignes sont ajoutées au fichier.

```bash
2024-10-15T13:43:55.424970+02:00 serveur-correction systemd[1]: Stopping cron.service - Regular background program processing daemon...
2024-10-15T13:43:55.425459+02:00 serveur-correction systemd[1]: cron.service: Deactivated successfully.
2024-10-15T13:43:55.425940+02:00 serveur-correction systemd[1]: Stopped cron.service - Regular background program processing daemon.
2024-10-15T13:43:55.457651+02:00 serveur-correction systemd[1]: Started cron.service - Regular background program processing daemon.
2024-10-15T13:43:55.461547+02:00 serveur-correction cron[727]: (CRON) INFO (pidfile fd = 3)
2024-10-15T13:43:55.462407+02:00 serveur-correction cron[727]: (CRON) INFO (Skipping @reboot jobs -- not system startup)
```
En redémarrant le cron depuis un autre shell on peut voir que le sevice cron s'est arrêté puis à redémarré.

Le fichier /etc/logrotate.conf est le fichier de configuration qui va permettre de gérer facilement les logs pour garantir des performances et une utilisation d'espace optimisés.


```bash
root@serveur-correction:~# dmesg
   10.975226] audit: type=1400 audit(1728991372.535:7): apparmor="STATUS" operation="profile_load" profile="unconfined" name="man_groff" pid=552 comm="apparmor_parser"
[   11.024063] audit: type=1400 audit(1728991372.583:8): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=551 comm="apparmor_parser"
[   11.024077] audit: type=1400 audit(1728991372.583:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=551 comm="apparmor_parser"
[   11.024084] audit: type=1400 audit(1728991372.583:10): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=551 comm="apparmor_parser"
```

La commande dmesg | grep -i "cpu" permet de rechercher spécifiquement les informations sur le processeur dans le dmesg.

```bash
root@serveur-correction:~# dmesg | grep -i "cpu"
[    0.001170] CPU MTRRs all blank - virtualized system.
[    0.001925] ACPI: SSDT 0x00000000DFFF02F0 00036C (v01 VBOX   VBOXCPUT 00000002 INTL 20200925)
[    0.012666] smpboot: Allowing 12 CPUs, 0 hotplug CPUs
[    0.016152] setup_percpu: NR_CPUS:8192 nr_cpumask_bits:12 nr_cpu_ids:12 nr_node_ids:1
[    0.016777] percpu: Embedded 61 pages/cpu s212992 r8192 d28672 u262144
[    0.016783] pcpu-alloc: s212992 r8192 d28672 u262144 alloc=1*2097152
[    0.016786] pcpu-alloc: [0] 00 01 02 03 04 05 06 07 [0] 08 09 10 11 -- -- -- -- 
[    0.043782] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=12, Nodes=1
[    0.050170] rcu: 	RCU restricting CPUs from NR_CPUS=8192 to nr_cpu_ids=12.
[    0.050174] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=12
[    0.072546] MDS: Mitigation: Clear CPU buffers
[    0.194534] smpboot: CPU0: 12th Gen Intel(R) Core(TM) i7-12700 (family: 0x6, model: 0x97, stepping: 0x2)
[    0.194664] Performance Events: unsupported p6 CPU model 151 no PMU driver, software events only.
[    0.194958] smp: Bringing up secondary CPUs ...
[    0.195019] .... node  #0, CPUs:        #1  #2  #3  #4  #5  #6  #7  #8  #9 #10 #11
[    0.232737] smp: Brought up 1 node, 12 CPUs
[    0.250791] cpuidle: using governor ladder
[    0.250791] cpuidle: using governor menu
[    0.937919] intel_pstate: CPU model not supported
[    0.940205] ledtrig-cpu: registered to indicate activity on CPUs
[    8.661550] cryptd: max_cpu_qlen set to 1000
```

Le modèle du processeur est Intel Core i7-12700 : 
```bash
[    0.194534] smpboot: CPU0: 12th Gen Intel(R) Core(TM) i7-12700 (family: 0x6, model: 0x97, stepping: 0x2)
```
La carte réseau détectée est Intel Pro/1000 Network Connection :  
```bash
root@serveur-correction:~# dmesg | grep -i "eth"
[    2.085676] e1000 0000:00:03.0 eth0: (PCI:33MHz:32-bit) 08:00:27:ae:4e:31
[    2.085683] e1000 0000:00:03.0 eth0: Intel(R) PRO/1000 Network Connection
[    2.087985] e1000 0000:00:03.0 enp0s3: renamed from eth0
```









