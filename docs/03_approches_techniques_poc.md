# P11 — Approches techniques du PoC et justification batch / online

**Entreprise :** Fashion-Insta  
**Projet OpenClassrooms :** Réalisez le cadrage d'un projet IA — Partie 1, avec prolongement architectural pour la Partie 2  
**Statut :** Proposition technique à valider avec Alicia, le Tech Lead Data et le mentor  
**Catégorie pilote :** Pantalons et jeans — **120 références**, correspondant à **27 % du CA annuel déclaré**  
**Document associé :** `docs/06_dimensionnement_couts_azure_partie2.md` (scénarios de trafic, autoscaling, tests de charge et chiffrage Azure)

> **Décision proposée :** générer et actualiser **en batch** les embeddings des photographies du catalogue ; calculer **à la demande (online)** l'embedding de la photographie transmise par un utilisateur et produire les recommandations. Le PoC peut simuler cette deuxième étape hors ligne pour évaluer la qualité, sans déployer immédiatement une API 24 h/24. Le **dimensionnement de production** relève du document 06 : aucune performance ni économie n’est considérée comme démontrée avant mesure.

## 1. Objectif et périmètre du PoC

Le problème métier est de permettre à un client de photographier un pantalon ou un jean qu'il apprécie, puis de retrouver **cinq références différentes** du catalogue Fashion-Insta présentant une proximité visuelle ou stylistique pertinente. Le résultat doit être utile aux équipes métier, mesurable et reproductible.

**Question de faisabilité :** une solution d'IA peut-elle retrouver des pantalons et jeans correspondant au style d'une photographie prise dans des conditions proches de l'usage réel ?

Le PoC **ne consiste pas à produire l'application mobile** : la gestion des comptes, la garde-robe numérique, le paiement et l'essayage virtuel sont hors périmètre de cette première preuve de faisabilité. Aucun entraînement de modèle à partir de zéro n'est requis a priori.

Les paramètres du PoC ne doivent pas être confondus avec ceux du futur système en production : **120 références et environ 40 photos de test** pour le pilote, puis extension éventuelle à l'ensemble du catalogue et à une fréquentation marketing estimée à **400 000 utilisateurs par an**.

## 2. Données nécessaires et conditions de qualité

### 2.1. Données du catalogue

- Jusqu'à **120 références pantalons/jeans**, à confirmer comme disponibles et exploitables.
- Plusieurs images par référence lorsqu'elles existent : vêtements portés, non portés et vues sous différents angles.
- Identifiants produits, descriptions, prix, tags de style ; couleurs et coupes lorsque les attributs sont disponibles.
- Une relation **image → référence produit**, indispensable pour ne pas recommander cinq photos du même pantalon.
- Un contrôle des doublons, images manquantes, formats, orientation, éclairage et cohérence des tags.

### 2.2. Photographies de test

- Hypothèse initiale : **30 à 50 photographies**, avec **40** comme volume de référence pour la préparation du protocole.
- Photographies **indépendantes** des images du catalogue ; éviter les fichiers identiques ou quasi identiques.
- Variété d'éclairages, arrière-plans, coupes, couleurs, vêtements portés ou non.
- Pour un premier test, recadrage manuel possible et documenté ; la détection/segmentation automatique n'est pas un prérequis du PoC.
- Données acquises et traitées légalement ; privilégier les photographies sans visage ni informations personnelles inutiles.

### 2.3. Sortie attendue

Pour chaque photographie, retourner un **Top 5 ordonné de références uniques**. La définition de « même style » devra être coconstruite avec les métiers : coupe, matière, silhouette, formalité, motifs et couleur. La proximité de couleur **ne suffit pas**, à elle seule, à démontrer une recommandation stylistique pertinente.

### 2.4. Styles métier et pertinence : référentiel de départ

