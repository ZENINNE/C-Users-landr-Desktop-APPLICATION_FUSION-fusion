# Fusion

Application web Symfony 7.4 pour Fusion Match et Fusion Confess. La base de développement est MySQL, nommée **`fusion`**. Le `.env` fourni utilise le MySQL WAMP local (`root` sans mot de passe) ; cette configuration est réservée à ce PC.

## Migrations et démarrage

Depuis PowerShell, à la racine de ce dossier :

```powershell
composer install
php bin/console doctrine:migrations:migrate --no-interaction
php bin/console app:seed-demo-users
php -d upload_max_filesize=300M -d post_max_size=650M -d max_file_uploads=10 -d max_execution_time=900 -d max_input_time=900 -S 127.0.0.1:8000 -t public
```

Ouvre ensuite [http://127.0.0.1:8000](http://127.0.0.1:8000). MySQL WAMP doit être démarré. La commande de migration est sans risque à relancer : elle applique seulement les migrations non exécutées. `app:seed-demo-users` prépare ou réinitialise les comptes de démonstration locaux.

### Comptes de démonstration

Tous utilisent le mot de passe **`FusionEssai2026!`**.

| Profil | E-mail | Accès |
|---|---|---|
| Administrateur | `admin@fusion.test` | `/admin`, gestion des comptes et des rôles |
| Modérateur | `moderateur@fusion.test` | `/moderation`, revue des messages Confess |
| Testeuse | `awa@fusion.test` | Profil féminin pour tester le match |
| Testeur | `koffi@fusion.test` | Profil masculin pour tester le match |

Ces identifiants sont réservés au développement local. **Ne les utilise pas sur un site public** et change/supprime ces comptes avant le déploiement. Pour tester un match, connecte-toi dans deux profils différents, complète leur âge si nécessaire, puis lance une fusion avec chacun. La limite reste d’une fusion par jour et par compte.

## Fonctions et étapes reportées

- Profil consultable et modifiable séparément : pseudo, e-mail, mot de passe, photo de profil, couverture, galerie, âge, genre, taille, ville, nationalité, pays, téléphone, bio, intérêts et caractère. Les informations partageables sont choisies dans les réglages de confidentialité.
- Critères réciproques de match : genre, ville, âge, taille, nationalité, pays, origines facultatives, caractère et intérêts. Le critère libre est mémorisé mais ne filtre pas encore automatiquement.
- Proposition de match anonyme avec acceptation des deux personnes avant l’ouverture du chat ; salon et messages supprimés après 24 h, score de compatibilité, fin manuelle et signalements.
- Historique des recherches et fusions sans informations d’identité des autres personnes ; profil de l’autre personne visible uniquement après accord explicite et selon ses réglages.
- Boîte Confess structurée entre nouveaux messages et archives, création de cartes image partageables, partage de lien WhatsApp et inscription accessible depuis la page publique de réception.
- Annuaire des comptes actifs en ligne (présence récente), avec consultation des seuls éléments de profil autorisés.
- Console administrateur avec indicateurs, graphiques en barres et courbes, filtres de dates, gestion des comptes, présence en ligne et fiche détaillée avec profils, statistiques, historique et conversations disponibles.
- Fusion Confess : lien anonyme à partager, boîte privée, suppression des messages et rôle de modération.
- Tableau de bord utilisateur, tableau admin, rôles utilisateur/modérateur/admin et suspension/réactivation des comptes.

La confirmation d’adresse par code et la page **« Mot de passe oublié ? » avec réinitialisation** ne sont pas encore activées. Elles seront mises en place ensemble avec l’envoi e-mail avant l’ouverture publique. L’âge est déclaré par l’utilisateur, pas vérifié par pièce d’identité.

## Déploiement

Configure `DATABASE_URL` vers une base MySQL persistante nommée `fusion`, avec un utilisateur SQL dédié, ainsi que `APP_SECRET` dans les variables secrètes de l’hébergeur. N’utilise pas le compte root vide du `.env` en production. Configure HTTPS, sauvegardes et un stockage persistant non public pour `var/uploads/private`.

Sur une base vierge : `php bin/console doctrine:migrations:migrate --no-interaction`. Programme `php bin/console app:purge-expired-fusions` toutes les cinq minutes pour effacer le contenu des salons expirés. `compose.yaml` fournit aussi un service MySQL de développement sur le port hôte 3307 ; les variables `MYSQL_PASSWORD` et `MYSQL_ROOT_PASSWORD` sont requises.

## Nouveautés — expérience V2

- Accueil ivoirien avec « Akwaba », bandeau animé et carrousel illustré qui explique les étapes Fusion.
- Pages admin avec pagination des comptes (4 comptes par page) et des membres en ligne (12 présences par page) ; les filtres sont conservés pendant la navigation.
- Fusion Confess accepte un texte avec une photo JPG/PNG/WebP (10 Mo maximum) ou une vidéo MP4/WebM (300 Mo maximum). Les fichiers sont stockés dans `var/uploads/private` et visibles uniquement par le destinataire connecté.
- Espace admin « Publicités programmées » : créer une campagne texte, image ou vidéo, choisir accueil/profil, partenaire, dates de début et de fin, activer/suspendre ou supprimer. Les campagnes actives apparaissent sur l’accueil et le profil selon leur programmation.
- Les migrations `Version20261004180000`, `Version20261004190000` et `Version20261004200000` ajoutent le stockage publicitaire et les médias Confess. Elles ont été appliquées à la base locale. Sur une autre installation, relance la commande de migration ci-dessus.

Pour les vidéos sur WAMP, ajuste `upload_max_filesize` à `300M` et `post_max_size` à `650M` dans le `php.ini` utilisé par le serveur PHP, puis redémarre Apache. En production, vérifie également que l’hébergeur accepte ces tailles de requête.
Chaque campagne publicitaire accepte jusqu’à huit médias, avec un poids cumulé maximal de 600 Mo. Pour Apache WAMP, configure upload_max_filesize=300M, post_max_size=650M, max_file_uploads=10, max_execution_time=900 et max_input_time=900 dans le php.ini chargé par Apache, puis redémarre-le.

La migration Version20261004210000 ajoute le lien du partenaire et plusieurs médias par publicité.

## Communication, communauté et application mobile

- Dans **Administration → Gérer les informations, suggestions et liens du site**, prépare jusqu’à dix annonces à la fois. Elles peuvent contenir un message écrit, une photo, une vidéo ou un fichier audio, un lien et une date de publication. Après lecture, chaque annonce disparaît de la boîte de l’utilisateur.
- Les annonces se modifient et se suppriment depuis la même interface. Images JPG/PNG/WebP jusqu’à 12 Mo, vidéos MP4/WebM jusqu’à 300 Mo et audios usuels jusqu’à 50 Mo.
- Le formulaire **Suggestions** permet aux membres d’envoyer une idée, puis de suivre son état et la réponse de l’administration. Les administrateurs peuvent répondre, traiter ou supprimer chaque suggestion.
- La page **Télécharger l’application** présente les choix Android, iOS, Windows et macOS. Les liens, les réseaux sociaux et l’affichage du carrousel d’accueil se règlent par l’administration ; les clics de téléchargement sont comptés.
- Le carrousel d’accueil accepte des textes et images personnalisés, y compris les logos Fusion fournis. Les visuels peuvent être réordonnés, masqués, modifiés ou retirés.
- L’administration affiche les nouvelles statistiques et un rapport PDF de communication, avec indicateurs de lecture, suggestions et téléchargements.

La migration `Version20261005090000` crée les tables de ces fonctionnalités. Après mise à jour du projet, exécute `php bin/console doctrine:migrations:migrate --no-interaction` depuis la racine du projet.
