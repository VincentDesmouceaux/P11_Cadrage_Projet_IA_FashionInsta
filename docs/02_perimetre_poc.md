# P11 — Délimitation du périmètre du Proof of Concept (PoC)

**Projet :** Fashion-Insta — Recommandation vestimentaire à partir de photographies  
**Phase :** Mission, partie 1 — Cadrage du PoC  
**Statut :** Proposition à valider avec Alicia et le mentor

## 1. Rappel de l'objectif

Avant de financer le développement complet d'une application mobile, Fashion-Insta souhaite vérifier qu'une solution d'intelligence artificielle peut proposer des articles de son catalogue visuellement pertinents à partir d'une photographie fournie par un utilisateur.

Le PoC a pour objectif de **tester la faisabilité et la pertinence de cette fonctionnalité centrale**. Il ne doit ni construire l'application complète, ni prouver à lui seul les objectifs de croissance du chiffre d'affaires sur 24 mois.

## 2. Périmètre produit proposé

### Catégorie pilote : pantalons et jeans

Nous proposons de limiter l'expérimentation aux **pantalons et jeans** du catalogue Fashion-Insta.

| Indicateur métier | Valeur communiquée |
| --- | ---: |
| Nombre de références uniques vendues dans la catégorie | 120 |
| Chiffre d'affaires annuel estimé de la catégorie | 2,8 M€ |
| Part du chiffre d'affaires global | 27 % |