Fashion-Insta communique **cinq styles** à conserver comme vocabulaire contrôlé initial : **essentiel urbain**, **casual chic**, **professionnel moderne**, **streetwear** et **bohème féminin**. Ils ne constituent pas des étiquettes de vérité terrain automatiquement fiables : les deux évaluateurs métier devront définir comment ils se manifestent dans les pantalons/jeans, et tenir compte aussi de la coupe, de la silhouette, des motifs, de la matière apparente et de la couleur.

La qualité du PoC doit démontrer une valeur au-delà d'une comparaison mécanique des tags ; cette nomenclature ne modifie **ni les 120 références pilotes ni les seuils de Precision@5 et Success@5 proposés**. La gestion de marques, de tendances externes et d'essayage génératif reste un **besoin cible différé et non chiffré**. Voir `01_analyse_besoins_metier.md` pour la traçabilité métier.

## 3. Approche A — Embeddings visuels et similarité cosinus

### Principe

Un modèle préentraîné de représentation d'images, par exemple **CLIP/OpenCLIP** ou un équivalent approuvé dans l'environnement Azure, transforme chaque photographie en vecteur numérique (*embedding*). Les vecteurs du catalogue sont comparés à celui de la photo de test ; une **similarité cosinus** sert à classer les images, puis les résultats sont regroupés par référence produit.

### Chaîne de traitement

1. Contrôler et préparer les images sources du catalogue.
2. Générer leurs embeddings **en une ou plusieurs opérations batch**, en conservant l'identifiant et la version du produit.
3. Stocker les vecteurs et les métadonnées ; ne recalculer que les articles modifiés lorsque cela est fiable.
4. Pour une photo de test, générer un embedding avec **le même modèle, les mêmes poids et le même prétraitement**.
5. Calculer la similarité cosinus ; trier, dédupliquer les références et appliquer les filtres métier disponibles.
6. Retourner cinq références différentes et soumettre le classement aux évaluateurs métier.

### Services Azure envisageables

- **Azure Blob Storage** : images du catalogue et images de test autorisées.
- **Azure Machine Learning** : jobs d'encodage batch du catalogue ; environnement de test pour l'encodage des photos.
- **Stockage de vecteurs / calcul de similarité** : recherche exhaustive simple pour 120 références ; **Azure AI Search**, optionnel, si la dimension et le filtrage le justifient.
- **Azure Monitor / Key Vault** : journalisation technique et gestion des secrets dans la future architecture industrialisée.

### Avantages et limites

**Avantages :** pas de jeu d'entraînement massif initial, recherche image → image directe, embeddings du catalogue réutilisables, évaluation reproductible.

**Limites :** influence possible du décor ou de la posture, décalage entre photos clients et photos professionnelles, explicabilité limitée. Un score cosinus **n'est ni une probabilité d'achat ni une validation métier**.

## 4. Approche B — Vision-langage et comparaison d'attributs

### Principe

Un modèle multimodal capable d'analyser les photographies (selon les offres accessibles via **Azure OpenAI / Microsoft Foundry**) extrait une description structurée : type, coupe, couleur, matière apparente, silhouette et style. Ces attributs sont comparés aux tags et descriptions du catalogue, avec une règle de classement explicite ou une recherche hybride.

### Chaîne de traitement

1. Définir un **vocabulaire contrôlé** avec Alicia et les métiers (par exemple slim/droit/large, casual chic/streetwear/professionnel).
2. Extraire ou contrôler les attributs des références du catalogue, idéalement **en batch**.
3. Extraire les attributs de la photographie test à la demande lors du service interactif, ou hors ligne pour le PoC.
4. Construire un score de rapprochement pondéré selon des règles métier documentées.
5. Dédupliquer par produit, générer le Top 5 et mesurer sa pertinence face à l'approche A.

### Avantages et limites

**Avantages :** attributs intelligibles pour les métiers, prise en compte de règles métier, enrichissement possible des métadonnées.

