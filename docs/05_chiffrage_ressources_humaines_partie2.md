# P11 — Partie 2 : estimation des charges et coûts humains Data/IA

> **Statut : hypothèses de cadrage à valider par Alicia, le Tech Lead et le COMEX.**  
> **Périmètre : uniquement l'équipe Data/IA ; hors développement de l'application mobile, marketing et achat en ligne.**  
> **Source des tarifs :** `sources/P11_Profils_Data.pdf` (document fourni par Fashion-Insta).

## 1. Objectif

Estimer la charge de réalisation en **jours-homme (JH)** et son coût salarial interne pour le programme de recommandation vestimentaire de Fashion-Insta, en cohérence avec la timeline précédemment proposée :

- **PoC :** 3 semaines, sur 120 références de pantalons et jeans.
- **MVP Data/IA :** 10 semaines, avec industrialisation du moteur et des flux de données.
- **Run initial :** 4 semaines de stabilisation après mise en service.
- **Run récurrent :** exploitation continue au-delà des 17 semaines du planning de lancement.

La disponibilité effective des collaborateurs, les dates précises et la couverture fonctionnelle du MVP restent à confirmer.

## 2. Tarifs officiels des profils internes

| Profil | Effectif disponible | Coût salarial journalier |
|---|---:|---:|
| Data Scientist | 3 | 350 € / JH |
| Data Engineer | 2 | 370 € / JH |
| Tech Lead Data | 1 | 400 € / JH |
| MLOps Engineer | 1 | 360 € / JH |

Ces tarifs sont ceux du document de Fashion-Insta. Ce sont des coûts internes d'allocation de ressources et non nécessairement des décaissements nouveaux.

## 3. Répartition des charges par phase

| Profil | PoC (3 sem.) | MVP (10 sem.) | Run initial (4 sem.) | Total JH |
|---|---:|---:|---:|---:|
| Data Scientist | 16 | 75 | 10 | **101** |
| Data Engineer | 6 | 55 | 8 | **69** |
| Tech Lead Data | 4 | 20 | 4 | **28** |
| MLOps Engineer | 4 | 30 | 14 | **48** |
| **Total** | **30** | **180** | **36** | **246** |

> La phase PoC conserve l'estimation antérieure de **30 JH**. Le détail par profil est une nouvelle hypothèse à valider ; ce n'est pas un relevé d'activité.

### Rôle des profils pendant chaque phase

**PoC**
- **Data Scientist :** sélectionner/comparer les approches, définir les métriques, évaluer la pertinence des recommandations.
- **Data Engineer :** obtenir et fiabiliser les données des 120 références et le lot de photos de test.
- **Tech Lead Data :** valider l'architecture et les choix techniques.
- **MLOps Engineer :** préparer un environnement de démonstration reproductible et la traçabilité des expérimentations.

**MVP Data/IA**
- **Data Scientist :** améliorer le classement visuel, intégrer les préférences et retours utilisateurs, organiser l'évaluation.
- **Data Engineer :** industrialiser l'ingestion du catalogue, les pipelines et les règles de qualité/conservation des données.
- **Tech Lead Data :** superviser les interfaces avec le logiciel mobile existant, la sécurité et la cohérence de l'architecture.
- **MLOps Engineer :** automatiser les déploiements, mettre en place la supervision, le suivi des modèles et le contrôle des coûts.

**Run initial**
- **Data Scientist :** analyser la qualité des recommandations et les éventuelles dérives.
- **Data Engineer :** stabiliser l'alimentation du catalogue et les traitements de données.
- **Tech Lead Data :** arbitrer et traiter les risques d'architecture.
- **MLOps Engineer :** gérer la disponibilité, les alertes, les performances et les procédures d'exploitation.

L'essayage virtuel génératif et les fonctionnalités avancées devront être **étudiés puis priorisés** : leur industrialisation complète ne doit pas être supposée incluse dans ces charges sans spécification et estimation spécifiques.

## 4. Coûts salariaux estimés

Formule : **coût de phase = somme(JH du profil × coût journalier du profil)**.

| Profil | PoC | MVP | Run initial | Total |
|---|---:|---:|---:|---:|
| Data Scientist | 5 600 € | 26 250 € | 3 500 € | **35 350 €** |
| Data Engineer | 2 220 € | 20 350 € | 2 960 € | **25 530 €** |
| Tech Lead Data | 1 600 € | 8 000 € | 1 600 € | **11 200 €** |
| MLOps Engineer | 1 440 € | 10 800 € | 5 040 € | **17 280 €** |
| **Total** | **10 860 €** | **65 400 €** | **13 100 €** | **89 360 €** |

