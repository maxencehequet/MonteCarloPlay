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

## Utilisation sur le stand

- **Inscription client** : ouvrir `register.html` sur une tablette/téléphone, le client remplit son prénom/nom/téléphone/email
- **Espace client** : après inscription, redirection automatique vers `client.html` — il peut jouer aux 3 jeux (limités à 3 tentatives par jeu et par jour) et écrire à l'équipe du stand
- **Admin** : ouvrir `admin.html`, entrer le mot de passe
  - Onglet **Clients** : rechercher un client, ajouter/retirer des points manuellement, lui envoyer un message
  - Onglet **Messagerie** : activer/désactiver la possibilité pour les clients d'écrire (interrupteur), envoyer un message à tous les clients d'un coup, répondre aux messages reçus

## Les mini-jeux

La machine à sous, le blackjack et la roulette sont **juste des jeux pour le plaisir** : ils n'attribuent aucun point. Les points restent uniquement gérés par l'équipe du stand depuis l'onglet **Clients** de l'admin (ajout/retrait manuel), pour que l'appli reste un vrai programme de fidélité.

## Personnaliser le nom du stand

Dans `admin.html`, onglet **Réglages**, il suffit d'écrire le nom du stand et de cliquer sur "Enregistrer". Ce nom s'affiche automatiquement en haut de `register.html` et `client.html` (pas besoin de toucher au code).

## Notes techniques

- Pas d'authentification Firebase (comme sur X-Games) : la sécurité repose sur le mot de passe admin côté application. Suffisant pour un usage mono-stand.
- Les points et compteurs de jeux sont mis à jour via des transactions Firestore protégées contre les doublons.
- Le format PWA permet à un client d'installer l'appli sur son téléphone ("Ajouter à l'écran d'accueil") pour retrouver facilement ses points.
