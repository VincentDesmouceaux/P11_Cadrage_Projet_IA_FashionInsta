# P11 — Partie 2 : estimation des charges et coûts humains Data/IA

**Entreprise :** Fashion-Insta  
**Mission :** Réalisez le cadrage d'un projet IA — Partie 2  
**Statut :** estimation de cadrage, non validée par Alicia, le Tech Lead Data ou le COMEX  
**Périmètre financier :** uniquement les activités Data & IA ; hors développement de l'application mobile, comptes clients, paiement, publicité et coûts commerciaux.  
**Documents associés :** `03_approches_techniques_poc.md`, `06_dimensionnement_couts_azure_partie2.md`, `07_analyse_roi_rentabilite_partie2.md`.

> **Décision d'architecture prise en compte :** préparer et actualiser les embeddings du catalogue en **batch**, servir l'analyse d'une nouvelle photographie et le Top 5 en **online** lorsque l'application sera en production. Le PoC peut simuler la partie interactive hors ligne, sans endpoint permanent.

## 1. Objectif, phases et conventions

L'objectif est de traduire les composants du System Design en **charges de travail (jours-homme, JH)** et en **coûts salariaux internes**, pour planifier les compétences nécessaires et alimenter l'étude de rentabilité.

| Phase | Semaines | Durée | Finalité / jalon |
|---|---|---:|---|
| **PoC** | S1–S3 | 3 semaines | Démontrer la faisabilité sur 120 références pantalons/jeans, 40 photos de test ; décision Go/No-Go |
| **MVP Data/IA** | S4–S13 | 10 semaines | Industrialiser alimentation catalogue, préparation batch, moteur online, préférences/feedback, sécurité et monitoring |
| **Run initial** | S14–S17 | 4 semaines | Mise en service progressive, réglages de capacité, stabilisation et bilan |
| **Run récurrent** | À partir de S18 | Continu | Exploitation, supervision, actualisation des données et modèles, maîtrise des coûts |

- **1 JH** = une journée de travail imputée à un profil, **pas** une journée calendaire écoulée. Une phase de 10 semaines peut mobiliser plusieurs personnes en parallèle.
- **17 semaines** est la durée envisagée pour PoC + MVP + Run initial, **pas** le temps de préparation de la présentation au COMEX.
- Les effectifs représentent l'équipe interne disponible, **pas un engagement à temps plein** sur ce projet.
- Les hypothèses de charges, de calendrier et de périmètre MVP doivent être validées avant toute approbation budgétaire.

## 2. Profils et tarifs internes de référence

*Source : document officiel fourni par Fashion-Insta, `sources/P11_Profils_Data.pdf`.*

| Profil | Effectif | Coût salarial journalier |
|---|---:|---:|
| Data Scientist (DS) | 3 | **350 € / JH** |
| Data Engineer (DE) | 2 | **370 € / JH** |
| Tech Lead Data (TL) | 1 | **400 € / JH** |
| MLOps Engineer | 1 | **360 € / JH** |
| **Total équipe** | **7 personnes** | — |

Ces coûts correspondent à une **valorisation salariale des ressources internes**. Ils ne représentent pas nécessairement une dépense de trésorerie additionnelle si les salariés sont déjà en poste ; pour le ROI, la convention de coût interne doit être validée avec la finance et appliquée de façon cohérente.

## 3. Répartition des jours-homme, par phase et par profil

| Profil | PoC (3 sem.) | MVP (10 sem.) | Run initial (4 sem.) | **Total JH** |
|---|---:|---:|---:|---:|
| Data Scientist | 16 | 75 | 10 | **101** |
| Data Engineer | 6 | 55 | 8 | **69** |
| Tech Lead Data | 4 | 20 | 4 | **28** |
| MLOps Engineer | 4 | 30 | 14 | **48** |
| **Total** | **30** | **180** | **36** | **246** |

Cette matrice **conserve les hypothèses financières du cadrage déjà utilisé**. Le détail d'activités ci-dessous propose une ventilation opérationnelle qui reste à confirmer, sans modifier les totaux.

### 3.1. PoC — 30 JH, 3 semaines

| Profil | Activités proposées | JH |
|---|---|---:|
| DS | Baseline sans IA (3), embeddings/Top 5 (7), évaluation métier et analyse des erreurs (6) | **16** |
| DE | Extraction/catalogue (3), contrôle qualité et dédoublonnage (2), accès/traçabilité (1) | **6** |
| TL | Choix d'architecture (2), exigences d'interface/sécurité (1), revue Go/No-Go (1) | **4** |
| MLOps | Environnement expérimental (2), reproductibilité (1), mesure initiale du coût de calcul (1) | **4** |
| **Total** | | **30** |