**Budget humain de lancement prévisionnel : 89 360 €**, répartis sur 17 semaines. Ce montant comprend le Run initial de stabilisation, mais **ne comprend pas** les coûts des services cloud Azure, les outils tiers, ni les coûts récurrents du Run au-delà de la semaine 17.

## 5. Hypothèse de Run récurrent après la semaine 17

| Profil | Charge mensuelle proposée | Coût mensuel |
|---|---:|---:|
| Data Scientist | 4 JH / mois | 1 400 € |
| Data Engineer | 4 JH / mois | 1 480 € |
| Tech Lead Data | 2 JH / mois | 800 € |
| MLOps Engineer | 8 JH / mois | 2 880 € |
| **Total** | **18 JH / mois** | **6 560 € / mois** |

**Ordre de grandeur annuel : 78 720 €/an** de charge salariale Run, hors Azure et autres charges. Cette hypothèse s'applique **après les 4 semaines de Run initial** pour éviter de compter deux fois la même période.

## 6. Capacité et faisabilité du planning

À titre indicatif, chaque collaborateur peut représenter au plus environ 5 JH par semaine s'il est affecté à plein temps. Nos hypothèses restent sous les capacités maximales de l'équipe :

- **PoC (3 semaines)** : 30 JH répartis sur 7 personnes ; charge partielle.
- **MVP (10 semaines)** : 180 JH répartis sur 7 personnes ; charge soutenue, compatible en théorie avec les effectifs disponibles.
- **Run initial (4 semaines)** : 36 JH répartis sur 7 personnes ; charge de stabilisation.

La faisabilité réelle dépend de la disponibilité de chaque personne, de ses autres projets et des dépendances externes.

## 7. Coûts technologiques à chiffrer dans l'étape suivante

Les services techniques envisagés comprennent les postes suivants, sans attribuer de prix non vérifiés :

- **Stockage des images et données** : Azure Blob Storage, stockage opérationnel et éventuelles sauvegardes.
- **Ingestion et orchestration** : pipelines d'intégration du catalogue et événements utilisateurs.
- **Calcul ML** : extraction d'embeddings, expérimentation, réentraînement éventuel et traitements batch.
- **Serving et recherche** : API de recommandation, index vectoriel et base de données de caractéristiques.
- **Génération d'images** : option pour l'essayage virtuel, sous réserve de faisabilité et de coûts.
- **Exploitation** : métriques, journaux, alertes, gestion des secrets, réseau et sécurité.

### Données indispensables pour un coût Azure défendable

- Région d'hébergement Azure et environnements (test, préproduction, production).
- **Nombre de recommandations par utilisateur actif** et fréquence d'usage.
- Nombre et résolution des photographies envoyées par requête.
- Nombre d'articles, d'images par article et fréquence de mise à jour du catalogue.
- Cible de latence, disponibilité, stockage et conservation des images.
- Fréquence d'utilisation éventuelle de l'essayage virtuel génératif.

L'estimation marketing de **400 000 utilisateurs utilisant l'application au moins une fois par an** ne suffit pas, à elle seule, à déterminer les requêtes par seconde ou le coût cloud. Des scénarios d'usage devront être établis.

## 8. Distinction entre coûts ponctuels et récurrents

- **Coûts ponctuels de lancement :** 89 360 € de charge interne estimée + dépenses cloud de construction/expérimentation à chiffrer.
- **Coûts récurrents après stabilisation :** 6 560 € par mois de charge interne Run (hypothèse) + Azure et outils facturés à l'usage ou à la capacité.
- **Non inclus :** développement mobile, création des comptes, e-commerce, publicité et autres coûts métier hors périmètre Data/IA.

## 9. Points à valider avant intégration définitive au COMEX

1. Valider la disponibilité des 7 collaborateurs aux périodes prévues.
2. Faire approuver le découpage des 246 JH et le périmètre fonctionnel du MVP.
3. Confirmer que les coûts salariaux sont la bonne convention pour l'analyse économique.
4. Chiffrer Azure à partir de scénarios de charge explicites et de tarifs régionaux actuels.
5. Pour le ROI, distinguer **chiffre d'affaires additionnel**, **marge générée** et **coûts complets** ; ne pas assimiler directement une hausse de CA à un bénéfice.

---

*Document de travail — Partie 2 du P11 Fashion-Insta. Estimations non contractuelles, non validées par Fashion-Insta.*
