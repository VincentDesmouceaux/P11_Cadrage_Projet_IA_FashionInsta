# P11 — Partie 2 : projection des gains, coûts cumulés et ROI Data/IA

> **Statut : simulation financière de cadrage, non validée par la direction financière.**  
> **Périmètre : coûts du projet Data & IA uniquement.** Le développement mobile, le parcours d’achat, la publicité, l'acquisition clients et l’essayage virtuel génératif ne sont pas inclus dans le budget.  
> **Documents de référence :** `docs/05_chiffrage_ressources_humaines_partie2.md` et `docs/06_dimensionnement_couts_azure_partie2.md` (hypothèses de dépenses Azure).  
> **Point de vigilance :** une rentabilité calculée sur le seul coût Data/IA n'est **pas** le ROI complet de l'application mobile.

## 1. Objectif demandé par Alicia

Présenter au COMEX une projection sur plusieurs années afin d'identifier **si et quand la marge générée par l'accroissement des ventes dépasse les coûts Data/IA cumulés**. Le graphique doit représenter séparément les gains et les coûts cumulés. Ce document n'est pas une mesure de revenus effectivement obtenus.

## 2. Données métier — chiffres issus de l'expression de besoins

| Source de ventes | CA annuel de référence | Objectif marketing à 24 mois | CA annuel additionnel à l'horizon cible |
|---|---:|---:|---:|
| Site e-commerce | 5 200 000 € | +14 % | **728 000 €** |
| Magasins physiques | 5 200 000 € | +4 % | **208 000 €** |
| **Total** | **10 400 000 €** | — | **936 000 €** |

**Interprétation :** les **936 000 €** représentent une *augmentation de chiffre d’affaires annualisée à l’horizon de 24 mois*, selon l'étude marketing. Ce n'est ni un encaissement immédiat, ni un bénéfice comptable, ni une performance démontrée du moteur IA. Les prévisions supposent également que les hausses web et magasin sont additionnelles, non comptées deux fois et attribuables au projet ; ces points restent à vérifier.

## 3. Paramètres retenus pour la simulation centrale

### 3.1. Coûts Data/IA (issus des documents 05 et 06)

| Poste | Montant | Nature / statut |
|---|---:|---|
| Coûts salariaux PoC + MVP + Run initial, semaines 1 à 17 | **89 360 €** | Jours-homme × coûts journaliers fournis par Fashion-Insta ; plan de charge à confirmer |
| Coûts Azure de lancement sur ces 17 semaines | **3 600 €** | *Enveloppe de simulation*, pas un devis Azure |
| **Coût total du lancement** | **92 960 €** | Coût de lancement alloué au projet |
| Coût humain du Run stabilisé | **6 560 €/mois** | Allocation de 18 JH par mois ; hypothèse |
| Coût Azure du Run stabilisé | **1 800 €/mois** | *Enveloppe de simulation* non validée par Microsoft |
| **Coût total Run stabilisé** | **8 360 €/mois**, soit **100 320 €/an** | Hors périmètre non budgété |

L'architecture **hybride batch / online** est incluse dans l'enveloppe Azure centrale : actualisation des embeddings catalogue en batch et inférence interactive online sur les photos client. Ne **pas déduire** du coût de Run les économies théoriques de comparaison « 16 h contre 730 h » du document 06 : cet exemple illustre un mécanisme de facturation, et n'est pas une économie mesurée ni un budget alternatif.

### 3.2. Calendrier de prise en compte des coûts

- **A1 :** investissement de lancement de **92 960 €** pendant les 17 premières semaines, incluant déjà les 4 semaines de Run initial.
- **A1 après la semaine 17 :** **35 semaines** de Run stabilisé, proratisées pour ne pas compter deux fois la stabilisation : `100 320 × 35 / 52 = 67 523,08 €`.
- **A1 total :** `92 960 + 67 523,08 = 160 483,08 €`.
- **A2 et suivantes :** **100 320 € par année complète** dans le scénario central, à charges et capacité égales.

Ce modèle raisonne en années de projet de 52 semaines, non en années civiles ; il simplifie les décalages de facturation et les éventuels effets saisonniers. Il suppose que le Run stabilisé commence après les 17 semaines initiales et que ces deux plages de coûts ne se chevauchent pas.

### 3.3. Hypothèse de réalisation progressive des ventes

Le sujet fournit **une cible à 24 mois**, mais **pas de courbe annuelle de montée en charge**. À titre d'étude, nous retenons :

| Année | Part moyenne du gain annuel cible de 936 000 € utilisée dans le calcul |
|---|---:|
| A1 (construction puis lancement progressif) | **25 %** |
| A2 (adoption et accélération) | **75 %** |
| A3 à A5 (régime cible stabilisé) | **100 %** |