**Livrables :** préparation des photos et métadonnées, comparaison baseline/approches IA, Precision@5, Success@5, relevé de latence et de ressources, décision Go/No-Go. Le travail peut être démontré sans déploiement d'un endpoint online 24 h/24.

### 3.2. MVP Data/IA — 180 JH, 10 semaines

| Profil | Activités proposées | JH |
|---|---|---:|
| DS | Amélioration embeddings/modèle (30), classement/filtres/personnalisation (25), évaluation qualité et retours (20) | **75** |
| DE | Ingestion et qualité catalogue (25), mises à jour incrémentales/index (15), stockage, droits, rétention et purge (15) | **55** |
| TL | Architecture et revues (8), interfaces/Data-Software (4), performance et disponibilité (4), sécurité/RGPD (4) | **20** |
| MLOps | Orchestration des jobs batch (8), serving online et déploiement (10), observabilité et tests de charge (8), suivi coûts (4) | **30** |
| **Total** | | **180** |

**Livrables :** synchronisation catalogue, jobs batch d'embeddings, index ou recherche simple documentée, endpoint/service de recommandation interactive, filtres métier, remontée de feedback, contrôles RGPD, monitoring et premier test de charge.

Les fonctions d'**essayage virtuel génératif**, de changement avancé de style et d'analyse automatique de blogs/tendances font l'objet d'un **arbitrage séparé** : leur industrialisation n'est **pas incluse** dans ces 180 JH.

### 3.3. Run initial — 36 JH, 4 semaines

| Profil | Activités proposées | JH |
|---|---|---:|
| DS | Analyse de pertinence et des cas d'échec (6), suivi de dérive (4) | **10** |
| DE | Fiabilité de la synchronisation catalogue (6), contrôle purge/rétention (2) | **8** |
| TL | Arbitrages incidents et disponibilité (2), revue d'architecture et bilan (2) | **4** |
| MLOps | Déploiement/rollbacks (4), réglages capacité/latence (5), monitoring (3), alertes/incidents (2) | **14** |
| **Total** | | **36** |

**Livrables :** bilan des temps de réponse p50/p95, taux d'erreurs, disponibilité et coût par requête ; procédures d'incident, alertes, reprise et décision de passage en Run récurrent.

## 4. Rôles et responsabilités sur les briques Data/IA

Le tableau associe un **pilote de construction**, un **pilote de maintenance** et les contributions clés. Il reprend le découpage du System Design et évite d'attribuer le développement mobile à l'équipe Data.

| Brique | Construction — pilote | Maintenance — pilote | Contributeurs / points de contrôle |
|---|---|---|---|
| Ingestion et qualité du catalogue | DE | DE | TL valide schémas, accès et fiabilité |
| Blob, métadonnées, index et filtres | DE | DE | DS définit attributs et pertinence ; MLOps surveille la disponibilité technique |
| Embeddings catalogue **batch** | DS (modèle) + DE (données) | DE + MLOps | MLOps orchestre les jobs ; TL arbitre les coûts/versions |
| Embedding photo client et recommandations **online** | DS (qualité) + MLOps (serving) | MLOps | TL fixe exigences latence/capacité ; DS suit les métriques |
| Préférences et retours utilisateur | DS + DE | DE | Product/métiers définissent les règles et le consentement |
| Surveillance, dérive, CI/CD, autoscaling, reprise | MLOps | MLOps | DS suit pertinence ; TL valide SLA et sécurité |
| Suppression, durée de conservation et traçabilité | DE | DE | TL, référents RGPD et sécurité : validation transverse |
| Essayage virtuel génératif (option) | À étudier : DS + TL | À définir | Charge et coûts **hors scénario central** |

**Acteurs non chiffrés dans la présente matrice :** Alicia (sponsor), équipe Product/Software, représentants métier chargés de juger les recommandations, référent RGPD/sécurité, finance. Leur coordination est nécessaire, mais leurs charges ne figurent pas dans les quatre tarifs Data fournis ; ce **périmètre budgétaire partiel** doit être explicite devant le COMEX.

## 5. Coûts humains : recalcul détaillé

Formule : `coût_phase = somme(JH_profil × tarif_journalier_profil)`.

