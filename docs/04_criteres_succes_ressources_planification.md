# P11 — Critères de succès, ressources et planification du PoC

**Projet :** Fashion-Insta — Recommandation vestimentaire à partir de photographies  
**Mission OpenClassrooms :** Réalisez le cadrage d'un projet IA — Partie 1  
**Statut :** Proposition de cadrage à valider avec Alicia et les représentants métier  
**Périmètre pilote retenu :** Pantalons et jeans (120 références, soit 27 % du chiffre d'affaires annuel communiqué)

> **Important :** Ce document décrit le **PoC à réaliser après autorisation du COMEX**. Dans le cadre du projet pédagogique, il n'est pas demandé de développer ou d'exécuter ce PoC : le livrable de la partie 1 est une **présentation PowerPoint de cadrage**. Les durées, seuils et moyens indiqués ci-dessous sont des **hypothèses de planification**, et non des résultats déjà constatés.

## 1. Objectif et décision attendue

### 1.1. Objectif du PoC

Vérifier, sur une catégorie de produits limitée, qu'une solution d'IA est capable de **recommander des pantalons et jeans Fashion-Insta pertinents à partir de photographies externes**, avec des résultats supérieurs à une recherche simple par attributs du catalogue.

Le PoC doit produire des éléments objectifs permettant de décider si un développement plus large est justifié.

### 1.2. Hypothèses de départ

- **Catalogue cible :** jusqu'à 120 références de pantalons et jeans, si les images et les métadonnées sont exploitables.
- **Données produits :** photographies (plusieurs angles selon disponibilité), descriptions, prix et tags de style.
- **Données de test :** 40 photographies représentatives, distinctes des photographies du catalogue et utilisables légalement.
- **Approche principale :** embeddings visuels issus d'un modèle préentraîné de type CLIP, puis classement par similarité cosinus.
- **Approche alternative :** modèle multimodal décrivant les caractéristiques d'un vêtement, puis mise en correspondance avec les attributs produits.
- **Environnement envisagé :** services Microsoft Azure déjà retenus par Fashion-Insta, sous réserve de leur disponibilité et des autorisations internes.

Le moteur ne sera pas intégré à une application mobile : il suffira de montrer des **photos d'entrée et des listes classées de recommandations**.

## 2. Critères de succès

Les seuils ci-dessous sont **proposés pour discussion**. Ils devront être confirmés avec Alicia **avant** toute expérimentation.

| Dimension | Indicateur | Objectif proposé | Intérêt métier |
|---|---|---|---|
| Pertinence globale | **Precision@5** | **≥ 60 %** | Une majorité des cinq suggestions présentées est jugée pertinente |
| Utilité de chaque recherche | **Success@5** | **≥ 80 %** | La plupart des utilisateurs obtiennent au moins une suggestion pertinente |
| Valeur ajoutée de l'IA | **Gain de Precision@5 face à une méthode sans IA** | **≥ 10 points de pourcentage** | Montrer que la solution apporte davantage qu'un filtrage simple |
| Faisabilité | Démonstration reproductible sur les données du PoC | **Oui** | Prouver que la chaîne de traitement fonctionne de bout en bout |
| Conformité et données | Utilisation autorisée des images et précautions RGPD appliquées | **Oui** | Écarter un risque bloquant avant d'envisager la suite |

### 2.1. Définition des métriques

**Precision@5** : pour chaque photo de test, on examine les cinq articles proposés et on calcule la proportion jugée pertinente. La métrique finale est la moyenne de ces proportions sur les photos de test.

Si 40 photos sont évaluées et si 5 recommandations sont affichées pour chacune, l'évaluation porte sur **200 recommandations**. Un seuil de 60 % correspond à **au moins 120 évaluations pertinentes sur 200**, si chaque photo reçoit bien cinq propositions.

**Success@5** : part des photos de test pour lesquelles **au moins un** des cinq articles recommandés est jugé pertinent.

Sur 40 photos, un objectif de 80 % signifie qu'**au moins 32 photos sur 40** doivent recevoir une proposition pertinente dans les cinq premières.

**Gain par rapport à la référence** : différence, exprimée en **points de pourcentage**, entre la Precision@5 du modèle IA et celle d'une méthode de recommandation simple, évaluées sur exactement les mêmes photos.

> Exemple purement illustratif : si le modèle IA atteint 65 % de Precision@5 et la méthode de référence 52 %, son gain est de **13 points**, et non de 13 %.

### 2.2. Qui décide qu'une recommandation est pertinente ?

Avant le test, un représentant métier (merchandising, produit ou e-commerce) définit une grille de jugement : **type de vêtement, coupe, couleur, matière apparente et style**. Une recommandation est notée « pertinente » ou « non pertinente » sur cette grille.

Deux évaluateurs métier jugent idéalement les recommandations **sans connaître le modèle qui les a produites**. En cas de désaccord, ils arbitrent ensemble. Les critères doivent être suffisamment explicites pour limiter la subjectivité.

## 3. Protocole d'évaluation

### 3.1. Préparer les données

1. Extraire les données utiles des 120 références avec l'équipe Data Engineering.
2. Vérifier les droits d'utilisation et la qualité des photographies.
3. Identifier les images manquantes, les doublons et les métadonnées incohérentes.
4. Sélectionner **40 photos de test** prises dans des conditions variées (angles, éclairage, arrière-plans, styles), sans réutiliser les photographies exactes du catalogue.
5. Prévoir des photos pour lesquelles des articles raisonnablement proches existent dans le catalogue : un catalogue trop restreint peut empêcher toute bonne recommandation, même avec un algorithme correct.

Si des photographies montrent des personnes identifiables, prévoir les bases légales, autorisations, accès restreints et durées de conservation appropriés. **Éviter par défaut les visages et autres informations personnelles non nécessaires** à l'expérimentation.

### 3.2. Comparer trois configurations

| Configuration | Principe | Rôle dans le test |
|---|---|---|
| **Référence sans IA** | Classement simple sur les tags ou attributs disponibles (type, style, couleur) | Vérifier l'apport réel de l'IA |
| **Approche A (prioritaire)** | Embeddings visuels préentraînés + similarité cosinus | Tester une solution rapide et relativement simple |
| **Approche B (alternative)** | Analyse des images par modèle multimodal + recherche sur attributs | Tester l'intérêt d'une représentation sémantique explicable |

Pour conserver un PoC court, **l'approche A est prioritaire**. L'approche B fait l'objet d'une exploration limitée dans le temps ; elle n'est poursuivie que si les accès et le calendrier le permettent.

### 3.3. Mesurer les résultats

- Utiliser les **mêmes 40 photos** et le **même catalogue** pour toutes les approches.
- Présenter cinq articles distincts par photographie (Top 5).
- Faire évaluer chaque suggestion par les représentants métier selon une grille commune.
- Calculer Precision@5 et Success@5, puis le gain de Precision@5 face à la référence sans IA.
- Documenter les échecs : mauvaise catégorie, confusion de coupe, couleur, contexte photographique, etc.
- Conserver quelques exemples anonymisés de recommandations réussies et ratées pour le COMEX.

Les 40 photos constituent un **échantillon pilote**, pas une preuve statistique définitive de performance en production. Le PoC sert à décider si une expérimentation plus large mérite d'être financée.

## 4. Profils nécessaires et charge estimée

**Hypothèse : 15 jours ouvrés de calendrier (trois semaines après autorisation du COMEX)**, avec des tâches en parallèle. Cette répartition doit être **strictement cohérente avec le document 05, la timeline et le PowerPoint de la Partie 2**.

Les coûts salariaux journaliers des profils Data sont fournis par Fashion-Insta dans `sources/P11_Profils_Data.pdf`. Les experts métier, le Product et le DPO sont **consultés** pour rendre le PoC recevable, mais leurs jours-homme ne sont **pas ajoutés sans barème** au budget Data/IA de 30 JH. Leur contribution reste à organiser et à chiffrer séparément si un budget complet est demandé.

| Profil Data interne | Effectif disponible | Charge totale du profil pendant le PoC | Tarif salarial fourni | Coût imputé au PoC |
|---|---:|---:|---:|---:|
| **Data Scientist** | 3 | **16 JH** | 350 €/JH | **5 600 €** |
| **Data Engineer** | 2 | **6 JH** | 370 €/JH | **2 220 €** |
| **Tech Lead Data** | 1 | **4 JH** | 400 €/JH | **1 600 €** |
| **MLOps Engineer** | 1 | **4 JH** | 360 €/JH | **1 440 €** |
| **Total Data/IA** | **7 personnes disponibles** | **30 JH** | — | **10 860 €** |

**Responsabilités :** DS = baseline sans IA, embeddings, métriques et analyse des erreurs ; DE = extraction, correspondance images/références, qualité ; Tech Lead = architecture, arbitrages, validation technique ; MLOps = environnement Azure expérimental, reproductibilité et suivi des consommations. Les deux évaluateurs métier définissent et appliquent la grille de pertinence, avec l'appui d'Alicia ; les fonctions Software et DPO sont associées aux interfaces et aux contrôles.

Le repère historique de **3 jours-homme de Data Engineer** concerne l'extraction des informations sur **toutes les 875 références** et n'est ni un délai garanti ni une charge à additionner aux **6 JH prévus** pour le PoC.

### 4.1. Budget et convention de périmètre

- **Ressources Data/IA du PoC : 10 860 €**, calculées sur les **tarifs salariaux officiels fournis**, sous réserve de validation des affectations. Il s'agit d'une valorisation des ressources internes, pas nécessairement d'un coût de trésorerie nouveau.
- **Azure PoC : 400 €**, **enveloppe de simulation** issue du document 06, sans devis Microsoft vérifié.
- **Coût de cadrage PoC Data/IA : 11 260 €**, estimation de travail hors temps des experts métier, développement mobile et autres coûts non documentés.
- **Différence de périmètre** : trois semaines **avant** le COMEX concernent la préparation du cadrage ; trois semaines **après autorisation** concernent les essais du PoC.

Les **30 JH** sont une charge cumulée, **pas** une durée de 30 jours ouvrés. Aucune ressource n'est réputée disponible à 100 % par défaut.

## 5. Planning prévisionnel du PoC

> **Attention aux deux échéances :** Alicia dispose de **trois semaines pour préparer le cadrage avant la réunion du COMEX**. Le tableau suivant décrit **une proposition distincte de trois semaines pour réaliser le PoC**, uniquement si le COMEX donne son accord.

| Période du PoC | Travaux principaux | Livrable / jalon |
|---|---|---|
| **Semaine 1 — J1 à J5** | Lancement, validation de la grille métier, extraction et vérification des données, préparation du jeu de test, accès Azure | **Jalon 1 :** données et protocole validés |
| **Semaine 2 — J6 à J10** | Méthode de référence, expérimentation des embeddings visuels, calcul des premières recommandations ; exploration limitée de l'alternative multimodale | **Jalon 2 :** premiers résultats comparatifs |
| **Semaine 3 — J11 à J15** | Évaluation par les métiers, analyse des erreurs, contrôle des contraintes, consolidation des métriques et restitution | **Jalon 3 :** décision Go / No-Go documentée |

Le planning suppose une disponibilité rapide des images, des évaluateurs et des ressources Azure. Un blocage d'accès ou une qualité de données insuffisante entraîne une **réévaluation du périmètre**, plutôt qu'un prolongement automatique du PoC.

## 6. Gouvernance et suivi

- **Sponsor / décision :** Alicia, VP Product ; décision de financement et de poursuite à soumettre au COMEX.
- **Pilotage :** chef de projet IA ; suivi du calendrier, des risques, des décisions et de la charge.
- **Responsabilité technique :** Data Scientist / ML Engineer et Data Engineer.
- **Validation métier :** merchandising ou e-commerce, responsable de la définition et du jugement de la pertinence.
- **Contrôles transverses :** référents Azure, RGPD et sécurité.

**Rythme proposé :** point de lancement, deux points courts de suivi par semaine, revue des résultats intermédiaires, puis restitution finale.

**Éléments suivis :** nombre de références utilisables, nombre de photos de test validées, avancement des expériences, Precision@5, Success@5, gain face à la référence, charge consommée, incidents et risques ouverts.

## 7. Principaux risques et mesures de maîtrise

| Risque | Conséquence | Prévention / action |
|---|---|---|
| Données produits dispersées ou incomplètes | Retard, comparaisons peu fiables | Extraction ciblée et contrôle qualité en semaine 1 |
| Photos de test non représentatives | Résultats trompeurs | Varier conditions de prise de vue et styles |
| Petit catalogue sans vêtements réellement similaires | Recommandations jugées mauvaises à tort | Vérifier la couverture du catalogue et interpréter les échecs |
| Définition du « même style » trop subjective | Notes métier incohérentes | Grille explicite et arbitrage des désaccords |
| Photos personnelles mal protégées | Risques RGPD et réputationnels | Minimisation, autorisations, accès restreints, durée de conservation limitée |
| Accès ou coûts Azure non maîtrisés | Blocage technique ou dépassement | Valider les accès, quotas et plafonds de consommation en amont |
| Volonté d'étendre le périmètre pendant le PoC | Dérive des délais | Garder le périmètre « pantalons et jeans / 120 références » et reporter les options |

## 8. Décision finale : Go / No-Go

### Go — Poursuite vers un pilote élargi

La recommandation de poursuivre est justifiée **si toutes les conditions suivantes sont satisfaites** :

1. Precision@5 **≥ 60 %**.
2. Success@5 **≥ 80 %**.
3. Gain de Precision@5 **≥ 10 points** par rapport à la méthode de référence sans IA.
4. Résultats reproductibles et données exploitables dans le périmètre retenu.
5. Absence de blocage non résolu sur les droits d'utilisation des images, la sécurité ou la protection des données.

### No-Go ou réorientation

Si un ou plusieurs seuils ne sont pas atteints, **ne pas conclure automatiquement que l'IA est impossible**. Examiner les causes (qualité des photos, couverture du catalogue, définition de la pertinence, approche utilisée, taille de l'échantillon), puis proposer au sponsor soit un **arrêt**, soit un **nouveau cadrage limité et chiffré**.

