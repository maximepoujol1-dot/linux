# TP 3

    I. Sans que je vous file les réponses à chaques étapes

        A. Base64

            1. Génération d’un fichier binaire

            Créer un fichier data.bin contenant 100 Ko de données binaires aléatoires.
            Vérifier sa taille.

            2. Encodage

            Encoder le fichier en Base64 dans un fichier data.b64.
            Afficher son contenu.
            Comparer la taille de data.bin et data.b64.

            3. Décodage

            Décoder le fichier data.b64 afin d’obtenir un fichier data_restored.bin.
            Vérifier que data.bin et data_restored.bin sont strictement identiques.
            
            4. Questions

                Base64 est-il un chiffrement ? Pourquoi ?

                    -

                Pourquoi la taille du fichier change-t-elle après encodage ?

                    -
                    
                Quel est approximativement le pourcentage d’augmentation ?

                    -
                    
                Quelle méthode permet de vérifier rigoureusement que deux fichiers sont identiques ?

                    -
                    
    B. Chiffrement symétrique – AES

        1. Création d’un message

            Créer un fichier confidentiel.txt contenant :

            Votre nom
            La date
            5 lignes minimum


        2. Chiffrement

            Chiffrer le fichier avec :

            AES 256
            Un sel
            Une dérivation de clé robuste
            Un algorithme de hachage sécurisé

            Le fichier chiffré devra s’appeler :
            confidentiel.enc
            Vérifier que le fichier obtenu est bien binaire.

        3. Déchiffrement

            Déchiffrer le fichier vers :
            confidentiel_dechiffre.txt
            Vérifier que le contenu correspond exactement à l’original.

        4. Analyse

            Chiffrer une seconde fois le même fichier avec le même mot de passe.
            Comparer les deux fichiers chiffrés.

        5. Questions


            Pourquoi les deux fichiers chiffrés sont-ils différents ?

                    -

            Quel est le rôle du sel ?

                    -

            Que se passe-t-il si une option change lors du déchiffrement ?

                    -

            Pourquoi utilise-t-on PBKDF2 ?

                    -

            Quelle est la différence entre encodage et chiffrement ?

                    -


    C. Cryptographie asymétrique – RSA

        1. Génération de clés

            Générer une paire de clés RSA 2048 bits :

            rsa_private.pem
            rsa_public.pem

            Protéger la clé privée par un chiffrement.
            Afficher les paramètres détaillés de la clé privée.
            Afficher les paramètres de la clé publique.
            Comparer les deux.

        2. Chiffrement asymétrique

            Créer un fichier secret.txt.
            Chiffrer ce fichier avec la clé publique.
            Le fichier chiffré devra s’appeler :
            secret.enc
            Déchiffrer ensuite avec la clé privée.

        3. Questions

            Pourquoi la clé privée ne doit-elle jamais être partagée ?

                -

            Pourquoi RSA n’est-il pas adapté au chiffrement de gros fichiers ?

                -

            Quelles différences observe-t-on entre les paramètres d’une clé publique et d’une clé privée ?

                -

            Quel est le rôle du modulo dans RSA ?

                -

            Pourquoi utilise-t-on souvent RSA pour chiffrer une clé AES plutôt qu’un document entier ?

                -


    D. Signature numérique

        
        1. Création et signature

            Créer un fichier contrat.txt.
            Générer son empreinte (hash).
            Signer le fichier avec votre clé privée.
            Le fichier de signature devra s’appeler :
            contrat.sig

        2. Vérification

            Vérifier la signature avec la clé publique.
            Modifier légèrement le fichier contrat.txt.
            Refaire la vérification.

        3.Questions


            Que se passe-t-il après modification du fichier ?
            Pourquoi ?
            Quel est le rôle du hachage dans le mécanisme de signature ?
            Quelle différence entre signature numérique et chiffrement ?

        Bonus (facultatif)

        Mettre en place un chiffrement hybride complet :

        Générer une clé AES aléatoire
        Chiffrer un fichier volumineux avec AES
        Chiffrer la clé AES avec RSA
        Expliquer précisément chaque étape