# P11 — Analyse des besoins métier

**Entreprise :** Fashion-Insta  
**Porteuse :** Alicia, VP Product  
**Objet :** cadrage du PoC IA et vision de la solution cible  
**Statut :** synthèse du document métier transmis, complétée par des hypothèses de cadrage explicitement signalées.

## 1. Contexte et décision attendue

Fashion-Insta commercialise des vêtements via **11 magasins physiques** et un **site e-commerce**. L'entreprise souhaite proposer, dans une application mobile, des recommandations d'articles de son catalogue à partir de photos des vêtements appréciés par ses clients et, à terme, de préférences, d'une garde-robe numérique et de tendances.

Alicia doit présenter un cadrage au **COMEX dans trois semaines** afin d'obtenir l'autorisation de lancer un **Proof of Concept (PoC)**. Cette échéance de préparation du cadrage est distincte du **PoC proposé de 15 jours ouvrés après décision**.

## 2. Chiffres clés 2024 — données communiquées par Alicia

| Indicateur | Valeur |
|---|---:|
| Chiffre d'affaires global | **10,4 M€ / an** |
| Chiffre d'affaires e-commerce | **5,2 M€ / an** |
| Chiffre d'affaires magasins | **5,2 M€ / an** |
| Magasins physiques | **11** |
| Chiffre d'affaires moyen par magasin | **≈ 472 000 €/an** (5,2 M€ / 11 ≈ 472 727 €) |
| Visiteurs uniques annuels du site e-commerce | **950 000** |
| Références uniques vendues | **875**, réparties en **9 catégories** |
| Utilisateurs de l'application estimés à terme | **400 000 au moins une fois par an**, dont plusieurs dizaines de milliers très actifs |

**Précision de dimensionnement :** les 950 000 visiteurs uniques web et les 400 000 futurs utilisateurs annuels de l'application sont des métriques **différentes**. Elles ne permettent pas de calculer un taux de conversion, ni de déduire la concurrence des appels IA sans hypothèses supplémentaires.

### 2.1. Catalogue et répartition du chiffre d'affaires

Les valeurs ci-dessous sont reprises **telles que fournies** dans la synthèse métier, sans recalcul silencieux des parts affichées.

| Catégorie | CA annuel communiqué | Part du CA communiquée | Références uniques |
|---|---:|---:|---:|
| Pantalons & jeans | 2 800 000 € | 27 % | 120 |
| Chemises & blouses | 2 000 000 € | 19 % | 65 |
| Vestes & manteaux | 1 600 000 € | 15 % | 90 |
| T-shirts & tops | 1 100 000 € | 10,5 % | 200 |
| Robes & jupes | 900 000 € | 8,5 % | 75 |
| Costumes & tailleurs | 700 000 € | 6,5 % | 40 |
| Accessoires | 600 000 € | 5,8 % | 150 |
| Chaussures | 400 000 € | 3,8 % | 100 |
| Lingerie | 300 000 € | 3,9 % | 35 |
| **Total** | **10 400 000 €** | **100 % selon les parts fournies** | **875** |

**Anomalie source à clarifier :** la ligne *Lingerie* indique 300 000 € et 3,9 %, alors que 300 000 / 10 400 000 ≈ **2,88 %**. Les pourcentages affichés par le métier totalisent bien 100 %, mais ne correspondent pas tous exactement au ratio des montants. Ne pas présenter cette différence comme une erreur corrigée de manière certaine sans échange avec Alicia.

### 2.2. Référentiel de styles communiqué

Les cinq styles proposés par Fashion-Insta sont :

| Style | Description métier communiquée |
|---|---|
| Essentiel urbain | Pièces polyvalentes, ville et bureau : pantalons, blouses, vestes |
| Casual chic | Pièces confortables et élégantes : tops, jeans stylisés |
| Professionnel moderne | Costumes légers, chemises ajustées, tailleurs |
| Streetwear | T-shirts graphiques, vêtements oversize |
| Bohème féminin | Robes fluides, imprimés discrets, couleurs naturelles |

**Conséquence pour l'IA :** ces styles constituent un vocabulaire initial de classification/évaluation des préférences, mais ne remplacent pas les attributs visuels plus fins (coupe, silhouette, motif, matière apparente, couleur). La taxonomie doit être validée par le merchandising.

## 3. Problématique métier

**Comment permettre à un client de Fashion-Insta de découvrir, à partir de photos de ses propres vêtements, des références du catalogue correspondant à ses goûts, au-delà d'une navigation traditionnelle par catégorie ou mot-clé ?**

Le moteur devra contribuer à une découverte produit pertinente. L'essai de similarité visuelle du PoC ne valide pas à lui seul la personnalisation complète, la satisfaction de l'utilisateur ni une hausse réelle des ventes.

## 4. Objectifs business et hypothèses commerciales

- Améliorer la découverte et la pertinence des recommandations.
- Favoriser l'achat d'articles additionnels et la fidélisation.
- Permettre l'expression de préférences (styles, marques, tendances) et de retours sur la pertinence.
- Soutenir le CA web et magasin, **sans assimiler projection commerciale et résultat technique du PoC**.

D'après l'étude Marketing communiquée : **+14 % de CA web** et **+4 % de CA en magasin** à l'horizon de 24 mois. À bases annuelles inchangées, ces hypothèses représentent respectivement **728 000 €** et **208 000 €** de **CA annuel additionnel au rythme cible**, soit **936 000 €/an au total**. Ce n'est ni une marge ni un gain déjà réalisé ; la trajectoire temporelle et l'attribution au projet demeurent à prouver. Le ROI Data/IA correspondant figure dans le document 07.