**Le PoC ne démontre pas à lui seul** les prévisions marketing de croissance du CA web de 14 % ou du CA en magasin de 4 % à 24 mois. Ces hypothèses relèveront d'une expérimentation commerciale ultérieure (par exemple un pilote utilisateur avec mesure de conversion).

## 9. Restitution attendue à l'issue du PoC

- Une synthèse des données disponibles et de leurs limites.
- Un comparatif clair entre la méthode sans IA, l'approche principale et, si testée, l'alternative multimodale.
- Les résultats Precision@5, Success@5, gain par rapport à la référence et exemples de recommandations.
- Le relevé des risques, des coûts et des difficultés techniques.
- Une recommandation explicite **Go / No-Go**, accompagnée des conditions d'un éventuel pilote élargi.

## 10. Points à faire valider avec Alicia

- Le choix des **120 références de pantalons et jeans** comme périmètre initial.
- La disponibilité et les droits d'utilisation des **40 photographies de test**.
- La définition métier de la pertinence et la disponibilité des évaluateurs.
- Les seuils **Precision@5 ≥ 60 %**, **Success@5 ≥ 80 %** et **gain ≥ 10 points**.
- La proposition de **15 jours ouvrés / 30 jours-homme**, à ajuster en fonction des disponibilités.
- Les accès Azure et les plafonds de consommation.
- Les critères de décision finale et le format de restitution au COMEX.

---

### Synthèse pour la future présentation COMEX

**Nous proposons un PoC de recommandation visuelle ciblé sur 120 pantalons et jeans, sur Azure après accord du COMEX, sur trois semaines de calendrier et pour une charge indicative de 30 jours-homme Data/IA (10 860 € internes + 400 € Azure hypothétiques).** La faisabilité sera évaluée sur 40 photographies de test, avec une comparaison à une méthode sans IA. Les critères de réussite proposés sont une Precision@5 d'au moins 60 %, une Success@5 d'au moins 80 % et un gain d'au moins 10 points de Precision@5. Les résultats guideront une décision Go / No-Go ; aucun développement de l'application mobile complète n'est inclus.
