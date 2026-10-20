# P11 — Analyse des besoins métier

## 1. Contexte

Fashion-Insta est une entreprise de distribution d'articles vestimentaires disposant de 11 magasins physiques et d'un site e-commerce.

L'entreprise souhaite développer une application mobile de recommandation vestimentaire basée sur l'intelligence artificielle afin d'améliorer la découverte de ses produits et de favoriser ses ventes.

Le projet est porté par Alicia, VP Product. Une présentation de cadrage doit être soumise au COMEX dans trois semaines afin d'obtenir l'autorisation de lancer un Proof of Concept (PoC).

## 2. Problématique métier

Comment permettre aux clients de Fashion-Insta de découvrir, à partir des photos de leurs propres vêtements, des articles du catalogue correspondant à leurs préférences vestimentaires ?

La solution devra apporter une valeur ajoutée mesurable par rapport à une recherche traditionnelle par catégories ou mots-clés.

## 3. Objectifs business

- Améliorer l'expérience de découverte des produits.
- Faciliter l'identification de vêtements correspondant aux goûts des clients.
- Favoriser la découverte et l'achat de nouveaux articles.
- Contribuer à la fidélisation des utilisateurs.
- Soutenir les objectifs de croissance du chiffre d'affaires e-commerce et en magasin.

Les prévisions marketing de croissance du chiffre d'affaires restent des hypothèses commerciales, distinctes de la validation technique du PoC.

## 4. Données existantes

Fashion-Insta dispose de 875 références uniques vendues, réparties en neuf catégories de produits.

Pour chaque référence, plusieurs informations sont disponibles :

- Images des produits, sous différents angles, portés ou non.
- Description sommaire.
- Prix de vente.
- Tags de style vestimentaire.

Ces données ne sont actuellement pas centralisées dans un système unique. Leur extraction et leur consolidation nécessitent une intervention de l'équipe Data Engineering.

L'extraction ayant servi à établir les chiffres métier a nécessité trois jours-homme de Data Engineer.

## 5. Fonctionnalité centrale du PoC

La fonctionnalité prioritaire est la recommandation d'articles vestimentaires à partir d'une photographie.

Le fonctionnement attendu est le suivant :

1. Recevoir une photographie d'un vêtement.
2. Analyser ses caractéristiques visuelles à l'aide d'une approche IA.
3. Comparer ces caractéristiques avec celles des articles du catalogue Fashion-Insta.
4. Identifier les produits les plus similaires ou stylistiquement pertinents.
5. Présenter une liste de recommandations.
6. Évaluer leur pertinence auprès de personnes représentatives du besoin métier.

Ce fonctionnement est une hypothèse initiale, à préciser pendant le cadrage technique.

## 6. Fonctionnalités hors du périmètre initial

Les fonctionnalités suivantes ne sont pas nécessaires à la première validation de faisabilité :

- Création et gestion des comptes clients.
- Gestion complète d'une garde-robe numérique.
- Essayage virtuel des vêtements.
- Modification interactive des couleurs ou modèles.
- Recommandations basées sur les blogs et influenceurs.
- Développement de l'application mobile complète.
- Déploiement d'une solution destinée aux 400 000 utilisateurs annuels prévisionnels.

Ces fonctionnalités restent des besoins du produit final.

## 7. Contraintes identifiées

- Utilisation de l'environnement Microsoft Azure pour la solution envisagée.
- Disponibilité et qualité des photographies du catalogue.
- Hétérogénéité des sources de données.
- Nécessité de définir précisément la pertinence d'une recommandation.
- Prise en compte de la protection des données personnelles, notamment des photographies des utilisateurs.
- Durée d'expérimentation limitée et maîtrise des ressources mobilisées.

## 8. Questions à clarifier

- Quels articles du catalogue sont actuellement commercialisés et disponibles ?
- Quel niveau de qualité et d'homogénéité présentent les images produits ?
- Que signifie exactement « même style » pour les équipes métier ?
- Existe-t-il des photos représentatives des utilisateurs pouvant être utilisées légalement pour les tests ?
- Qui pourra évaluer la pertinence des recommandations ?
- Quel périmètre de produits permettra de démontrer le plus rapidement la faisabilité ?
- Quels services Azure sont accessibles à l'équipe projet ?

## 9. Conclusion provisoire

Le besoin prioritaire de Fashion-Insta est de valider la faisabilité d'un moteur de recommandation visuelle capable d'associer des photographies de vêtements à des références pertinentes du catalogue.

Le périmètre exact, les approches IA, les données de test, les métriques de succès, la durée et les profils mobilisés seront définis dans les étapes suivantes du cadrage.
