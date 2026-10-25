# P11 — Partie 2 : dimensionnement et chiffrage de l'infrastructure Azure

**Version révisée — cohérente avec `03_approches_techniques_poc.md` (architecture hybride batch + online).**

**Entreprise :** Fashion-Insta  
**Mission :** Réalisez le cadrage d'un projet IA — Partie 2  
**Statut :** Document de cadrage économique, hypothèses à valider par Alicia, le Tech Lead Data et le COMEX  
**Périmètre chiffré :** **Data & IA uniquement** — hors réalisation de l'application mobile, comptes clients, achats en ligne et dépenses marketing  
**Document associé :** `docs/03_approches_techniques_poc.md`

> **Avertissement budgétaire :** tous les montants Azure en euros figurant dans ce document sont des **enveloppes de simulation non contractuelles**, et non un devis ni des prix Microsoft vérifiés pour une région et un SKU. Les prix réels devront être recalculés dans l'Azure Pricing Calculator. Les coûts humains proviennent d'un **modèle de charge estimatif** construit à partir des tarifs officiels de Fashion-Insta.

## 1. Objectifs et périmètres du dimensionnement

Ce document doit permettre au COMEX de comprendre :

1. Les composants Azure correspondant aux besoins métier et leurs principaux facteurs de coût.
2. **Pourquoi préparer le catalogue en batch** et **pourquoi servir les recommandations en online**.
3. Le lien entre les **400 000 utilisateurs annuels prévus** par le marketing, la capacité requise et les coûts de service.
4. Les dépenses **ponctuelles (construction/lancement)** et **récurrentes (Run)**.
5. Les données chiffrées qui alimenteront ultérieurement le **modèle de rentabilité/ROI**.

### 1.1. Phases de livraison conservées

| Phase | Durée de planification | Périmètre Data/IA |
|---|---:|---|
| **PoC** | 3 semaines | 120 pantalons/jeans, 40 photos de test, similarité et évaluation métier |
| **MVP** | 10 semaines | Données catalogue, moteur de recommandation, préférences/feedback, API IA, sécurité et RGPD |
| **Run initial** | 4 semaines | Pilote de production, monitoring, performance, stabilisation |
| **Run récurrent** | Après les 17 semaines | Révisions catalogue/index, inférence client, suivi des modèles, sécurité, incidents et coûts |

Le périmètre complet de l'entreprise comprend davantage que le PoC. **L'essayage virtuel génératif, la création d'images avec Stable Diffusion ou équivalent et les modifications avancées de vêtements** restent des **extensions à arbitrer** : leurs coûts ne sont pas inclus dans le scénario financier central faute de volumes et de choix techniques validés. Ils figurent dans le System Design comme évolutions possibles, pas comme services gratuits ou déjà livrés.

### 1.2. Sources internes de référence

- `docs/01_analyse_besoins_metier.md` : besoins métier et objectifs marketing.
- `docs/02_perimetre_poc.md` : périmètre du pilote.
- `docs/03_approches_techniques_poc.md` : algorithmes et justification batch/online.
- `docs/04_criteres_succes_ressources_planification.md` : métriques et planning initial.
- `docs/05_chiffrage_ressources_humaines_partie2.md` : hypothèses de charge et coûts humains.
- `sources/P11_Profils_Data.pdf` : coûts journaliers fournis par Fashion-Insta.

## 2. Trafic prévisionnel : distinguer utilisateurs et requêtes

L'étude marketing prévoit **400 000 personnes utilisant l'application au moins une fois par an** à terme. Ce chiffre n'est **ni un volume d'utilisateurs mensuels actifs, ni un nombre de requêtes par jour**.

Pour dimensionner un premier scénario, on suppose **une photo transmise et une recherche de recommandations par requête**.

| Scénario | Demandes par utilisateur/an | Demandes/an | Moyenne par mois |
|---|---:|---:|---:|
| Limité | 2 | 800 000 | ≈ 66 667 |
| **Central (hypothèse)** | **5** | **2 000 000** | **≈ 166 667** |
| Élevé | 10 | 4 000 000 | ≈ 333 333 |