**Limites :** descriptions générées parfois fausses ou instables ; vocabulaire stylistique subjectif ; dépendance éventuelle à la qualité des tags ; coût et latence d'appels multimodaux à mesurer. La production d'une description n'implique **pas** automatiquement une bonne recherche d'images.

**Services possibles :** offre vision-langage approuvée dans Azure, stockage des métadonnées, calcul de classement, Azure AI Search facultatif selon le besoin et la volumétrie. Disponibilité, région, quotas et conditions d'utilisation restent à confirmer.

## 5. Comparaison des approches IA

| Critère | A — Embeddings visuels | B — Vision-langage + attributs |
|---|---|---|
| Entrée | Photographie | Photographie |
| Représentation | Vecteur numérique | Description et attributs structurés |
| Recherche | Similarité cosinus + regroupement par produit | Règles de score / recherche hybride |
| Entraînement spécifique initial | Non requis | Non requis |
| Explicabilité métier | Plutôt limitée | Plus importante |
| Besoin de tags fiables | Faible à modéré | Plus important |
| Risque principal | Proximité visuelle ≠ style | Attributs inexacts ou subjectifs |
| Préparation catalogue | Batch | Batch |
| Photo de l'utilisateur en production | Online | Online |
| Choix pour le PoC | **Approche de référence proposée** | **Alternative à tester** |

**Décision provisoire :** commencer par **A**, tester **B** si le budget et les accès le permettent, puis retenir une solution sur les **résultats d'évaluation**, et non sur la seule sophistication du modèle.

## 6. Choix d'architecture — démontrer pourquoi batch et online sont complémentaires

### 6.1. Le besoin dicte le mode d'exécution

| Traitement | Contrainte métier | Mode privilégié | Justification |
|---|---|---|---|
| Importation et nettoyage des images catalogue | Différable | **Batch** | Regrouper les traitements et fiabiliser les données |
| Génération des embeddings catalogue | Différable ; changements ponctuels | **Batch** | Calculer une fois et actualiser uniquement les références concernées |
| Reconstruction ou mise à jour de l'index | Différable | **Batch / pipeline incrémental** | Éviter les recalculs à chaque visite |
| Photo envoyée par le client | Réponse attendue dans l'application | **Online** | Encoder la nouvelle photo pendant la requête |
| Recherche Top 5 et filtres produits | Résultat interactif | **Online** | Répondre avec une faible latence |
| Statistiques, rapports de pertinence, audits | Différable | **Batch** | Consolider hors du chemin de réponse client |
| Essayage virtuel génératif | Potentiellement lourd et coûteux | **À arbitrer** | Synchrone si expérience interactive exigée ; asynchrone si délai acceptable |

La distinction ne signifie pas que **« le batch est toujours meilleur »** : le batch est mieux adapté aux données déjà connues et aux travaux différables ; l'online est mieux adapté à une photographie nouvelle pour laquelle l'utilisateur attend une réponse immédiate.

### 6.2. Schéma logique de bout en bout

```text
                  PRÉPARATION DU CATALOGUE — BATCH
Sources produits / images / tags
             ↓
Ingestion + contrôles de qualité (Data Engineer)
             ↓
Blob Storage → Job Azure ML batch → embeddings catalogue
                                      ↓
                       Vecteurs + index + métadonnées produits
                                      │
                                      │ réutilisés par toutes les requêtes
                                      ↓
                   RECOMMANDATION UTILISATEUR — ONLINE
Photo utilisateur → service IA → embedding de la photo
                                      ↓
                         Recherche de similarité + filtres
                                      ↓
                           Top 5 de références différentes
                                      ↓
                             Application mobile existante

TRANSVERSE : sécurité/RGPD, mises à jour, monitoring, contrôle des coûts.
```

Ce schéma représente les composants Data/IA. L'interface mobile, les comptes et les achats existent hors de notre chiffrage. Pour un PoC non déployé, la branche « online » peut être **simulée** en traitant successivement les 40 photos de test et en mesurant les temps de traitement.

