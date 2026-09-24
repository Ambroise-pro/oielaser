# Jeu de l'Oie Laser Run

Application web statique (HTML/CSS/JS, sans build) pour animer une séance de
Laser Run façon "jeu de l'oie" en classe.

- `index.html` — le jeu principal : le professeur crée une partie, les élèves
  rejoignent en équipe de 2 via un QR code, et progressent sur un plateau 3D
  (Three.js, avec pions 3D, caméra orbitale à la souris/au doigt) en fonction
  de leurs tirs laser. Synchronisation en temps réel via Firebase (Firestore)
  et authentification anonyme. Les boutons d'action (tir, validation, etc.)
  sont protégés contre les double-déclenchements (rebond du bouton laser,
  double-clic).
- `chrono.html` — un chronomètre solo pour une course Laser Run individuelle
  (tours de course + sessions de tir), indépendant de Firebase.
- `oielogo.jpeg` — logo affiché sur la page d'accueil.
- `app.json` — petit manifeste (titre, point d'entrée, icône).

## Prérequis Firebase

`index.html` utilise le projet Firebase `jeudeloie-81180`. Pour que la partie
fonctionne, dans la [console Firebase](https://console.firebase.google.com/) :

1. **Authentication** → activer le fournisseur **Anonyme**.
2. **Firestore Database** → créer une base (mode production) et autoriser les
   lectures/écritures sur la collection `games` pour les utilisateurs
   authentifiés, par exemple :

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /games/{gameId} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```

La clé `apiKey` présente dans le code est une clé publique cliente Firebase
(normal pour une web app) ; c'est la règle de sécurité Firestore ci-dessus qui
protège réellement les données.

## Déploiement sur Vercel

Le site est 100% statique, aucune configuration de build n'est nécessaire.

1. Sur [vercel.com](https://vercel.com), **Add New → Project**.
2. Importer le dépôt GitHub `Ambroise-pro/oielaser`.
3. Laisser **Framework Preset** sur *Other* et le champ *Build Command* vide
   (Vercel sert directement les fichiers statiques).
4. Déployer : `index.html` sera servi à la racine du domaine.

Vous pouvez aussi déployer en local avec la CLI Vercel :

```bash
npm i -g vercel
vercel --prod
```