Dans le scénario central :

- **2 000 000 / 365 ≈ 5 479 demandes/jour**, en moyenne.
- **2 000 000 / (365 × 24 × 3 600) ≈ 0,0634 requête/seconde**, sur une année complète.
- Un **coefficient hypothétique de pointe ×20** donne **≈ 1,27 requête/seconde**. Ce n'est qu'un test de sensibilité, pas une mesure de pic réel.

**Attention :** même des moyennes modestes peuvent cacher de fortes pointes pendant les campagnes marketing. Le dimensionnement final nécessite une distribution horaire, un objectif de latence p95, une disponibilité cible et des tests de charge. La capacité ne peut pas être déduite de la seule moyenne annuelle.

## 3. Catalogue, photographies et stockage

### 3.1. Catalogue produit

- **875 références uniques vendues** en 2024 ; le nombre de références réellement commercialisées simultanément doit être confirmé.
- **Hypothèse** : trois images par référence → **2 625 photographies**.
- Taille moyenne hypothétique : **1 Mo/image** → **≈ 2,6 Go** d'images brutes, hors sauvegardes, dérivés et métadonnées.
- Les embeddings associés doivent porter la **version du modèle**, l'identifiant de l'image, de la référence et la date d'actualisation.

### 3.2. Images transmises par les utilisateurs

- Taille moyenne de référence : **1,5 Mo/photo**, hypothèse à mesurer.
- Scénario central : **166 667 × 1,5 Mo ≈ 250 Go de données images transmises par mois**.
- **Si les images sont temporairement conservées 24 h** : volume moyen simultané ≈ **8,2 Go**, hors pics, fichiers dérivés, réplicas et sauvegardes.
- **Si la conservation dure 30 jours** : ≈ **250 Go** en régime stabilisé, hors éléments annexes.
- **Garde-robe conservée volontairement — scénario distinct :** 20 % de 400 000 personnes × 5 photos × 1,5 Mo ≈ **600 Go**, si cette hypothèse d'usage est retenue.

Ces volumes **ne signifient pas que tous les octets entrants sont nécessairement stockés durablement**. La politique de rétention, la minimisation des données, la purge et le mode de traitement influencent la facture et la conformité RGPD.

## 4. Architecture Azure de référence et postes facturables

### 4.1. Séparation logique des flux

```text
                             FLUX CATALOGUE — BATCH
Catalogue / photos / tags
      ↓
Azure Data Factory : ingestion, transformations, contrôle qualité
      ↓
Azure Blob Storage + base de métadonnées produits
      ↓
Azure Machine Learning : job batch (embeddings uniquement si nouveaux/modifiés)
      ↓
Vecteurs du catalogue + métadonnées + index / recherche
      ║
      ║  ressources pré-calculées et réutilisées
      ▼
                           FLUX CLIENT — ONLINE
Photo utilisateur → API IA existante / service de requête
      ↓
Azure ML managed online endpoint (option de référence pour inférence)
      ↓
Embedding de la photo + recherche parmi les vecteurs du catalogue
      ↓
Filtres métier et Top 5 de références distinctes
      ↓
Retour à l'application mobile (développement hors budget Data/IA)

TRANSVERSE : RGPD, gestion des secrets, surveillance, coûts, versions, alertes.
```

L'API métier, l'application et les comptes figurent comme interfaces et dépendances, **sans chiffrage de développement Software**.

### 4.2. Correspondance avec les services Azure