### 6.3. Pourquoi ne pas recalculer le catalogue à chaque requête ?

- Hypothèse de production : **875 références × 3 images = 2 625 images catalogue**.
- Hypothèse de trafic central : **2 000 000 recommandations/an**.
- Une implémentation naïve recalculant tous les embeddings du catalogue à chaque requête réaliserait **2 625 × 2 000 000 = 5,25 milliards d'encodages d'images catalogue/an**, au lieu de réutiliser les embeddings déjà calculés.
- Ce calcul représente un **contre-exemple théorique**, pas un scénario de coût réel ni la façon normale d'exploiter un endpoint online. Il justifie **le pré-calcul et la mise en cache** des représentations du catalogue.

### 6.4. Conséquences techniques et économiques

- **Azure ML batch :** traitement asynchrone, calcul activé lors des jobs, possibilité de réduire le cluster à zéro en inactivité. Le coût de calcul dépend principalement des **heures de machine et de leur capacité**, outre les services annexes.
- **Azure ML managed online :** inférence interactive, disponibilité et temps de réponse à garantir ; les **instances provisionnées sont facturées** même si le trafic est faible. L'autoscaling doit être dimensionné et ne supprime pas nécessairement la capacité minimale.
- **Données partagées :** Blob Storage, base produit et éventuel service de recherche sont des postes indépendants ; **un cluster batch éteint ne signifie pas que l'ensemble de la solution coûte zéro**.
- **Alternative à évaluer :** service de calcul à la demande pour l'inférence interactive lorsque le modèle et la latence le permettent. Ne pas additionner deux architectures qui se substituent l'une à l'autre dans un même budget.

