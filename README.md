# Casino Royal · Programme de fidélité

Application de fidélité pour stand forain, style casino Monte-Carlo.
Ce projet est **totalement indépendant** — il n'est relié à aucun compte existant, il suffit de créer ses propres comptes gratuits.

## Contenu

- `register.html` — inscription client (prénom, nom, téléphone, email)
- `client.html` — espace client : points, 3 jeux (machine à sous, blackjack, roulette), messagerie
- `admin.html` — panneau admin : gestion des clients, points, messagerie
- `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png` — fichiers PWA (installation sur téléphone)
- `firestore.rules` — règles de sécurité de la base de données

## Étape 1 — Créer le projet Firebase (gratuit)

1. Aller sur https://console.firebase.google.com
2. Créer un nouveau projet (ex: "casino-royal-fidelite")
3. Dans le menu de gauche : **Build > Firestore Database** → Créer une base → **mode production**
4. Aller dans l'onglet **Règles** de Firestore, coller tout le contenu du fichier `firestore.rules`, puis **Publier**
5. Toujours dans les paramètres du projet (roue crantée en haut à gauche) → **Paramètres du projet**
6. En bas, section "Vos applications" → cliquer sur l'icône `</>` (Web) → donner un nom → **Enregistrer l'application**
7. Firebase affiche un bloc de code `firebaseConfig = {...}` — copier ces informations

## Étape 2 — Coller la configuration dans les 3 fichiers

Ouvrir **register.html**, **client.html** et **admin.html**, chercher (avec Ctrl+F) :

```
const firebaseConfig = {
  apiKey: "REPLACE_ME",
  ...
```

et remplacer les `"REPLACE_ME"` par les vraies valeurs copiées à l'étape 1.
**Les 3 fichiers doivent avoir exactement la même configuration.**

## Étape 3 — Changer le mot de passe admin

Dans `admin.html`, chercher :

```
const ADMIN_PWD = "casinoroyal2024";
```

et remplacer par le mot de passe de ton choix avant la mise en service.

## Étape 4 — Mettre en ligne (GitHub Pages, gratuit)

1. Créer un compte GitHub si besoin, puis un nouveau dépôt (ex: `casino-royal`)
2. Mettre en ligne les fichiers de ce dossier (via l'interface GitHub "Add file > Upload files", ou `git push`)
3. Dans le dépôt : **Settings > Pages** → Source : "Deploy from a branch" → Branch : `main` / dossier `/ (root)` → Save
4. Après 1-2 minutes, le site est accessible à une adresse du type :
   `https://<nom-utilisateur>.github.io/casino-royal/register.html`

## Le compte client (pseudo + code PIN)

- **Inscription** : le client crée un compte avec pseudo, prénom, nom, téléphone, email et un code à 4 chiffres de son choix
- **Connexion** (visites suivantes, nouvel appareil) : juste pseudo + code à 4 chiffres — pas besoin de retaper l'email/téléphone
- Le code n'est jamais stocké en clair (il est haché), et la connexion reste active sur l'appareil utilisé (pas besoin de se reconnecter à chaque fois)
- **Si un client oublie son code** : dans l'admin, ouvrir sa fiche → bouton "🔑 Réinitialiser le code PIN" → donne un nouveau code à communiquer au client

## La carte fidélité

Chaque client a une carte avec :
- Son **pseudo**, un **QR code** (identifiant unique, utile pour un futur scanner opérateur)
- Son **niveau VIP**, calculé automatiquement selon ses points :
  - 🎲 Joueur débutant (0+), ⭐ Chanceux (100+), 🎰 High Roller (500+), 💎 Diamond (1500+), 👑 Roi de Monte-Carlo (5000+)
  - La couleur de la carte change avec le niveau
- Ses **succès/badges** : 🥇 100 parties jouées, 🎁 20 cadeaux reçus, 🎯 Jackpot (triple 7 à la machine à sous), ⭐ 30 jours de connexion d'affilée, 🎲 1000 points cumulés
- Son **historique** (onglet dédié) : chaque ajout/retrait de points et cadeau remis par l'équipe apparaît automatiquement

Les seuils de niveaux et les conditions des badges sont modifiables dans `client.html` (variables `LEVELS` et `BADGE_DEFS`) et dans `admin.html` (variable `LEVELS`).

## Étape 5 — Activer l'envoi d'emails groupés (optionnel mais recommandé)

L'admin permet d'envoyer un vrai email (pas juste un message dans l'appli) à une sélection de clients, via le service gratuit **EmailJS** (200 emails/mois gratuits, largement suffisant pour un stand).

1. Créer un compte gratuit sur https://www.emailjs.com
2. **Email Services** → "Add New Service" → connecter une adresse Gmail ou Outlook → noter le **Service ID**
3. **Email Templates** → "Create New Template" → dans le corps du template, utiliser ces variables :
   `{{to_email}}`, `{{to_name}}`, `{{subject}}`, `{{message}}` → noter le **Template ID**
4. **Account** (en haut à droite) → **General** → copier la **Public Key**
5. Dans `admin.html`, chercher :
   ```
   const EMAILJS_CONFIG = {
     publicKey: "REPLACE_ME",
     serviceId: "REPLACE_ME",
     templateId: "REPLACE_ME"
   };
   ```
   et remplacer les 3 valeurs.

Sans cette configuration, le bouton d'envoi d'email affichera un message d'avertissement mais l'appli fonctionne normalement pour tout le reste.

## Utilisation sur le stand

- **Inscription client** : ouvrir `register.html` sur une tablette/téléphone, le client remplit son prénom/nom/téléphone/email
- **Espace client** : après inscription, redirection automatique vers `client.html` — il peut jouer aux 3 jeux (limités à 3 tentatives par jeu et par jour) et écrire à l'équipe du stand
- **Admin** : ouvrir `admin.html`, entrer le mot de passe
  - Onglet **Clients** : rechercher un client, cocher un ou plusieurs clients (ou "Tout sélectionner"), puis "Envoyer un email" pour leur écrire directement à leur adresse email (nécessite la configuration EmailJS, étape 5) ; cliquer sur une fiche pour ajouter/retirer des points ou lui envoyer un message dans l'appli
  - Onglet **Messagerie** : activer/désactiver la possibilité pour les clients d'écrire (interrupteur), envoyer un message à tous les clients d'un coup, répondre aux messages reçus

## Les mini-jeux

La machine à sous, le blackjack et la roulette sont **juste des jeux pour le plaisir** : ils n'attribuent aucun point. Les points restent uniquement gérés par l'équipe du stand depuis l'onglet **Clients** de l'admin (ajout/retrait manuel), pour que l'appli reste un vrai programme de fidélité.

## Personnaliser le nom du stand

Dans `admin.html`, onglet **Réglages**, il suffit d'écrire le nom du stand et de cliquer sur "Enregistrer". Ce nom s'affiche automatiquement en haut de `register.html` et `client.html` (pas besoin de toucher au code).

## Notes techniques

- Pas d'authentification Firebase (comme sur X-Games) : la sécurité repose sur le mot de passe admin côté application. Suffisant pour un usage mono-stand.
- Les points et compteurs de jeux sont mis à jour via des transactions Firestore protégées contre les doublons.
- Le format PWA permet à un client d'installer l'appli sur son téléphone ("Ajouter à l'écran d'accueil") pour retrouver facilement ses points.