| Brique | Service envisagé | Principaux facteurs de coût | Vigilance |
|---|---|---|---|
| Images catalogue et photos autorisées | **Azure Blob Storage** | Go-mois, transactions, réplication, sorties | Durée de conservation et purge |
| Données produits, préférences et retours | **Azure SQL Database** | Service tier, capacité, stockage, sauvegardes | Éviter de dupliquer des données existantes |
| Ingestion / synchronisation | **Azure Data Factory** | Exécutions, activités, opérations de copie | Traitements incrémentaux et cadence nécessaire |
| Préparation des embeddings catalogue | **Azure Machine Learning — jobs / batch endpoint** | **Heures machine pendant les jobs**, taille CPU/GPU | Cluster à capacité nulle au repos lorsque supporté |
| Embedding de la nouvelle photo client | **Azure ML — managed online endpoint** | **Instances provisionnées**, autoscaling, CPU/GPU | Latence, disponibilité et capacité minimale |
| Recherche vectorielle et filtrage | **Azure AI Search** (à justifier) | Service tier, unités, réplication | Pour un petit catalogue, comparer à une recherche exhaustive |
| Logs, observabilité, alertes, secrets | **Azure Monitor / Application Insights / Key Vault** | Ingestion, rétention, appels, fonctionnalités annexes | Exclure les photos brutes des journaux |
| Réseau et données sortantes | **Services réseau Azure** | Transferts, trafic inter-régions, réseau privé éventuel | Respect de la région et des coûts réseau |

**Choix d'architecture, et non cumul automatique des services :** Azure AI Search peut être inutile si une recherche exhaustive très simple satisfait le besoin et la latence. Un service à la demande de type Azure Container Apps peut être testé pour certaines inférences en alternative au managed online endpoint, sous réserve de compatibilité avec le modèle et des exigences de latence. **On ne facture pas les deux variantes simultanément pour la même charge.**

## 5. Justification détaillée — pourquoi batch pour le catalogue et online pour l'utilisateur ?

### 5.1. Les traitements n'ont pas la même exigence de latence

| Question à résoudre | Catalogue déjà connu | Nouvelle photographie utilisateur |
|---|---|---|
| Peut-on différer le travail ? | **Oui** | **Non**, si on promet une recommandation immédiate |
| Fréquence | Selon ajouts/modifications d'articles | À chaque demande cliente |
| Mode recommandé | **Batch** | **Online** |
| Résultat | Embeddings pré-calculés, index actualisé | Embedding requête, Top 5 renvoyé |
| Coût déterminant | Fréquence × durée des jobs × SKU | Capacité provisionnée × durée + services liés |
| Risque | Index obsolète ou retard de mise à jour | Latence, saturation, disponibilité, coût fixe |

Microsoft décrit ses batch endpoints comme destinés aux **traitements longs, asynchrones et sans contrainte de faible latence**. Les ressources de calcul peuvent être allouées au lancement d'un job et libérées à sa fin. Un managed online endpoint répond aux appels interactifs et s'appuie sur des instances de service déployées. Cela valide la **séparation fonctionnelle**, mais ne fournit pas à lui seul un montant d'économies pour Fashion-Insta.