Références Microsoft : [principes et différences online/batch](https://learn.microsoft.com/fr-fr/azure/machine-learning/concept-endpoints?view=azureml-api-2), [fonctionnement et facturation batch](https://learn.microsoft.com/en-us/azure/machine-learning/concept-endpoints-batch?view=azureml-api-2), [coûts des managed online endpoints](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-view-online-endpoints-costs?view=azureml-api-2).

### 6.5. Hypothèses de trafic et dimensionnement de l’inférence online

Le Marketing prévoit **400 000 personnes utilisant l’application au moins une fois par an** à terme. Il ne s’agit **ni de 400 000 utilisateurs simultanés ni de 400 000 requêtes quotidiennes**. Pour relier l’architecture à un dimensionnement, le document 06 propose, à titre d’hypothèse centrale, **5 demandes de recommandation par utilisateur et par an**, soit **2 000 000 requêtes/an**.

| Profil temporel *supposé* | Débit moyen durant la plage considérée | Pointe illustrative ×20 |
|---|---:|---:|
| Activité lissée 24 h/24 | ≈ 0,063 req/s | ≈ 1,27 req/s |
| Activité concentrée sur 8 h/jour | ≈ 0,190 req/s | ≈ 3,81 req/s |

Ces **deux profils sont alternatifs**, non cumulatifs ; le coefficient ×20 n’est pas issu d’une mesure. Une campagne marketing peut engendrer un pic plus important. Il faudra obtenir des historiques horaires ou des scénarios métier réalistes avant de figer la capacité.

**Méthode de sizing, après benchmark :** mesurer le débit soutenable d’une instance (CPU ou GPU) *tout en respectant un objectif de latence p95* et les contraintes mémoire/erreurs, puis calculer une première borne :

`nombre_instances ≈ plafond(débit_pointe / (débit_mesuré_par_instance × taux_utilisation_cible))`

Exemple fictif : **2 req/s par instance**, **3,81 req/s** de pointe simulée et **70 %** d’utilisation cible donnent `plafond(3,81 / 1,4) = 3 instances`. Cette illustration **ne signifie pas que trois instances sont nécessaires** : il faut tester le modèle choisi, les quotas Azure et la concurrence réelle, puis considérer la redondance et le temps de démarrage.

**Services Azure associés :** l’inférence interactive pourrait être hébergée dans un **managed online endpoint Azure ML**, avec autoscaling piloté par Azure Monitor et limites minimum/maximum explicites. Une variante à la demande (par exemple Azure Container Apps si le modèle s’y prête) peut être étudiée **à la place** de cet endpoint, non ajoutée automatiquement à son coût.

### 6.6. Fiabilité, dégradation et limites de l’autoscaling

- **Objectifs indicatifs, à faire valider :** réponse Top 5 `p95 ≤ 3 s` et disponibilité mensuelle `≥ 99,5 %`. Ce sont des **cibles proposées pour les essais**, pas des engagements de Fashion-Insta ni des SLA Azure.
- **Pics de trafic :** contrôler le débit à l’entrée du service IA, surveiller les `429` et `5xx`, définir la temporisation des nouvelles tentatives et tester les délais de montée en charge. L’autoscaling ne peut pas absorber instantanément tous les pics.
- **Catalogue :** publier le nouvel index uniquement après validation, maintenir une dernière version saine et empêcher que les filtres de stock recommandent un article retiré. L’actualisation batch peut être retardée, mais ne doit pas corrompre le service online.
- **Dégradation maîtrisée :** si l’inférence n’est plus disponible, fournir une erreur explicite avec reprise contrôlée ; une recherche de secours par tags n’est envisageable qu’avec validation métier. Ne pas fabriquer de recommandations présentées comme issues du modèle.
- **Traitements lourds :** une file asynchrone (par exemple Azure Service Bus) peut être étudiée **pour l’essayage virtuel génératif** ou des opérations différables, mais **pas imposée** au Top 5 interactif si elle compromet la latence.
- **Vie privée :** gérer la conservation volontaire des photos de garde-robe séparément des clichés de requête ; tester la suppression des objets, métadonnées, index et caches concernés, sans images personnelles dans les logs.

Ces mécanismes doivent figurer dans le **System Design de production** et être traités dans les essais de robustesse décrits dans le document 06. Le chiffrage final dépend notamment du nombre minimal d’instances online et du coût des services de haute disponibilité ou de supervision effectivement retenus.

## 7. Démonstration attendue : qualité, latence et coûts

Aucune économie n'est **prouvée** tant que les charges réelles et le SKU Azure ne sont pas mesurés. Le protocole proposé comporte trois essais complémentaires :

1. **Batch catalogue :** relever le nombre d'images traitées, les heures machine, l'usage CPU/GPU, la fréquence des mises à jour et le coût imputable au job.
2. **Online utilisateur :** mesurer le temps de réponse p50/p95, le débit soutenable, le taux d'erreurs, les heures d'instances provisionnées et le coût mensuel attendu.
3. **Pertinence :** mesurer Precision@5 et Success@5 sur les mêmes photos ; comparer au classement par tags sans modèle IA et, si réalisable, à l'approche B.

La comparaison batch/online n'est **pas** le remplacement de l'inférence interactive par un job nocturne. Elle consiste à démontrer que **le calcul différable ne doit pas monopoliser une capacité online permanente**.

| Indicateur | Mesure à relever | Règle de décision proposée |
|---|---|---|
| Durée batch d'actualisation | Minutes ou heures par job | Compatible avec la fréquence des changements catalogue |
| Temps de réponse online | p50/p95, secondes | Compatible avec le parcours utilisateur ; cible à fixer avec Alicia |
| Coût d'actualisation | Heures machine × prix SKU | Choisir le mode le plus économique à qualité équivalente |
| Disponibilité des vecteurs | Fraîcheur de l'index | Ne pas recommander de référence retirée du catalogue |
| Qualité des recommandations | P@5, Success@5 | Respecter les seuils métier préalablement convenus |

### 7.1. Vérification de la scalabilité au stade MVP / Run (distincte du PoC)

Le PoC valide avant tout la **pertinence des recommandations** sur 40 photos et 120 références. Il **ne prouve pas** à lui seul la capacité de production. Les essais de charge devront donc être réalisés pendant le MVP, puis contrôlés en Run initial :

| Essai | Situation | Mesures et condition de décision |
|---|---|---|
| Référence | Trafic faible et stable | Latence p50/p95, débit, mémoire, erreurs et coût par requête |
| Paliers de charge | Environ 1×, 5× et 20× le débit de référence choisi | Nombre d’instances, taux de `429`/`5xx`, p95 et stabilité |
| Pic soudain | Campagne Marketing simulée | Délai d’autoscaling, rejet contrôlé, reprise |
| Mise à jour batch | Ajout/retrait de produits pendant les requêtes | Fraîcheur et cohérence de l’index, absence de références retirées |
| Incident | Job batch raté, endpoint indisponible ou index corrompu | Alertes, restauration du dernier index sain, dégradation maîtrisée |
| RGPD | Suppression des images conservées avec autorisation | Contrôle d’effacement et absence de données brutes dans les logs |

Le **Go / No-Go de passage au Run** tiendra compte des métriques métier, des objectifs de latence/disponibilité validés, de la robustesse, de la gestion RGPD et d’une estimation Azure fondée sur des SKU vérifiables. **Aucun de ces résultats n’est présenté comme déjà observé.**

## 8. Protocole de qualité du PoC

### Référence métier (*ground truth*)

- Définir une grille de style commune.
- Faire juger les recommandations par **deux évaluateurs métier** : non pertinent, partiellement pertinent, pertinent.
- Prévoir une règle de consensus en cas de désaccord.
- Écarter les images de test identiques ou quasi identiques aux images commerciales.

### Indicateurs et seuils proposés

1. **Precision@5 (P@5)** : proportion moyenne d'articles pertinents parmi les cinq propositions, avec un regroupement par référence.
2. **Success@5** : part des photographies pour lesquelles au moins un article pertinent apparaît dans les cinq recommandations.
3. **Analyse qualitative** : échecs dus à la coupe, à la couleur, au décor, au port du vêtement ou au flou.
4. **Mesures techniques du PoC** : latence observée dans l’environnement d’essai, coût estimé par requête et coût de préparation du catalogue. Les SLO et tests de charge de production sont évalués ultérieurement, au MVP.

**Hypothèses de seuils à valider avant les essais :** **P@5 ≥ 60 %** et **Success@5 ≥ 80 %**, accompagnés d'une appréciation métier positive. Leur pertinence dépend de la diversité du catalogue : on ne peut pas exiger trois références pertinentes si la photo n'en admet réellement qu'une.

**Baseline :** classement déterministe à partir des tags/coupes/couleurs existants, sans modèle visuel. Cette comparaison sert à démontrer la **valeur ajoutée de l'IA** au-delà d'une recherche traditionnelle.

## 9. Sécurité, RGPD et maintenance

- Minimiser la collecte d'informations personnelles dans les photos ; ne pas placer d'images brutes de clients dans les logs.
- Formaliser la finalité, la base légale, les autorisations d'accès, les durées de conservation et les modalités de suppression.
- Favoriser le traitement éphémère des photographies de requête lorsque la constitution d'une garde-robe n'est pas demandée.
- Gérer séparément la conservation volontaire des photos de garde-robe et la purge automatique prévue par le besoin métier.
- Versionner les modèles, prétraitements et vecteurs pour éviter de comparer des embeddings incompatibles.
- Automatiser l'actualisation incrémentale du catalogue et surveiller les changements de distribution des photographies.

## 10. Répartition des rôles Data associée à la décision

| Profil | Responsabilités batch | Responsabilités online / transverses |
|---|---|---|
| Data Engineer (2) | Ingestion, qualité, métadonnées, jobs d'actualisation, synchronisation catalogue/index | Accès aux données, suppression, fiabilité des données métier |
| Data Scientist (3) | Choix du modèle, embeddings, comparaison A/B, tests pertinence | Contrôle de qualité, seuils, analyse des erreurs et dérive |
| Tech Lead Data (1) | Architecture, interfaces, choix de stockage/index, revue de sécurité | Dimensionnement, arbitrages coût-latence et qualité de service |
| MLOps Engineer (1) | Orchestration Azure ML batch, versioning, exécutions et alertes | Déploiement du modèle, monitoring, autoscaling, incidents et coût par requête |

Ces effectifs décrivent **l'équipe disponible**, non l'affectation à temps plein de chaque personne.

## 11. Décisions à confirmer avec Alicia et le Tech Lead

1. Définition métier de « même style » et grille d'évaluation.
2. Accès effectif aux images et tags des 120 références ; représentativité des photos de test.
3. Qualité minimale et niveau d'automatisation du recadrage de vêtements.
4. Fréquence de modification du catalogue : quotidienne, hebdomadaire, par événement ; délai maximal accepté pour actualiser les vecteurs.
5. Latence interactive visée et niveau de disponibilité attendu.
6. Volume réel et distribution temporelle des requêtes, y compris pics marketing.
7. Services, quotas, région, types d'instances et budget Azure autorisés.
8. Conditions RGPD des photographies clients et fonctionnalité de garde-robe.
9. Déclencheurs d'adoption d'Azure AI Search plutôt que d'une recherche exhaustive simple.
10. Conditions d'évaluation de l'essayage virtuel génératif, hors PoC de recommandation.

## 12. Conclusion et sources techniques

**Conclusion à défendre en soutenance :** pour Fashion-Insta, nous privilégions une **architecture hybride batch + online**. Les photographies et embeddings du catalogue, connus à l'avance, sont préparés et actualisés par jobs différables. La photographie envoyée par l'utilisateur est traitée à la demande pour renvoyer rapidement des recommandations. La décision vise à **éliminer les calculs redondants sans dégrader l'expérience client** et reste conditionnée à des tests de qualité, de latence, de montée en charge et de coûts. Les scénarios chiffrés et les règles de dimensionnement correspondants sont détaillés dans le document 06, sans présumer de performances mesurées.

### Sources officielles et documentation complémentaire

- [Microsoft Learn — Endpoints online et batch Azure ML](https://learn.microsoft.com/fr-fr/azure/machine-learning/concept-endpoints?view=azureml-api-2).
- [Microsoft Learn — Batch endpoints et coût de calcul](https://learn.microsoft.com/en-us/azure/machine-learning/concept-endpoints-batch?view=azureml-api-2).
- [Microsoft Learn — Suivi des coûts managed online endpoints](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-view-online-endpoints-costs?view=azureml-api-2).
- [Microsoft Learn — Autoscaling des endpoints Azure ML](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-autoscale-endpoints?view=azureml-api-2).
- [Microsoft Learn — Diagnostic des endpoints online et erreurs 429](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-troubleshoot-online-endpoints).
- [Microsoft Learn — Azure AI Search, recherche vectorielle](https://learn.microsoft.com/en-us/azure/search/vector-search-overview).
- [Azure Architecture Center — Choix de moteurs de recherche vectorielle](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/vector-search).
- [Microsoft Learn — Rechercher dans des contenus multimodaux](https://learn.microsoft.com/en-us/azure/search/multimodal-search-overview).
- [Microsoft Learn — Modèles capables d'analyser les images](https://learn.microsoft.com/azure/ai-services/openai/how-to/gpt-with-vision).

---

**Statut de la décision :** recommandé, **non encore confirmé expérimentalement**. La démonstration économique chiffrée et les hypothèses de charge figurent dans `06_dimensionnement_couts_azure_partie2.md`.
