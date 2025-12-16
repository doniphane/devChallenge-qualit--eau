# 💧 Qualité de l'Eau Potable - France

![Application Screenshot](public/Screenshot%202025-12-16%20203004.png)

🌐 **[Voir la démo en ligne](https://dev-challenge-qualite-eau.vercel.app/)**

Une application web moderne pour consulter la qualité de l'eau potable en France, créée dans le cadre du **défi Dev de Yohan Dev**.

![Vue.js](https://img.shields.io/badge/Vue.js-3.5-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind-3.4-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-7.2-646CFF?style=for-the-badge&logo=vite&logoColor=white)

## 🎯 Le Challenge

**Défi proposé par [Yohan Dev](https://www.youtube.com/@yoandevco)**

Créer une application permettant aux citoyens français de consulter facilement les résultats des contrôles sanitaires de l'eau potable de leur commune en utilisant l'API Hub'Eau du gouvernement.

### Objectifs du défi :

- 🔍 Rechercher une commune par son nom ou code INSEE
- 💧 Afficher les résultats des analyses de l'eau (Nitrates, Dureté, Bactériologie, etc.)
- 📊 Visualiser l'historique des contrôles sanitaires avec des graphiques

## 🛠️ Technologies Utilisées

### Frontend

- **Vue.js 3.5** - Framework JavaScript progressif
- **TypeScript 5.9** - Typage statique
- **Vite 7.2** - Build tool ultra-rapide
- **Tailwind CSS 3.4** - Framework CSS utility-first
- **shadcn-vue** - Composants UI modernes et accessibles
- **Lucide Vue Next** - Icônes SVG

### Visualisation

- **Chart.js 4.4** - Graphiques interactifs
- **chartjs-adapter-date-fns** - Gestion des dates

### API

- **Hub'Eau API** - Données officielles du gouvernement français
  - Endpoint communes: `/qualite_eau_potable/communes_udi`
  - Endpoint analyses: `/qualite_eau_potable/resultats_dis`

## 📦 Installation

1. **Cloner le repository**

```bash
git clone <url-du-repo>
cd qualitéeau
```

2. **Installer les dépendances**

```bash
pnpm install
# ou
npm install
```

3. **Lancer le serveur de développement**

```bash
pnpm run dev
# ou
npm run dev
```

## 🔗 API Hub'Eau

### Documentation

- [Documentation officielle](https://hubeau.eaufrance.fr/page/api-qualite-eau-potable)
- [API Documentation](https://hubeau.eaufrance.fr/api/v1/qualite_eau_potable/api-docs)

### Exemples d'endpoints utilisés

**Recherche de communes :**

```
GET /api/v1/qualite_eau_potable/communes_udi?nom_commune=Paris&size=100
```

**Résultats d'analyses :**

```
GET /api/v1/qualite_eau_potable/resultats_dis?code_commune=75056&size=500&sort=desc
```

## 📄 Licence

Ce projet est à but éducatif et utilise des données publiques du gouvernement français.

---

**⭐ Si vous aimez ce projet, n'hésitez pas à lui donner une étoile!**

💧 _L'eau de qualité, c'est la santé!_