Références : [concept batch endpoints](https://learn.microsoft.com/en-us/azure/machine-learning/concept-endpoints-batch?view=azureml-api-2), [comparaison des types de endpoints](https://learn.microsoft.com/fr-fr/azure/machine-learning/concept-endpoints?view=azureml-api-2), [coûts managed online endpoints](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-view-online-endpoints-costs?view=azureml-api-2).

### 5.2. Démonstration de consommation : même calcul différable, deux façons de réserver du compute

**Exemple purement illustratif**, distinct des devis Azure et des budgets simulés plus bas :

- Un recalcul ou ensemble d'actualisations du catalogue dure **4 heures** par exécution.
- Le traitement est réalisé **4 fois par mois**, soit **16 heures de calcul par mois**.
- Une ressource **de capacité équivalente** laissée active sans interruption représente environ **730 heures par mois**.
- `r` désigne le **tarif horaire réel** de cette capacité, à renseigner depuis le calculateur Azure.

| Pour cette seule tâche différable | Heures de calcul réservées / mois | Expression de coût de calcul |
|---|---:|---:|
| Jobs batch, calcul arrêté hors traitement | **16 h** | **16 × r** |
| Instance dédiée laissée active 24 h/24 à cette tâche | **730 h** | **730 × r** |
| Écart théorique de réservation | **714 h** | **714 × r** |

Soit **16 / 730 ≈ 2,2 %** du temps de calcul d'une instance dédiée laissée active en permanence, ou **97,8 % d'heures provisionnées en moins** *dans cet exemple théorique et pour cette tâche uniquement*.

**Traduction monétaire explicitement fictive :** si, uniquement pour illustrer la formule, nous fixons le coût de cette capacité à **2 €/heure** (valeur **inventée pour le calcul**, et **pas** un tarif Azure), le coût de calcul serait :

| Même tâche catalogue ; tarif horaire illustratif identique | Hypothèse de coût mensuel |
|---|---:|
| Batch, 16 heures × 2 €/h | **32 €** |
| Instance dédiée permanente, 730 heures × 2 €/h | **1 460 €** |
| Différence sur le seul calcul dédié à l'actualisation | **1 428 €** |

Cette différence **ne se soustrait pas** au budget mensuel de 1 800 € indiqué plus loin : celui-ci est un **autre scénario de provision**, non un devis et non la mesure de la même machine. Il faut remplacer 2 €/h, les 16 h et les 730 h par les spécifications réellement comparables avant de publier une économie projet.

**Test de sensibilité avec 4 exécutions par mois, un seul nœud et le même coût fictif de 2 €/h :**

| Durée moyenne d'un job | Heures batch/mois | Coût batch illustratif | Capacité permanente illustrative |
|---|---:|---:|---:|
| 1 heure | 4 h | 8 € | 1 460 € |
| 4 heures | 16 h | 32 € | 1 460 € |
| 8 heures | 32 h | 64 € | 1 460 € |

Ces chiffres servent à expliquer un **modèle de facturation**, sans valider une durée de traitement, un SKU, une configuration réseau, ni une comparaison à performance égale.

**Ce qu'on peut conclure :** lorsque l'actualisation du catalogue est réellement différable et relativement peu fréquente, une capacité activée pendant les jobs puis libérée peut réduire les heures payées **pour ce travail précis**.

**Ce qu'on ne peut PAS conclure :** le coût Azure complet sera réduit de 97,8 %, ni que l'on peut supprimer l'inférence online nécessaire aux utilisateurs. Le calcul exclut stockage, transactions, services permanents, démarrage de machine, parallélisation éventuelle, remises et spécificités SKU. Les 16 heures sont **une hypothèse**, à remplacer par des mesures ou une estimation documentée.

**Comparateur à ne pas oublier :** si un endpoint online *déjà financé* dispose de capacités disponibles et qu'une mise à jour catalogue peut y être exécutée sans gêner les utilisateurs, le coût marginal de réutilisation peut être différent de celui d'une instance dédiée 24 h/24. Il faut donc comparer les **coûts supplémentaires réellement induits** par trois scénarios : (a) batch à la demande, (b) capacité online existante mutualisée, (c) capacité dédiée permanente — et non décréter que le batch gagne dans tous les cas. Le batch demeure la **préférence opérationnelle** pour un travail différable, sous réserve de tests.

### 5.3. Pourquoi conserver des embeddings catalogue persistants ?

Avec les hypothèses de **2 625 images catalogue** et **2 000 000 demandes/an** :

- Recalculer toutes les images du catalogue à chaque requête conduirait, dans une architecture volontairement inefficace, à **2 625 × 2 000 000 = 5,25 milliards d'encodages catalogue/an**.
- Préparer les vecteurs **en amont** puis n'encoder que la nouvelle photographie cliente à chaque demande évite cette redondance.
- Mettre à jour seulement les articles nouveaux ou modifiés limite encore la durée des jobs.
- **Exemple illustratif de variation catalogue :** si 5 % des 2 625 images changent dans un mois, environ **131 images** sont à réencoder ce mois-là plutôt que 2 625 ; ce taux de 5 % est hypothétique et à vérifier. Si le modèle d'embeddings ou le prétraitement change, une **réindexation globale** peut être nécessaire.
- Les embeddings catalogue et requête doivent toujours être issus du **même espace vectoriel** (modèle, version et prétraitement compatibles), conformément au document 03.

Cet ordre de grandeur illustre un **anti-pattern** (ne pas recourir au pré-calcul). Il ne décrit pas une architecture online convenablement conçue et **n'est pas un coût Azure mesuré**.

### 5.4. Formules de coût à vérifier

| Poste | Formule de travail | Entrées manquantes |
|---|---|---|
| Calcul batch catalogue | `Σ(nb_instances × heures_job × tarif_horaire_instance)` | Fréquence, runtime, instance, parallélisme, prix SKU |
| Calcul online inférence | `Σ(nb_instances_provisionnées × heures_actives × tarif_horaire_instance)` | CPU/GPU, seuil min, autoscaling, disponibilité |
| Coût de service par recommandation | `coûts online attribuables / nb_recommandations_effectives` | Trafic réel et coûts observés |
| Stockage photo | `Go-mois × prix_stockage + opérations + réplication` | Politique de rétention, niveaux de stockage |
| Coût total Data/IA | `batch + online + recherche + stockage + pipelines + logs + réseau + autres` | SKU et consommation de chaque brique |

**Règle de décision :** choisir le batch pour les travaux différables si les coûts totaux et les délais de mise à jour respectent les besoins ; retenir l'online pour les appels exigeant une réponse interactive, puis optimiser le matériel et le déploiement sur la base des tests.

### 5.5. Mesures minimales à obtenir avant le COMEX ou à inscrire comme conditions du MVP

1. **Catalogue :** nombre d'images réellement traitées, modifications/semaine, durée moyenne et p95 des jobs batch.
2. **Service online :** temps de réponse p50/p95, débit, erreurs, taux de disponibilité et capacité minimale.
3. **Qualité :** P@5 et Success@5, dont la comparaison avec une méthode simple sans IA.
4. **Facturation :** heures machine et SKU batch, heures d'instances online, stockage, recherche et logs.
5. **Arbitrage FinOps :** réduire fréquence batch sans rendre les recommandations obsolètes ; ajuster capacité online sans dégrader la latence.

## 6. Méthode de passage des hypothèses au prix Azure vérifiable

Utiliser l'[Azure Pricing Calculator](https://azure.microsoft.com/fr-fr/pricing/calculator/) pour chaque service retenu :

1. Région de référence, **France Central à étudier** sous réserve de disponibilité des SKU/modèles et des exigences de résidence des données.
2. Devise EUR, contrat, remises et TVA selon la convention financière du projet.
3. Taille d'instance CPU/GPU, nombre minimal/maximal d'instances, réplication et niveau de recherche.
4. **730 h/mois** pour une ressource continue (approximation de planification) et **heures réelles par job** pour le batch.
5. Volumes de photos, stockages, opérations, téléchargements, requêtes et logs.
6. Séparation des environnements réellement nécessaires : développement, test, préproduction et production.
7. Disponibilité, réseau privé et journaux : certains postes ajoutent un coût hors VM d'inférence.

La [documentation du calculateur](https://learn.microsoft.com/fr-fr/azure/cost-management-billing/costs/pricing-calculator) et l'[API Azure Retail Prices](https://learn.microsoft.com/en-us/rest/api/cost-management/retail-prices/azure-retail-prices) permettent de documenter l'origine des montants. **Ne jamais présenter les enveloppes ci-dessous comme des tarifs Microsoft.**

## 7. Enveloppes de simulation pour la phase de lancement — NON VALIDÉES

Ces montants sont conservés comme **hypothèses de travail** pour garder la cohérence avec les documents précédents ; ils doivent être remplacés lorsqu'un devis Azure paramétré sera établi.

| Phase | Enveloppe Azure de simulation | Commentaire |
|---|---:|---|
| PoC (3 semaines) | **400 €** | Exécutions expérimentales intermittentes |
| MVP (10 semaines) | **2 500 €** | Environnements d'essai, intégration et benchmarks |
| Run initial (4 semaines) | **700 €** | Mise en service progressive et stabilisation |
| **Total de lancement** | **3 600 €** | **Provision théorique, pas un tarif Azure vérifié** |

Avec le coût humain de lancement provisoire figurant dans le document 05 :

- Charge humaine **89 360 €**.
- Enveloppe Azure de lancement **3 600 €**.
- **Total Data/IA de lancement estimatif = 92 960 €**.

L'estimation du budget global ne devient présentable comme **budget à approuver** qu'après validation des affectations humaines, de l'architecture et des devis cloud.

## 8. Coûts récurrents Azure — scénario central de travail

**À ne pas oublier :** un job batch n'implique pas que toutes les dépenses Azure disparaissent entre deux exécutions : stockage, base de données, index de recherche, supervision et endpoint online continuent éventuellement à être facturés.

| Service ou famille | Enveloppe mensuelle simulée | Nature principale |
|---|---:|---|
| Azure Blob Storage | 100 € | Stockage et opérations |
| Azure SQL Database | 180 € | Métadonnées, préférences autorisées |
| Azure Data Factory | 120 € | Exécutions des pipelines |
| Azure AI Search (si retenu) | 300 € | Capacité de recherche provisionnée |
| Azure ML batch (actualisation des embeddings catalogue) | 150 € | Jobs de calcul ponctuels |
| Azure ML online (photo utilisateur) | 700 € | Calcul online et capacité réservée |
| Azure Monitor, journaux, Key Vault | 100 € | Monitoring, logs, secrets |
| Réseau et transferts | 150 € | Connectivité et données sortantes |
| **Total Azure mensuel simulé** | **1 800 €** | **À vérifier dans le calculateur** |

**Interprétation critique :** les **150 € batch** et **700 € online** ne correspondent **pas à deux options substituables réalisant la même tâche**. Il s'agit respectivement de la préparation périodique du catalogue et de l'inférence interactive. On ne peut donc pas dire « batch coûte 150 € au lieu de 700 € » pour l'ensemble du moteur ; ce serait une comparaison trompeuse. **De même, l'exemple 32 € contre 1 460 € de la section 5.2 est indépendant de ces provisions mensuelles.**

Si une recherche exhaustive satisfait les besoins, l'enveloppe **Azure AI Search (300 €)** peut être supprimée **dans un scénario alternatif**, après vérification des autres coûts de la solution. De même, l'online géré peut être comparé à une autre solution d'hébergement, **sans cumuler le prix des variantes**.

### Sensibilité du budget Azure, sans prétention tarifaire

| Scénario | Provision mensuelle Azure | Sur 12 mois |
|---|---:|---:|
| Sobre / optimisé | 1 200 € | 14 400 € |
| **Central** | **1 800 €** | **21 600 €** |
| Plus forte disponibilité / usage | 4 000 € | 48 000 € |

Ces enveloppes doivent évoluer si les usages réels ou les services avancés (essayage génératif) sont intégrés.

## 9. Vision consolidée : coûts Data/IA de construction et de Run

### 9.1. Au lancement (17 semaines)

| Nature | Montant estimatif |
|---|---:|
| Ressources humaines (PoC + MVP + Run initial) | **89 360 €** |
| Azure (mêmes phases, provision de simulation) | **3 600 €** |
| **Total** | **92 960 €** |

### 9.2. Run récurrent après la stabilisation

- Ressources humaines : **6 560 €/mois** → **78 720 €/an** (hypothèse du document 05).
- Azure central : **1 800 €/mois** → **21 600 €/an** (hypothèse du présent document).
- **Total récurrent : 8 360 €/mois**, soit **100 320 €/an**.

Ne pas **compter deux fois** les quatre semaines de Run initial : elles figurent déjà dans les **92 960 €** de lancement. Les montants annuels de **100 320 €** correspondent ensuite à **12 mois complets de Run stabilisé**, et non automatiquement à la première année calendaire du projet.

### 9.3. Périmètre non chiffré et risques de sous-estimation

- Développement Software de l'application mobile (explicitement hors champ du sujet).
- Génération ou modification d'images par IA pour essayage virtuel, tant que modèle/volume non connus.
- Prestataires externes éventuels, licences additionnelles, sécurité réseau avancée, haute disponibilité supplémentaire.
- Effet d'une hausse du trafic, d'une conservation plus longue des photos et de la multiplication des environnements.
- Coûts indirects non fournis dans le sujet (marges commerciales, acquisition clients, support métier, etc.) à discuter séparément du périmètre Data/IA.

## 10. Lien avec les prévisions de ventes et le futur ROI

**Données issues de l'expression des besoins métier :**

- Chiffre d'affaires annuel de référence : **10,4 M€**, dont **5,2 M€ web** et **5,2 M€ magasins**.
- Prévision marketing **à l'horizon de 24 mois** : **+14 % de CA web** et **+4 % de CA magasins**.
- À partir de la base annuelle donnée et si ces pourcentages s'appliquent à cette base :
  - **5,2 M€ × 14 % = 728 000 €** de CA web annuel additionnel à l'horizon visé.
  - **5,2 M€ × 4 % = 208 000 €** de CA magasin annuel additionnel à l'horizon visé.
  - **Total = 936 000 € de CA annuel supplémentaire à l'horizon de 24 mois**.

**Attention :** ces chiffres ne sont **pas** des bénéfices, ni un gain acquis dès la première année. La progression annuelle (montée en charge des utilisateurs et des ventes), la **marge contributive** sur les ventes et le calendrier de déploiement doivent être définis avant de calculer un **ROI financier**.

Dans le document ROI suivant, il faudra expliciter :

1. Une convention de calendrier (année du projet, début des coûts, date de mise en service).
2. Plusieurs scénarios de progression annuelle des gains marketing, jusqu'à la cible à 24 mois.
3. Une hypothèse de marge contributive à faire valider avec la finance.
4. Les coûts de lancement et de Run proratisés selon leur date réelle, **sans double comptage**.
5. La courbe des **gains cumulés vs coûts cumulés**, avec l'année de croisement et une analyse de sensibilité.

Le modèle pourra également présenter le **ROI apparent sur CA** demandé à des fins pédagogiques, mais devra le distinguer d'un **ROI économique fondé sur la marge** pour éviter de traiter le CA comme du profit.

## 11. Gouvernance, responsabilités et optimisation FinOps

| Profil | Responsabilités dans l'architecture batch / online |
|---|---|
| **Tech Lead Data** | Valider architecture, région, sécurité, niveaux de service, compromis coût/latence, dimensionnement de production |
| **Data Engineer (2)** | Pipelines Data Factory, contrôle qualité, détection des modifications du catalogue, cohérence produit/index et purge |
| **Data Scientist (3)** | Choix du modèle, embeddings, pertinence, protocole d'essai, dérive, apport de l'approche multimodale |
| **MLOps Engineer (1)** | Orchestration des jobs, déploiements online, suivi des ressources, autoscaling, surveillance et incidents |
| **Alicia / finance** | Arbitrage du périmètre et des hypothèses budgétaires ; validation des gains commerciaux et de la marge |

Mesures de maîtrise des coûts :

- Mettre les clusters batch à **capacité nulle au repos**, lorsque le service choisi le permet.
- Déclencher les actualisations seulement en cas de changement pertinent et surveiller les échecs d'indexation.
- Dimensionner l'online selon le débit réel, la latence p95 et la disponibilité, pas selon les 400 000 utilisateurs annuels pris isolément.
- Tester le besoin réel d'un index managé ; éviter le stockage d'images personnelles au-delà du nécessaire.
- Suivre séparément les coûts par **environnement, endpoint, job et composant**, avec budgets et alertes Azure Cost Management.
- Ne pas considérer une **alerte budgétaire** comme un mécanisme automatique d'arrêt des ressources.

## 12. Risques et décisions à faire valider

1. **Catalogues** : nombre de produits actifs, nombre réel d'images, fréquence et volume des mises à jour.
2. **SLA produit** : latence p95, disponibilité, disponibilité de l'index et tolérance à un délai d'actualisation catalogue.
3. **Trafic** : pics horaires et saisonniers, nombre réel de photos par utilisateur, adoption de la fonctionnalité.
4. **Azure** : région, SKU, quotas, tarifs et engagements, réplication, réseau et sécurité.
5. **Inférence** : CPU ou GPU, nombre minimum d'instances, rôle éventuel d'une alternative à la demande.
6. **Vector search** : faut-il Azure AI Search, ou un index simple suffit-il au lancement ?
7. **RGPD** : durée de conservation, photos volontaires de garde-robe, suppression et journaux.
8. **Fonctionnalités supplémentaires** : périmètre de l'essayage virtuel et des recommandations par tendances/blogs.
9. **ROI** : courbe réelle de gains, marge contributive, coûts non Data/IA à présenter comme limites de l'étude.
10. **Évaluation** : expérimentation réelle batch/online, latence, coût de calcul par lot et par requête.

## 13. Sources et traçabilité

**Concordance avec la nouvelle version du document 03 :** l'approche A (CLIP/OpenCLIP ou modèle compatible) reste la référence ; l'approche B vision-langage est le comparateur possible. Toutes deux peuvent préparer les attributs et images catalogue en **batch**. En production, une **nouvelle** photo doit être analysée **online** pour une expérience interactive. Le PoC peut simuler ce traitement hors ligne pour mesurer la qualité, sans endpoint online permanent. Les seuils **Precision@5 ≥ 60 %** et **Success@5 ≥ 80 %** restent des hypothèses métier à confirmer.

### Sources métier

- Expression des besoins d'Alicia (CA de référence, +14 % web, +4 % magasins, cible 400 000 utilisateurs annuels, images et métadonnées disponibles).
- `P11_Profils_Data.pdf` : coûts journaliers internes (**Data Scientist 350 €/j ; Data Engineer 370 €/j ; Tech Lead Data 400 €/j ; MLOps 360 €/j**).
- Autres documents de cadrage du dossier `docs/`.

### Documentation officielle Microsoft

- [Azure Machine Learning — endpoints online et batch](https://learn.microsoft.com/fr-fr/azure/machine-learning/concept-endpoints?view=azureml-api-2).
- [Azure Machine Learning — batch endpoints, allocation de calcul et coûts](https://learn.microsoft.com/en-us/azure/machine-learning/concept-endpoints-batch?view=azureml-api-2).
- [Azure Machine Learning — coûts d'un managed online endpoint](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-view-online-endpoints-costs?view=azureml-api-2).
- [Azure ML — exemple de cluster batch à capacité nulle au repos](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-use-batch-model-deployments?view=azureml-api-2).
- [Azure Pricing Calculator](https://azure.microsoft.com/fr-fr/pricing/calculator/).
- [Documentation Azure Pricing Calculator](https://learn.microsoft.com/fr-fr/azure/cost-management-billing/costs/pricing-calculator).
- [API Azure Retail Prices](https://learn.microsoft.com/en-us/rest/api/cost-management/retail-prices/azure-retail-prices).
- [Azure AI Search — comparaison des algorithmes de recherche vectorielle](https://learn.microsoft.com/en-us/azure/search/vector-search-ranking).
- [Azure Cost Management — budgets et alertes](https://learn.microsoft.com/fr-fr/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending).

---

**Synthèse pour la soutenance :** nous défendons une **architecture hybride**. Le **batch** prépare et actualise les données et embeddings du catalogue à la cadence utile ; l'**online** répond aux nouvelles photos des clients. Cette séparation répond aux exigences métier et **peut réduire les heures de calcul dédiées aux travaux différables**, mais le gain monétaire doit être confirmé par des paramètres Azure vérifiables. Les chiffres de ce document constituent une **base de scénario**, non un budget final approuvé.
