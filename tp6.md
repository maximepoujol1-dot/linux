# TP -- Mise en place d'un serveur OpenVPN sur Ubuntu Server


À l'issue de ce TP, vous devrez être capables de :

-   Installer et configurer OpenVPN sur Ubuntu Server
-   Mettre en place une infrastructure de certificats (PKI)
-   Comprendre le rôle du NAT et du routage IP
-   Générer un profil client fonctionnel
-   Diagnostiquer un service système en échec



# Mise en place

Vous devez mettre en place :

-   D'une VM Ubuntu Server LTS
-   D'un réseau NAT
-   Maintenant qu'on sait faire des connexions SSH, ca serait dommage de se priver


# Préparation du système

-   Mettre à jour le système
-   Installer les paquets nécessaires : openvpn et easy-rsa

# Partie 1 : Comprendre la PKI

### Questions

1.  À quoi sert une autorité de certification (CA) ?

    - c'est un tiers de confiance qui agit pour la sécurisation des communications et des transactions sur Internet.

2.  Quelle différence entre clé privée et certificat ?

    - la clé privée sert à déchiffrer les messages chiffrés à l'aide de la clé publique correspondante. 
    
    - Un certificat numérique signé est un moyen standard pour vérifier l'authenticité d'une entité telle qu'un serveur, un client ou une application.

3.  Pourquoi un serveur VPN a-t-il besoin de certificats ?

    - Les sites Web ont besoin de certificats SSL pour sécuriser les données des utilisateurs, vérifier qui est le propriétaire du site Web, empêcher les criminels de créer une version frauduleuse du site et obtenir la confiance des utilisateurs.



# Création de l'infrastructure Easy-RSA


-   Créer un environnement PKI
-   Générer :
    -   Une CA
    -   Un certificat serveur
    -   Un certificat client
    -   Les paramètres Diffie-Hellman
    -   Une clé TLS supplémentaire

![tp6_c1](img_tp6/c1.png)

### Questions : 

1. Où Easy-RSA crée-t-il ses fichiers ?

    -

2. Que contient le dossier `pki/` ?

    -

3. Quelle est la différence entre `gen-req` et `sign-req` ?

    -

4. Que se passe-t-il si vous oubliez de signer un certificat ?

    -


# Partie 2 : Configuration du serveur OpenVPN

Créer un fichier de configuration serveur.
`/etc/openvpn/server/server.conf`

### Éléments attendus

-   Port d'écoute
-   Protocole
-   Interface virtuelle
-   Réseau attribué aux clients
-   Références aux certificats

![tp6_c2](img_tp6/c2.png)

### Questions : 

1. Que signifie `dev tun` ?

    -

2. Quelle est la différence entre UDP et TCP pour un VPN ?

    -

3. Quelle plage IP choisir pour le VPN ? Pourquoi ?

    -



# Routage et NAT

-   Activer le forwarding IP
-   Mettre en place une règle NAT pour avoir l'accès internet depuis le vpn

![tp6_c3](img_tp6/c3.png)

### Questions : 

1.   Où se configure le paramètre `ip_forward` ?
    
    -

2.   Quelle commande permet d'afficher les règles NAT actuelles ?

    -

3.  Pourquoi faut-il "masquerader" le réseau VPN ?

    -



# Démarrage et analyse du service

-   Démarrer le service OpenVPN
-   Vérifier son état

![tp6_c4](img_tp6/c4.png)

### Si le service échoue :

-   Quelle commande permet d'afficher les logs système d'un service ?
-   Quelle est la différence entre `status` et `journalctl` ?
-   Les chemins vers les certificats sont-ils corrects ?


![tp6_c5](img_tp6/c5.png)

# Partie 3 : Création du profil client

Créer un fichier `.ovpn` fonctionnel.

### Éléments à inclure

-   Adresse publique du serveur
-   Certificat CA
-   Certificat client
-   Clé privée client
-   Paramètres de chiffrement
-   Authentification TLS

![tp6_c6](img_tp6/c6.png)

### Questions : 

1.   Comment intégrer un certificat directement dans un fichier `.ovpn` ?

    -

2.   Pourquoi la clé privée ne doit-elle jamais être partagée
    publiquement ?
    
    -



# Tests et validation

Vous devez être capables de :

-   Établir une connexion VPN
-   Vérifier l'adresse IP obtenue
-   Vérifier l'accès Internet via le tunnel

![tp6_c7](img_tp6/c7.png)

### Questions :

1.   Comment vérifier que votre trafic passe par le VPN ?

    -

2.   Que se passe-t-il si le port 1194 est bloqué ?

    -



