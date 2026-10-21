# SmartZone Mali — Back-office professionnel (base de départ)

Ce pack est préparé à partir du fichier `index(1)(2).html` fourni : la boutique utilise déjà Supabase JS et tente d'enregistrer les commandes dans la table `orders`, mais les paramètres Supabase sont vides et le catalogue est actuellement défini dans le JavaScript (`PRODUCTS`).

## Fichiers
- `index.html` : boutique publique d'origine, maintenant reliée à Supabase pour lire le catalogue, la page d'accueil, la page À propos et les paramètres; les images des produits peuvent être fournies par URL.
- `admin.html` : interface d’administration avec connexion Supabase Auth, gestion produits/stock, contenu d’accueil/À propos, commandes et paramètres.
- `supabase_setup.sql` : tables, index, politiques RLS de départ et catalogue d'exemple importé depuis le prototype.

## Mise en place
1. Crée un projet Supabase et garde son URL et sa clé publique (anon/publishable).
2. Dans Supabase → SQL Editor, exécute `supabase_setup.sql`.
3. Dans Authentication → Users, crée l’utilisateur administrateur avec une adresse e-mail et un mot de passe robuste.
4. Dans les paramètres/app_metadata de cet utilisateur, définis le rôle serveur `role: admin`. Le panneau vérifie `app_metadata.role`; n’utilise pas `user_metadata` pour autoriser les droits.
5. Dans `index.html`, renseigne `SUPABASE_URL` et `SUPABASE_ANON_KEY` dans l'objet `CONFIG` (clé publique/publishable uniquement). Ne mets jamais la clé `service_role` dans un fichier HTML.
6. Ouvre `admin.html` via un serveur local ou déploie-le sur le même hébergement que la boutique. Renseigne l’URL et la clé publique Supabase à l'écran de configuration.
7. Dans Supabase Auth, configure le rôle `app_metadata.role = admin` pour ton compte, puis reconnecte-toi.
8. Vérifie la boutique et l'administration avec un produit de test avant publication.

## Sécurité importante
- Ne jamais mettre la clé `service_role` dans `index.html`, `admin.html` ou un dépôt public.
- La clé publique est prévue pour le navigateur; les politiques RLS limitent les écritures administrateur au rôle `app_metadata.role = admin`.
- Le checkout actuel écrit les commandes comme visiteur. La politique publique permet l’insertion, mais pas la lecture des commandes par le public. Avant la mise en production, ajoute une validation côté serveur (Edge Function) pour vérifier les prix, le stock, les quantités et limiter les commandes frauduleuses; ne fais pas confiance aux montants envoyés par le navigateur.
- Les formulaires d’administration sauvegardent les données dans Supabase. **À ce stade, le fichier public fourni n’est pas encore branché pour lire automatiquement le catalogue, le contenu et les paramètres depuis ces tables** : cette intégration front-end est nécessaire pour que les modifications apparaissent réellement dans la boutique. Le checkout existant doit également être ajusté pour mettre à jour les statuts et gérer les erreurs proprement.
- Le champ image du formulaire accepte une URL d’image publique. Pour permettre l’import direct de photos depuis le téléphone, configure ensuite un bucket Supabase Storage privé/public avec politiques de téléversement dédiées; cette première version ne téléverse pas les fichiers elle-même.
- Active les sauvegardes du projet et configure la récupération de compte par e-mail dans Supabase Auth.

## Limites connues de cette première version
- C’est un socle fonctionnel d’administration, pas un déploiement effectué.
- Les produits déjà codés dans `PRODUCTS` ne sont pas automatiquement importés en base; il faudra les saisir/importer et connecter le site public à `products`.
- Les champs de contenu et de paramètres sont stockés en base mais nécessitent le raccordement aux composants du site existant.
- La création/lecture de commandes doit être testée sur le schéma exact du projet avant mise en ligne; si une table `orders` existe déjà avec une autre structure, adapte le SQL au lieu de la recréer.