Ces pourcentages ne sont **pas** fournis par le Marketing. La cible de 100 % est supposée atteinte **à la fin d'A2** : sa moyenne sur A2 est donc inférieure à 100 %. Les 25 % d'A1 sont une équivalence sur l'année entière, malgré l'absence de production pendant les 17 premières semaines. La montée en charge réelle et les gains des autres années devront être validés.

### 3.4. Hypothèse de marge contributive

**Hypothèse centrale : 30 % de marge contributive sur le chiffre d'affaires additionnel.** Cette marge n'est pas communiquée par Alicia et devra être validée par le service finance. Nous testons aussi 20 % et 40 %.

Formules :

- **CA additionnel annuel Aₙ** = 936 000 € × taux de réalisation Aₙ.
- **Marge générée Aₙ** = CA additionnel Aₙ × marge contributive (30 % dans le scénario central).
- **Solde cumulé Aₙ** = Σ(marge générée de A1 à Aₙ) − Σ(coûts Data/IA de A1 à Aₙ).
- **ROI Data/IA cumulé Aₙ** = solde cumulé Aₙ / coûts Data/IA cumulés Aₙ × 100.

La marge contributive est ici supposée calculée **avant l'imputation des coûts Data/IA budgétés**, pour éviter de les soustraire deux fois. Les coûts et revenus absents du périmètre doivent faire l'objet d'un chiffrage complémentaire avant une décision d'investissement sur **l'application complète**.

## 4. Projection centrale sur cinq ans

| Année | Taux de réalisation* | CA supplémentaire | Marge additionnelle (30 %) | Coûts Data/IA de l’année | Gains cumulés | Coûts cumulés | Solde cumulé | ROI cumulé |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| A1 | 25 % | 234 000 € | 70 200 € | 160 483 € | 70 200 € | 160 483 € | **−90 283 €** | **-56,3 %** |
| A2 | 75 % | 702 000 € | 210 600 € | 100 320 € | 280 800 € | 260 803 € | **+19 997 €** | **+7,7 %** |
| A3 | 100 % | 936 000 € | 280 800 € | 100 320 € | 561 600 € | 361 123 € | **+200 477 €** | **+55,5 %** |
| A4 | 100 % | 936 000 € | 280 800 € | 100 320 € | 842 400 € | 461 443 € | **+380 957 €** | **+82,6 %** |
| A5 | 100 % | 936 000 € | 280 800 € | 100 320 € | 1 123 200 € | 561 763 € | **+561 437 €** | **+99,9 %** |

\* *Part du gain annualisé cible reconnue pendant l'année entière, selon notre hypothèse et non selon une prévision marketing détaillée.* Les montants sont arrondis à l'euro dans le tableau ; les calculs utilisent des valeurs non arrondies.

### Lecture des résultats

- **Fin A1 :** la marge additionnelle cumulée est de **70 200 €** contre **160 483 €** de coûts ; solde **−90 283 €**.
- **Fin A2 :** la marge cumulée atteint **280 800 €** et les coûts cumulés **260 803 €** ; solde **+19 997 €**. Le seuil de rentabilité **serait franchi au cours d'A2 dans ce scénario**.
- **Fin A3 :** solde net cumulé **+200 477 €** ; ROI partiel Data/IA cumulé **55,5 %**.

**Prudence sur la date :** nous ne disposons que d'hypothèses *annuelles*. Le tableau établit un croisement **entre la fin d'A1 et la fin d'A2** ; annoncer un mois précis sans courbe mensuelle de revenus et de coûts serait injustifié.

## 5. Graphique des gains et coûts cumulés

![Projection des gains et coûts cumulés du projet Data/IA](07_graphique_roi_cumule_partie2.png)

**Fichier à conserver à côté de ce Markdown :** `docs/07_graphique_roi_cumule_partie2.png`. Ce graphique représente la **marge contributive cumulée**, et non le chiffre d’affaires brut, face au **coût Data/IA cumulé**. Il permet d'identifier visuellement le croisement vers A2 selon les hypothèses retenues. Le point représenté entre A1 et A2 correspond uniquement à une interpolation graphique indicative.

## 6. Analyse de sensibilité : l'effet de la marge

Hypothèses de trafic, de montée en charge et de coût **identiques** à la projection centrale ; seule la marge contributive varie :

| Marge contributive supposée | Solde cumulé A1 | Solde cumulé A2 | Solde cumulé A3 | Première fin d’année rentable |
|---|---:|---:|---:|---|
| 20 % | −113 683 € | −73 603 € | +13 277 € | A3 |
| 30 % | −90 283 € | +19 997 € | +200 477 € | A2 |
| 40 % | −66 883 € | +113 597 € | +387 677 € | A2 |

