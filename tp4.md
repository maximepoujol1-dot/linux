# TP4 – Administration SSH et Serveur Web Nginx

---

## Partie 1 – Mise en place de l'environnement virtualisé

### 1.1 Installation de la VM Ubuntu

Installez VirtualBox et créez une VM Ubuntu :

- **RAM** : 2 Go minimum
- **Disque** : 20 Go
- **Réseau** : Bridged Adapter

![tp4_c1](img_tp4/c1)

### 1.2 Vérification de la connectivité réseau

Vérifiez que la VM a une IP accessible depuis la machine hôte.

**Pistes** : `ip a`, `ping <IP_VM>`

```bash
ip a
ping <IP_VM>
```

![tp4_c2](img_tp4/c2)

---

## Partie 2 – Serveur SSH

### 2.1 Installation du serveur SSH

Installez le serveur SSH sur la VM.

**Indice** : chercher le paquet `openssh-server`

```bash
sudo apt update
sudo apt install openssh-server
```

![tp4_c3](img_tp4/c3)

### 2.2 Vérification du service SSH

Vérifiez que le service SSH fonctionne et écoute sur un port.

**Piste** : `systemctl status` + `ss` ou `netstat`



![tp4_c4](img_tp4/c4)

### 2.3 Connexion SSH depuis la machine hôte

Connectez-vous depuis la machine hôte :

```bash
ssh etudiant@<IP_VM>
```

![tp4_c5](img_tp4/c5)

### 2.4 Authentification par clé SSH

Générez une clé SSH sur la machine cliente et copiez-la sur le serveur pour tester la connexion sans mot de passe.

**Pistes** : `ssh-keygen`, `ssh-copy-id`

```bash
ssh-keygen -t rsa -b 4096
ssh-copy-id etudiant@<IP_VM>
```

![tp4_c6](img_tp4/c6)
![tp4_c6](img_tp4/c6.2)
![tp4_c6](img_tp4/c6).3

---

## Partie 3 – Sécurisation SSH

### 3.1 Modification de la configuration SSH

Modifiez la configuration SSH sur le serveur pour renforcer la sécurité :

1. **Interdisez l'accès root**
2. **Désactivez l'authentification par mot de passe**
3. **Changez le port par défaut (22)** pour réduire les tentatives de brute-force

**Piste** : recherchez le fichier `/etc/ssh/sshd_config` et les options `PermitRootLogin`, `PasswordAuthentication`

```bash
sudo nano /etc/ssh/sshd_config
```

Modifications à effectuer :
```
Port 2222
PermitRootLogin no
PasswordAuthentication no
```

![tp4_c7](img_tp4/c7)

### 3.2 Redémarrage et test

Redémarrez le service SSH et testez la connexion avec le nouveau port depuis la machine cliente.

```bash
sudo systemctl restart ssh
ssh -p 2222 etudiant@<IP_VM>
```

![tp4_c8](img_tp4/c8)

### 3.3 Création d'un alias SSH

Créez un alias SSH dans `~/.ssh/config` pour simplifier les connexions.

```bash
nano ~/.ssh/config
```

Contenu :
```
Host serveur-tp
    HostName <IP_VM>
    User etudiant
    Port 2222
    IdentityFile ~/.ssh/id_rsa
```

![tp4_c9](img_tp4/c9)

---

## Partie 4 – Transfert de fichiers

### 4.1 Transfert avec SCP

Transférez un fichier depuis la machine cliente vers le serveur :

```bash
scp fichier.txt serveur-tp:/home/etudiant/
```

![tp4_c10](img_tp4/c10)

### 4.2 Transfert avec SFTP

Explorez les commandes `put`, `get`, `ls` pour transférer et naviguer sur le serveur.

```bash
sftp serveur-tp
```

Commandes SFTP :
```
ls
put fichier.txt
get fichier_distant.txt
```

![tp4_c11](img_tp4/c11)

### 4.3 Synchronisation avec RSYNC

Synchronisez un dossier entre client et serveur.

**Pistes** : utilisez les options `-a` (archive), `-v` (verbose), `-z` (compression)

```bash
rsync -avz /chemin/local/ serveur-tp:/chemin/distant/
```

![tp4_c12](img_tp4/c12)

---

## Partie 5 – Analyse des logs et sécurité

### 5.1 Suivi des logs d'authentification

Suivez les logs d'authentification pour observer les connexions SSH :