## 5. Données existantes et disponibilité

Pour chaque référence, plusieurs images existent (angles différents, vêtement porté ou non), ainsi qu'une **description sommaire**, un **prix de vente** et des **tags de style**. Les données sont réparties entre plusieurs systèmes et doivent être croisées par les Data Engineers. L'extraction des données ayant servi au tableau métier a nécessité **3 jours-homme de Data Engineer**, repère historique et non délai garanti pour une nouvelle extraction.

À confirmer : produits encore commercialisés, correspondance image-référence, qualité et droits des visuels, attributs de coupe/couleur, stocks actualisés, disponibilité des préférences clients, présence d'un identifiant unique.

## 6. Besoins métiers pour la solution finale — traçabilité

| Besoin exprimé par Alicia | Brique / traitement attendu | Périmètre de cadrage |
|---|---|---|
| Compte nominatif | Authentification et identité, fournies par la partie Software | Produit final ; **non développé dans le PoC** |
| Prise de photo, garde-robe et collection des clichés | Gestion de la bibliothèque utilisateur, contrôle des droits, stockage sécurisé | Produit cible, raccordé au traitement IA |
| Recommandations depuis les photos gardées | Encodage d'images, recherche visuelle et personnalisation | **Noyau testé dans le PoC** ; garde-robe complète différée |
| Afficher le vêtement proposé sur la photo | Essayage virtuel / image de synthèse conditionnée par produit | Étude ultérieure, **hors budget central** |
| Modifier couleur et éventuellement style/coupe | Variantes produit, éventuellement génération contrôlée | Évolution ultérieure à spécifier/chiffrer |
| Styles et marques préférés | Profil de préférences et filtrage/reranking | Architecture de production, MVP à valider |
| Blogs, sites, influenceurs et tendances | Acquisition de sources autorisées, mise à jour et extraction de signaux de tendance | Évolution séparée : droits, qualité, maintenance |
| Avis sur les recommandations | Collecte de feedback, mesure qualité et amélioration du classement | Architecture cible ; pilote MVP |
| Désinscription et accès aux données | Interface Software + API Data pour les droits utilisateurs | Obligation transverse de la solution cible |
| Rectification et suppression des données | Traçabilité identifiants, propagation des demandes aux stockages/index | Obligation transverse |
| Politique de conservation et purge après inactivité | Durées et déclencheurs à définir, automatisation, journal des purges | Obligation transverse ; validation DPO |
| Redirection vers le site web pour achat | Intégration e-commerce de l'application (Software) | Hors chiffrage Data/IA |

**Important :** un besoin « différé » n'est **pas supprimé** du programme : il fait l'objet d'un lot de déploiement ou d'une étude distincte. Les choix logiciels d'interface restent hors chiffrage, mais leurs flux de données avec l'IA sont représentés dans le System Design.

## 7. PoC : noyau fonctionnel et exclusions

Le premier PoC propose de vérifier uniquement la **similarité visuelle** sur **120 références pantalons/jeans**, grâce à environ **40 photos de test** indépendantes et une restitution **Top 5 de références distinctes** jugées par deux évaluateurs métier. Le seuil de succès et la comparaison à une référence sans IA figurent dans les documents 03 et 04.

Hors PoC : comptes clients, garde-robe gérée de bout en bout, essayage virtuel, changement de couleur, tendances/blogs, produit mobile complet et exploitation à l'échelle de 400 000 actifs/an. Les exigences de confidentialité des photos de test restent applicables dès le PoC.

## 8. Contraintes, gouvernance et questions ouvertes

- **Azure** constitue l'environnement cloud partenaire : services, région, SKU et quotas doivent être validés.
- Les données sont hétérogènes ; la préparation et le contrôle de la qualité précèdent toute décision d'industrialisation.
- Les photographies peuvent contenir des données personnelles : minimisation, base légale, gestion des accès, rétention et suppression doivent être définies avec le DPO.
- Le terme **« même style »** exige une grille métier commune ; la ressemblance de couleur ne suffit pas.
- Les charges en jours-homme, les tarifs internes, les coûts Azure et le scénario de marge sont des **hypothèses de cadrage**, pas des devis approuvés.

Questions à soumettre à Alicia, aux métiers et aux fonctions Data : catalogue actif, droits photo, données de test, taxonomie de style, capacité d'évaluation métier, accessibilité des services Azure, objectifs de latence, fréquence de mise à jour, politiques de rétention, et part attribuable au projet des gains Marketing.

## 9. Conclusion de cadrage

Fashion-Insta dispose de données de catalogue potentiellement exploitables et d'un objectif commercial explicite. L'orientation proposée est de **démontrer une recommandation visuelle simple** sur la catégorie pantalons/jeans, puis de décider de l'industrialisation à partir de mesures de pertinence, d'une architecture hybride batch/online et d'un chiffrage Data/IA conditionnel.

**Sources :** document d'expression des besoins communiqué par Alicia ; `02_perimetre_poc.md`, `03_approches_techniques_poc.md`, `04_criteres_succes_ressources_planification.md`, `05_chiffrage_ressources_humaines_partie2.md`, `06_dimensionnement_couts_azure_partie2.md`, `07_analyse_roi_rentabilite_partie2.md`.