**Interprétation :** avec **20 %** de marge, le point de rentabilité annuel se décale vers **A3** ; avec **30 % ou 40 %**, il apparaît avant la fin d'**A2**, si toutes les autres hypothèses se vérifient. Cette sensibilité montre pourquoi nous devons obtenir une hypothèse de marge réelle avant de défendre une date devant le COMEX.

## 7. Scénarios et risques non inclus

1. **Taux de réalisation des gains :** si l'adoption, la conversion ou la croissance sont inférieures aux projections, le croisement peut être repoussé, voire ne jamais survenir.
2. **Attribution commerciale :** la hausse des ventes peut être influencée par d'autres initiatives ; le marketing doit confirmer la part réellement attribuable à l'application.
3. **Infrastructure à l'usage :** l'augmentation du trafic peut modifier les coûts d'inférence online, le dimensionnement Azure AI Search et les transferts d'images.
4. **Fonctionnalités génératives :** essayage virtuel, variantes de couleurs et génération d'images peuvent nécessiter du GPU et un chiffrage propre ; **ces coûts ne sont pas inclus dans les résultats**.
5. **Périmètre financier partiel :** coût du développement mobile, maintenance frontend, support et marketing sont hors périmètre demandé ; le ROI total du produit serait différent.
6. **Nature des coûts internes :** les jours-homme salariés mobilisés constituent un **coût économique d'allocation** même s'ils ne correspondent pas à une sortie de trésorerie supplémentaire.
7. **Horizon et devis :** le prix réel des composants Azure (région, SKU, horaires, instances) n'est pas encore validé ; aucun ROI chiffré définitif ne peut être affirmé.
8. **Valeur du temps :** absence d'actualisation financière, de fiscalité et d'inflation ; ce modèle représente un **ROI cumulé simple**, pas une VAN ou un TRI.

## 8. Validations nécessaires avant une présentation définitive au COMEX

| À confirmer | Porteur / contributeur proposé | Incidence |
|---|---|---|
| Progression annuelle de +14 % web et +4 % magasin jusqu'à 24 mois | Marketing / Alicia | Niveau et calendrier des bénéfices |
| Marge contributive pertinente sur les ventes supplémentaires | Finance / Alicia | Solde et date de rentabilité |
| Répartition des jours-homme et disponibilité des sept profils | Tech Lead / responsable Data | Coûts humains et calendrier |
| Prix Azure par région / SKU, activité batch et online, pics de trafic | Tech Lead / MLOps | Coût cloud et scalabilité |
| Calendrier réel de mise en service et disponibilité des données | Produit / Data Engineer | Proratisation des coûts et des gains |
| Besoins de génération/essayage virtuel et fréquence d'usage | Produit / Data Scientist | Coûts et périmètre fonctionnel |
| Attribution des ventes à la recommandation et éventuelle cannibalisation | Marketing / Finance | Bénéfice réellement attribuable à l'IA |

## 9. Intégration attendue dans le PowerPoint existant

Après validation des hypothèses, enrichir **la même présentation** `livrables/P11_FashionInsta_Partie2_Cadrage_Travail.pptx` avec :

1. Une diapositive « Hypothèses économiques » : sources des +14 % web, +4 % magasins et scénario de marge.
2. Une diapositive « Coûts de construction et de Run » : 92 960 € au lancement puis 8 360 €/mois dans le scénario central.
3. Une diapositive « Projection et seuil de rentabilité » : graphique des gains / coûts cumulés et date **conditionnelle** du croisement.
4. Éventuellement, une diapositive « Risques et sensibilités » résumant les hypothèses à confirmer par Finance, Marketing et Azure.

Ne pas supprimer les diapositives déjà produites pour le PoC, le System Design, les responsabilités et la timeline. Les pourcentages de marge et de réalisation doivent être libellés **« hypothèses de simulation »**.

## 10. Synthèse à défendre en soutenance

Dans le scénario central **illustratif**, le programme Data/IA nécessite environ **92 960 € de coûts de lancement** et **100 320 € par an** de Run stabilisé. Les objectifs marketing correspondent à **936 000 € de CA annuel additionnel à l'horizon de 24 mois**, qui génèrent **280 800 € de marge contributive annuelle en régime cible** *si* la marge retenue est de 30 %. En tenant compte d'une montée en puissance hypothétique, **les gains cumulés dépasseraient les coûts Data/IA cumulés au cours d'A2**. Ce scénario **ne suffit pas à démontrer la rentabilité de l'application complète** : il reste à valider la marge, la trajectoire de ventes, le chiffrage Azure et les dépenses exclues.

---

**Traçabilité :** calculs fondés sur la synthèse métier d'Alicia, le PDF des profils Data et les documents `05` et `06` du P11 ; toutes les valeurs qui ne proviennent pas du sujet sont identifiées comme des hypothèses de simulation. **Aucun budget ni scénario de marge n'a été validé par Fashion-Insta.**