**Justification :** cette catégorie combine un **enjeu commercial important** (première catégorie en chiffre d'affaires) et un **volume raisonnable** de références pour une première expérimentation. La qualité et la disponibilité réelle des photographies devront néanmoins être confirmées.

Cette sélection est une **hypothèse de cadrage**, à valider avec Alicia. Les T-shirts et tops (200 références, 1,1 M€ de chiffre d'affaires annuel estimé) constituent un scénario alternatif si la disponibilité ou la qualité des images de pantalons est insuffisante.

### Cas d'usage unique

1. Une personne fournit une **photo montrant clairement un pantalon ou un jean**, dans des conditions de prise de vue définies pour l'expérimentation.
2. Le système compare cette photo aux représentations visuelles des références de la catégorie retenue.
3. Le système renvoie une **liste courte de pantalons et jeans du catalogue** jugés similaires ou pertinents sur le plan visuel.
4. Des évaluateurs métier jugent si les propositions correspondent raisonnablement au vêtement fourni en entrée.

**Point de vocabulaire :** pour ce PoC, « recommandation » désigne prioritairement la **recherche d'articles visuellement similaires**. Elle ne désigne pas encore la composition d'une tenue assortie ni une personnalisation fondée sur l'historique d'un client.

## 3. Fonctionnalités incluses et exclues

| Inclus dans le PoC | Exclu de ce premier PoC |
| --- | --- |
| Photo d'entrée d'un pantalon ou jean | Application mobile complète |
| Catalogue pilote de pantalons et jeans | Comptes utilisateurs et garde-robe numérique |
| Préparation d'un extrait de données produits | Essayage virtuel sur photo |
| Comparaison et classement de références visuellement pertinentes | Changement de couleur ou de coupe dans l'interface |
| Restitution simple d'une liste de recommandations pour les tests | Tendances, marques favorites, blogs et influenceurs |
| Évaluation métier sur un jeu de photos de test | Personnalisation comportementale et moteur commercial à grande échelle |
| Vérification des contraintes de confidentialité des photos | Déploiement de production et montée en charge à 400 000 utilisateurs |

Les exigences RGPD ne sont **pas exclues** du PoC : elles conditionnent la collecte, l'accès, la conservation et, le cas échéant, la suppression des photos de test.

## 4. Données nécessaires à l'expérimentation

### Catalogue interne

Pour les références sélectionnées :

- Identifiant de référence produit.
- Photographies disponibles (idéalement plusieurs vues par référence).
- Libellé ou description du produit.
- Tags de style existants, si disponibles et suffisamment fiables.
- Catégorie et éventuelles caractéristiques utiles à l'analyse des résultats.

Les prix peuvent être conservés pour le contexte métier, mais **ne sont pas indispensables** au test de similarité visuelle.

**Prérequis :** une extraction et une consolidation des données par le Data Engineer, car les sources ne sont pas centralisées. Le document métier indique que l'extraction initiale de données sur l'ensemble des références a nécessité **3 jours-homme** ; cela constitue une référence historique, et non encore une estimation définitive pour le PoC.

### Photographies de test

Prévoir, à titre d'hypothèse de travail, **30 à 50 photos de test** de pantalons ou jeans prises dans des conditions variées (éclairage, arrière-plan, orientation, portés ou non). Le nombre final dépendra de la diversité constatée et du protocole d'évaluation.

Les photos de test ne sont **pas annoncées comme déjà disponibles**. Il faudra vérifier leur origine, les droits d'utilisation et, pour les photos de personnes identifiables, la base légale et les modalités de protection adaptées. Des photos de vêtements seuls, licites et représentatives du cas d'usage, peuvent limiter les risques au démarrage.

## 5. Hypothèses à vérifier

- Les 120 références citées sont suffisamment représentatives et leurs visuels sont accessibles.
- Le système peut extraire des caractéristiques visuelles utiles même lorsque les photos d'entrée diffèrent des photos commerciales.
- Les équipes métier peuvent définir des critères compréhensibles de « similarité » : type de vêtement, coupe, couleur, matière ou style.
- Un jeu de test indépendant du catalogue utilisé pour préparer le système peut être constitué légalement.
- Les conditions d'accès aux services Microsoft Azure autorisent une expérimentation de cette nature.

## 6. Décisions différées aux étapes suivantes

**Étape 4 — Choix des approches IA :** comparer les méthodes techniques envisageables, leurs données d'entrée, leurs avantages et limites, en tenant compte de l'environnement Azure.

**Étape 5 — Critères de succès et ressources :** définir un protocole d'évaluation, un seuil de réussite mesurable et pertinent pour le métier, les profils mobilisés, les jours-homme et la durée du PoC.

**Étape 6 — Présentation COMEX :** synthétiser le cadrage dans le livrable `.pptx` demandé par OpenClassrooms.

## 7. Questions à soumettre à Alicia ou au mentor

1. La catégorie **pantalons et jeans** est-elle bien la meilleure catégorie pilote au regard de l'intérêt métier ?
2. Peut-on récupérer rapidement les photographies et les métadonnées des **120 références** ?
3. Les premiers tests doivent-ils porter sur des photos de **vêtements seuls**, **portés**, ou les deux ?
4. Quels critères concrets permettront à des évaluateurs de considérer deux pantalons comme « du même style » ?
5. Qui validera les recommandations proposées pendant l'expérimentation ?

## 8. Décision de cadrage proposée

**Nous recommandons un PoC de recommandation par similarité visuelle limité aux pantalons et jeans, sur un extrait de catalogue de 120 références, avec une évaluation sur un jeu de photographies de test distinct.**

Ce périmètre vise à établir rapidement si la fonctionnalité centrale a une valeur démontrable, avant toute décision de développement d'une application mobile complète.


## 9. Articulation avec l'expression de besoins complète (Partie 2)

La solution complète vise **875 références sur neuf catégories** et couvre les cinq styles de l'entreprise (essentiel urbain, casual chic, professionnel moderne, streetwear et bohème féminin). Le choix de **120 pantalons et jeans** est un *périmètre expérimental*, non une réduction définitive de la roadmap. Les **950 000 visiteurs web annuels**, les **11 magasins** et les **400 000 utilisateurs annuels prévisionnels de l'application** décrivent trois aspects distincts du marché et ne déterminent pas, sans hypothèses, le volume de requêtes IA.

| Besoin de la solution cible | Décision de périmètre pour ce PoC | Suite du programme |
|---|---|---|
| Moteur image → recommandations | **Tester** : Top 5 / catalogue pantalons-jeans | Étendre au catalogue actif après validation |
| Compte nominatif, capture et garde-robe | Non réalisé | Intégration Software et gestion sécurisée des photos |
| Préférences de styles et marques | Non personnalisé dans le PoC | Filtrage/reranking au MVP envisagé |
| Blogs, sites et influenceurs | Non testé | Exploration des sources autorisées et fraîcheur des tendances |
| Feedback utilisateur | Jugement métier des résultats pendant les essais | Collecte de feedback dans le pilote de production |
| Essayage virtuel et modification de couleurs/coupes | Non réalisé | Étude générative distincte, chiffrage spécifique |
| Désinscription, accès, rectification, suppression, rétention et purge | Protection et suppression des **photos de test** | Fonctionnement complet à définir avec le DPO |

Le détail des fonctions cible et les informations chiffrées de l'entreprise sont centralisés dans `01_analyse_besoins_metier.md`. L'équipe Data/IA ne chiffre pas le frontend ou l'acte d'achat, conformément au périmètre de la Partie 2.