```bash
sudo tail -f /var/log/auth.log
```

![tp4_c13](img_tp4/c13)

### 5.2 Installation et configuration de Fail2Ban

Installez Fail2Ban et testez un bannissement après plusieurs tentatives échouées.

```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Configuration :
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

![tp4_c14](img_tp4/c14)

Vérification :
```bash
sudo fail2ban-client status sshd
```

![tp4_c15](img_tp4/c15)

---

## Partie 6 – Tunnel SSH

### 6.1 Tunnel local

Créez un tunnel local pour accéder à un service web distant depuis la machine cliente.

```bash
ssh -L 8080:localhost:80 serveur-tp
```

![tp4_c16](img_tp4/c16)

### 6.2 Tunnel distant

Créez un tunnel distant pour permettre l'accès SSH au client via le serveur.

```bash
ssh -R 9090:localhost:22 serveur-tp
```

![tp4_c17](img_tp4/c17)

---

## Partie 7 – Nginx et HTTPS

### 7.1 Installation de Nginx

Installez Nginx sur la VM.

```bash
sudo apt install nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

![tp4_c18](img_tp4/c18)

### 7.2 Création du site test

Créez un site test dans `/var/www/site-tp` et un fichier `index.html` avec un message de bienvenue.

```bash
sudo mkdir -p /var/www/site-tp
sudo nano /var/www/site-tp/index.html
```

Contenu de `index.html` :
```html
<!DOCTYPE html>
<html>
<head>
    <title>TP4 - Site Test</title>
</head>
<body>
    <h1>Bienvenue sur le site test du TP4</h1>
    <p>Serveur Nginx fonctionnel !</p>
</body>
</html>
```

![tp4_c19](img_tp4/c19)

### 7.3 Configuration Nginx pour HTTP

Configurez Nginx pour servir ce site sur HTTP.

```bash
sudo nano /etc/nginx/sites-available/site-tp
```

Configuration :
```nginx
server {
    listen 80;
    server_name _;
    root /var/www/site-tp;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

Activation du site :
```bash
sudo ln -s /etc/nginx/sites-available/site-tp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

![tp4_c20](img_tp4/c20)

### 7.4 Génération du certificat SSL

Générez un certificat auto-signé pour HTTPS.

**Piste** :
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/nginx-selfsigned.key \
    -out /etc/ssl/certs/nginx-selfsigned.crt
```

![tp4_c21](img_tp4/c21)

### 7.5 Configuration HTTPS avec redirection

Configurez la redirection HTTP → HTTPS.

```bash
sudo nano /etc/nginx/sites-available/site-tp
```

Configuration complète :
```nginx
server {
    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name _;
    
    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
    
    root /var/www/site-tp;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

```bash
sudo nginx -t
sudo systemctl reload nginx
```

![tp4_c22](img_tp4/c22)

### 7.6 Test du site HTTPS

Testez le site depuis le client :

```bash
curl -k https://<IP_VM>
```

![tp4_c23](img_tp4/c23)

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

![tp4_c24](img_tp4/c24)

### 8.2 Vérification des permissions

Vérifiez les permissions sur `/var/www/site-tp` pour que Nginx puisse lire les fichiers.

**Pistes** : utiliser `chown` et `chmod` pour définir le propriétaire et les droits.

```bash
sudo chown -R www-data:www-data /var/www/site-tp
sudo chmod -R 755 /var/www/site-tp
ls -la /var/www/site-tp
```

![tp4_c25](img_tp4/c25)

---

## Partie 9 – Validation finale

### Checklist de validation

- ✅ **SSH fonctionnel** sur port personnalisé et authentification par clé uniquement
- ✅ **Fail2Ban actif** et opérationnel
- ✅ **Transferts de fichiers fonctionnels** (SCP, SFTP, RSYNC)
- ✅ **Nginx accessible** en HTTP et HTTPS avec redirection automatique HTTP → HTTPS
- ✅ **Certificat SSL** auto-signé valide
- ✅ **Firewall configuré** et permissions correctes sur `/var/www/site-tp`

### Tests finaux

```bash
# Test SSH
ssh serveur-tp

# Test transfert
scp test.txt serveur-tp:~/

# Test Fail2Ban
sudo fail2ban-client status

# Test Nginx
curl -I http://<IP_VM>
curl -k https://<IP_VM>

# Test firewall
sudo ufw status
```

![tp4_c26](img_tp4/c26)

---

**Fin du TP4**
