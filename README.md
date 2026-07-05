# Portfolio

Portfolio personnel statique (HTML / CSS / JS vanilla, sans build), présentant mes projets techniques (électronique, réseau, IoT) et mes analyses & critiques (jeux vidéo, musique).

Le contenu est rédigé en Markdown, injecté dynamiquement dans des pages HTML génériques via [marked.js](https://github.com/markedjs/marked). Aucun framework, aucune étape de build : tout tourne directement en statique (compatible GitHub Pages).

## Aperçu

- **Accueil** — dashboard type "bento", chargé depuis `accueil.md`
- **Projets** — grille de mes réalisations techniques (Électronique & IoT / Système & Réseau)
- **Analyses & critique** — grille de mes analyses (Jeux vidéo / Musique), avec vue article détaillée

## Fonctionnalités

- 🌓 **Thème clair / sombre**, mémorisé via `localStorage`
- 📄 **Contenu 100 % Markdown** avec frontmatter (titre, catégorie, date, statut, note, etc.)
- 🔎 **Filtres par catégorie** sur les grilles Projets / Analyses
- 🔗 **Routing par hash** (`#project=...`, `#article=...`) : liens directs, retour/précédent navigateur fonctionnels
- 📍 **Restauration de la position de scroll** au retour sur une page
- 🎧 **Lecteur audio intégré** (position et volume mémorisés) pour les analyses musicales
- 🖼️ **Scrollytelling léger** : captures d'écran qui accompagnent le paragraphe correspondant
- 📱 **Responsive mobile** : la navigation passe en barre du bas sur petit écran pour laisser toute la largeur au texte


## Stack technique

- HTML / CSS / JavaScript natifs (aucune dépendance à installer)
- [marked.js](https://github.com/markedjs/marked) via CDN, pour le rendu Markdown → HTML
- Variables CSS pour la gestion des thèmes clair/sombre
- Hébergement statique (GitHub Pages)

## Auteur

**Lucas** ([@zwakss_](https://x.com/zwakss_))