| Profil | PoC | MVP | Run initial | **Total** |
|---|---:|---:|---:|---:|
| Data Scientist | 5 600 € | 26 250 € | 3 500 € | **35 350 €** |
| Data Engineer | 2 220 € | 20 350 € | 2 960 € | **25 530 €** |
| Tech Lead Data | 1 600 € | 8 000 € | 1 600 € | **11 200 €** |
| MLOps Engineer | 1 440 € | 10 800 € | 5 040 € | **17 280 €** |
| **Total** | **10 860 €** | **65 400 €** | **13 100 €** | **89 360 €** |

**À retenir :** **246 JH** et **89 360 €** de charge humaine de construction et lancement sur **17 semaines**. Cette somme inclut déjà les quatre semaines de **Run initial**.

## 6. Run récurrent après stabilisation (à partir de S18)

| Profil | JH/mois | Coût salarial mensuel |
|---|---:|---:|
| Data Scientist | 4 | 1 400 € |
| Data Engineer | 4 | 1 480 € |
| Tech Lead Data | 2 | 800 € |
| MLOps Engineer | 8 | 2 880 € |
| **Total** | **18 JH/mois** | **6 560 €/mois** |

- **Coût Run humain annualisé : 78 720 €/an**, uniquement pour **12 mois complets** après la stabilisation.
- En année de lancement, **proratiser** les mois réels après S17 ; ne pas additionner mécaniquement 89 360 € et 78 720 € comme s'ils concernaient la même période.
- La charge MLOps peut croître avec une exigence de disponibilité renforcée, la multiplication des endpoints, la génération d'images, la supervision des pics et la complexité des incidents.

## 7. Capacité réelle de l'équipe et faisabilité temporelle

Hypothèse de capacité maximale théorique : **5 JH par personne et par semaine** si affectation à temps plein, hors congés, réunions internes et autres projets.

| Phase / profil | JH prévus | Capacité maximale théorique | Charge moyenne représentée |
|---|---:|---:|---:|
| PoC — DS (3) | 16 | 45 | 36 % |
| PoC — DE (2) | 6 | 30 | 20 % |
| PoC — TL (1) | 4 | 15 | 27 % |
| PoC — MLOps (1) | 4 | 15 | 27 % |
| MVP — DS (3) | 75 | 150 | 50 % |
| MVP — DE (2) | 55 | 100 | 55 % |
| MVP — TL (1) | 20 | 50 | 40 % |
| MVP — MLOps (1) | 30 | 50 | 60 % |
| Run initial — DS (3) | 10 | 60 | 17 % |
| Run initial — DE (2) | 8 | 40 | 20 % |
| Run initial — TL (1) | 4 | 20 | 20 % |
| Run initial — MLOps (1) | 14 | 20 | 70 % |

**Interprétation :** la charge prévue respecte les capacités maximales *théoriques* de chaque profil. Cela ne prouve pas la disponibilité effective des personnes ni la faisabilité de toutes les dépendances. Le **MLOps**, seul sur son poste, constitue un point de vigilance, en particulier pendant le Run initial (14 JH sur 20 jours ouvrés).

**Conditions de faisabilité :** disponibilité nominative, suppléance/astreinte en production si nécessaire, délais d'accès Azure, données catalogue effectivement exploitables, validation juridique des photos, capacité du Product/Software à consommer les interfaces, calendrier des évaluateurs métier.

## 8. Lien avec la scalabilité, le batch et l'online

Notre modèle d'effort tient compte d'une **architecture hybride** :

- Le catalogue est connu à l'avance : les DE industrialisent les sources, les DS choisissent les représentations visuelles et le MLOps orchestre les **jobs batch**, exécutés en cas de modification pertinente, avec réindexation complète si le modèle change.
- Une photographie utilisateur est nouvelle : le DS spécifie l'encodage et le ranking, le MLOps déploie le **service online** et gère capacité minimale, autoscaling, supervision et reprise.
- Pour le **PoC**, il suffit de simuler les requêtes successivement : aucun coût humain dédié à un service haute disponibilité 24/7 n'est supposé nécessaire.
- Pour le **MVP/Run**, les tests de charge (p50/p95, débit, erreurs) et les scénarios d'usage doivent confirmer l'affectation. Le marketing prévoit **400 000 utilisateurs actifs sur une année** à terme, pas 400 000 requêtes simultanées.
- Les simulations du document 06 utilisent notamment un scénario central de **5 recommandations par utilisateur et par an**, soit **2 000 000 demandes/an**, ainsi qu'un scénario de pointe : ces paramètres ne sont **pas des données marketing observées**.

**Attention financière :** le recours au batch peut limiter les heures de calcul réservées à l'actualisation du catalogue. Il **ne supprime pas** le coût ni l'effort d'exploitation du service online ou des composants persistants (stockage, index, journaux).

