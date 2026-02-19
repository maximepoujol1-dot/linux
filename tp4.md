# TP4 – Administration SSH et Serveur Web Nginx

---

## Partie 1 – Mise en place de l'environnement virtualisé

### 1.1 Installation de la VM Ubuntu

Installez VirtualBox et créez une VM Ubuntu :

- **RAM** : 2 Go minimum
- **Disque** : 20 Go
- **Réseau** : Bridged Adapter

![tp4_c1](img_tp4/c1.png)

### 1.2 Vérification de la connectivité réseau

Vérifiez que la VM a une IP accessible depuis la machine hôte avec les commandes :


-`ip a`
-`ping <IP_VM>`


![tp4_c2](img_tp4/c2.png)

---

## Partie 2 – Serveur SSH

### 2.1 Installation du serveur SSH

Installez le serveur SSH sur la VM avec les commandes :


-sudo apt update
-sudo apt install openssh-server


![tp4_c3](img_tp4/c3.png)

### 2.2 Vérification du service SSH

Vérifiez que le service SSH fonctionne et écoute sur un port avec les commandes :

-`systemctl status`
-`ss` ou `netstat`



![tp4_c4](img_tp4/c4.png)

### 2.3 Connexion SSH depuis la machine hôte

Connectez-vous depuis la machine hôte :


![tp4_c5](img_tp4/c5.png)

### 2.4 Authentification par clé SSH

Générez une clé SSH sur la machine cliente et copiez-la sur le serveur pour tester la connexion sans mot de passe.

Générer et copier une clé avec les commandes : `ssh-keygen`, `ssh-copy-id`


![tp4_c6](img_tp4/c6.png)
![tp4_c6](img_tp4/c6.2.png)
![tp4_c6](img_tp4/c6.3.png)

---

## Partie 3 – Sécurisation SSH

### 3.1 Modification de la configuration SSH

Modifiez la configuration SSH sur le serveur pour renforcer la sécurité :

1. **Interdisez l'accès root**
2. **Désactivez l'authentification par mot de passe**
3. **Changez le port par défaut (22)** pour réduire les tentatives de brute-force

recherchez le fichier `/etc/ssh/sshd_config` et les options `PermitRootLogin`, `PasswordAuthentication`



Modifications à effectuer :

-Port 2222
-PermitRootLogin no
-PasswordAuthentication no




### 3.2 Redémarrage et test

Redémarrez le service SSH et testez la connexion avec le nouveau port depuis la machine cliente.

![tp4_c7](img_tp4/c7.png)


### 3.3 Création d'un alias SSH

Créez un alias SSH dans `~/.ssh/config` pour simplifier les connexions.



![tp4_c8](img_tp4/c8.png)

---

## Partie 4 – Transfert de fichiers

### 4.1 Transfert avec SCP

Transférez un fichier depuis la machine cliente vers le serveur :


![tp4_c10](img_tp4/c10.png)

### 4.2 Transfert avec SFTP

Explorez les commandes `put`, `get`, `ls` pour transférer et naviguer sur le serveur.

Commandes SFTP :

-ls
-put fichier.txt
-get fichier_distant.txt


![tp4_c11](img_tp4/c11.png)

### 4.3 Synchronisation avec RSYNC

Synchronisez un dossier entre client et serveur.


![tp4_c12](img_tp4/c12.png)

---

## Partie 5 – Analyse des logs et sécurité

### 5.1 Suivi des logs d'authentification

Suivez les logs d'authentification pour observer les connexions SSH avec la commande :


-sudo tail -f /var/log/auth.log


![tp4_c13](img_tp4/c13.png)

### 5.2 Installation et configuration de Fail2Ban

Installez Fail2Ban et testez un bannissement après plusieurs tentatives échouées avec les commandes :


-sudo apt install fail2ban
-sudo systemctl enable fail2ban
-sudo systemctl start fail2ban


Configuration :


![tp4_c14](img_tp4/c14.png)

Vérification avec la commande :

-sudo fail2ban-client status sshd


![tp4_c15](img_tp4/c15.png)

---

## Partie 6 – Tunnel SSH

### 6.1 Tunnel local

Créez un tunnel local pour accéder à un service web distant depuis la machine cliente avec la commande :


-ssh -L 8080:localhost:80 serveur-tp


![tp4_c16](img_tp4/c16.1.png)
![tp4_c16](img_tp4/c16.2.png)
![tp4_c16](img_tp4/c16.3.png)

### 6.2 Tunnel distant

Créez un tunnel distant pour permettre l'accès SSH au client via le serveur avec la commande :


-ssh -R 9090:localhost:22 serveur-tp


![tp4_c17](img_tp4/c17.png)

---

## Partie 7 – Nginx et HTTPS

### 7.1 Installation de Nginx

Installez Nginx sur la VM avec les commandes :


-sudo apt install nginx
-sudo systemctl enable nginx
-sudo systemctl start nginx


![tp4_c18](img_tp4/c18.png)

### 7.2 Création du site test

Créez un site test dans `/var/www/site-tp` et un fichier `index.html` avec un message de bienvenue.


![tp4_c19](img_tp4/c19.png)

### 7.3 Configuration Nginx pour HTTP

Configurez Nginx pour servir ce site sur HTTP.

![tp4_c20](img_tp4/c20.1.png)

![tp4_c20](img_tp4/c20.2.png)

### 7.4 Génération du certificat SSL

Générez un certificat auto-signé pour HTTPS.

![tp4_c21](img_tp4/c21.png)

### 7.5 Configuration HTTPS avec redirection

Configurez la redirection HTTP → HTTPS.


![tp4_c22](img_tp4/c22.png)

![tp4_c22](img_tp4/c22.1.png)

![tp4_c22](img_tp4/c22.2.png)

### 7.6 Test du site HTTPS

Testez le site depuis le client :

```bash
curl -k https://<IP_VM>
```

![tp4_c23](img_tp4/c23.png)

---

## Partie 8 – Firewall et permissions

### 8.1 Configuration du firewall UFW

Autorisez Nginx dans le firewall (ports HTTP/HTTPS).

**Piste** :
```bash
sudo ufw enable
sudo ufw allow 'Nginx Full'
sudo ufw allow 2222/tcp
sudo ufw status
```

![tp4_c24](img_tp4/c24.png)

![tp4_c24.1](img_tp4/c24.1.png)

### 8.2 Vérification des permissions

Vérifiez les permissions sur `/var/www/site-tp` pour que Nginx puisse lire les fichiers.

**Pistes** : utiliser `chown` et `chmod` pour définir le propriétaire et les droits.

```bash
sudo chown -R www-data:www-data /var/www/site-tp
sudo chmod -R 755 /var/www/site-tp
ls -la /var/www/site-tp
```

![tp4_c25](img_tp4/c25.png)

---

## Partie 9 – Validation finale

### Tests finaux


Test SSH :
![tp4_c26](img_tp4/c26.png)

Test Fail2Ban :
![tp4_c26](img_tp4/c26.1.png)

Test Nginx :
![tp4_c26](img_tp4/c26.2.png)

Test transfert:
![tp4_c26](img_tp4/c26.3.png)

Test firewall:
![tp4_c26](img_tp4/c26.4.png)

---

**Fin du TP4**