## 9. Consolidation avec les coûts Azure du document 06

Le document `06_dimensionnement_couts_azure_partie2.md` conserve les **enveloppes budgétaires de simulation non vérifiées par Microsoft** suivantes :

| Poste | Estimation de travail |
|---|---:|
| Coût humain PoC + MVP + Run initial | **89 360 €** |
| Azure de lancement (3 + 10 + 4 semaines) | **3 600 €** |
| **Total lancement Data/IA** | **92 960 €** |
| Run humain après stabilisation | **6 560 €/mois** |
| Azure Run central simulé | **1 800 €/mois** |
| **Total Run central** | **8 360 €/mois, soit 100 320 €/an** |

Les enveloppes Azure **ne constituent pas un devis** et peuvent évoluer après choix région/SKU/CPU/GPU, niveau de service, politique de stockage, latence et disponibilité. Les calculs humains sont arithmétiquement cohérents avec les coûts journaliers fournis ; la pertinence des affectations reste à confirmer.

**Ne pas double compter** le Run initial (S14–S17) lors du passage du budget de lancement à l'année de Run récurrent. L'essayage virtuel génératif et le développement mobile ne sont pas couverts par ces montants.

## 10. Implications pour l'étude de rentabilité

Le document 07 doit :

1. répartir les coûts **dans le temps** (PoC/MVP/Run initial, puis Run récurrent proratisé) ;
2. comparer les **gains attribuables au projet** aux coûts Data/IA, sans confondre chiffre d'affaires et marge ;
3. appliquer de façon cohérente une éventuelle **marge contributive hypothétique** (par exemple 30 %, **non fournie par Alicia**) ;
4. identifier explicitement le périmètre financier **partiel** de l'analyse (hors application mobile, acquisition clients et essayage génératif) ;
5. proposer des tests de sensibilité : montée en charge, coûts de serving, maintien en conditions opérationnelles, baisse des performances du modèle et réduction des gains.

**Rappel métier :** les prévisions Marketing visent à 24 mois **+14 % du CA web** et **+4 % du CA magasins** à partir des bases annuelles de **5,2 M€** chacune : soit **936 000 € de CA additionnel annuel à l'horizon de 24 mois**, et **non** 936 000 € de bénéfice annuel immédiat.

## 11. Gouvernance, risques et décisions ouvertes

| Décision/risque | Propriétaire proposé | À confirmer |
|---|---|---|
| Disponibilité des 7 professionnels et autres engagements | Tech Lead + responsables d'équipe | Planning et disponibilité nominative |
| Validation des caractéristiques de style et des seuils qualité | Alicia + représentants métier + DS | Ground truth, P@5, Success@5 |
| Fréquence d'actualisation et fraîcheur du catalogue | DE + responsable catalogue | SLA de mise à jour, données sources |
| Dimensionnement online, sécurité et autoscaling | MLOps + TL | SLA/latence et mesures de charge |
| Protection des photographies et purge | DE + référent RGPD | Finalité, base légale, conservation, suppression |
| Vérification des coûts cloud et FinOps | MLOps + TL + finance | SKU/région/tarifs Azure contractuels |
| Marge contributive et conventions ROI | Alicia + finance | Marge, montée en charge et coûts hors Data/IA |
| Extension essayage virtuel | Product + TL + DS | Étude séparée, données, faisabilité, budget |

## 12. Conclusion pour la soutenance

Nous proposons **246 JH sur 17 semaines** pour construire et stabiliser la solution **Data/IA** : **30 JH PoC**, **180 JH MVP**, **36 JH Run initial**, valorisés au total **89 360 €** avec les tarifs salariaux Fashion-Insta. Le **Run récurrent** mobiliserait ensuite **18 JH/mois**, soit **6 560 €/mois** de coût humain simulé.

Ces charges couvrent l'**ingestion et la qualité des données, les embeddings catalogue batch, l'inférence interactive online, la maintenance, la sécurité, l'observabilité et la gouvernance technique**. Elles excluent le développement mobile et les fonctions avancées non arbitrées. Le dimensionnement exact reste conditionné aux usages mesurés et aux exigences de service. Le budget Azure et la projection de rentabilité sont distingués dans les documents 06 et 07.

---

**Sources internes :** `sources/P11_Profils_Data.pdf` ; dossier `docs/` (documents 01 à 07).  
*Version révisée du document 05 — cadrage Fashion-Insta, Partie 2. Hypothèses de charges non validées, aucune dépense engagée.*
