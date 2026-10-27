# P11 — Partie 2 : projection des gains, coûts cumulés et ROI Data/IA

**Version harmonisée — documents 03, 05 et 06 mis à jour ; contrôle arithmétique effectué le 25 octobre 2026.**

> **Statut : simulation financière de cadrage, non validée par la direction financière.**  
> **Périmètre : coûts du projet Data & IA uniquement.** Le développement mobile, le parcours d’achat, la publicité, l'acquisition clients et l’essayage virtuel génératif ne sont pas inclus dans le budget.  
> **Documents de référence :** `docs/03_approches_techniques_poc.md`, `docs/05_chiffrage_ressources_humaines_partie2.md` et `docs/06_dimensionnement_couts_azure_partie2.md` (hypothèses d’architecture, charges et dépenses Azure).  
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

**Contexte d'audience (non converti en gains) :** Alicia mentionne **950 000 visiteurs uniques du site web par an**, **11 magasins** et **400 000 utilisateurs de l'application au moins une fois par an à terme**, avec plusieurs dizaines de milliers d'utilisateurs particulièrement actifs. Ces données éclairent le potentiel et le trafic, mais elles ne permettent **pas** de déduire un taux d'adoption, une conversion ni des revenus additionnels distincts des **+14 % / +4 %** de l'étude Marketing. Les intégrer séparément aux gains du modèle risquerait de créer un **double comptage**.

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

Ce modèle raisonne en **années de projet de 52 semaines**, non en années civiles ; il simplifie les décalages de facturation et les éventuels effets saisonniers. Il suppose que le Run stabilisé commence après les 17 semaines initiales et que ces deux plages de coûts ne se chevauchent pas.

**Vérification des coûts A1 (sans double comptage) :** `92 960 + 100 320 × (52 − 17) / 52 = 160 483,08 €`. Les dépenses de lancement couvrent déjà le PoC, le MVP **et le Run initial** : la charge récurrente s'applique seulement aux 35 semaines restantes d'A1. Cette convention doit être réexaminée si la facturation Azure ou les affectations humaines se chevauchent dans la réalité.

### 3.3. Hypothèse de réalisation progressive des ventes

Le sujet fournit **une cible à 24 mois**, mais **pas de courbe annuelle de montée en charge**. À titre d'étude, nous retenons :

| Année | Part moyenne du gain annuel cible de 936 000 € utilisée dans le calcul |
|---|---:|
| A1 (construction puis lancement progressif) | **25 %** |
| A2 (adoption et accélération) | **75 %** |
| A3 à A5 (régime cible stabilisé) | **100 %** |

Ces pourcentages ne sont **pas** fournis par le Marketing. La cible de 100 % est supposée atteinte **à la fin d'A2** : sa moyenne sur A2 est donc inférieure à 100 %. Les **25 % d'A1 constituent une moyenne annualisée** : ils ne signifient **pas** que l'application produit des gains pendant les 17 semaines de construction. Pour atteindre 25 % du gain annuel théorique sur A1 **uniquement pendant les 35 semaines postérieures à S17**, il faudrait une réalisation moyenne d'environ `25 % × 52 / 35 = 37,1 %` du rythme-cible pendant cette période. Cela constitue une **hypothèse à tester**, non une prévision marketing.

Les **75 % d'A2** sont une moyenne sur cette année. L'atteinte de 100 % du rythme annuel-cible **à la fin d'A2** est supposée ; l'année A3 est la première année pour laquelle nous utilisons 100 % sur l'année entière. La montée en charge réelle et les gains des autres années devront être validés.

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

**Prudence sur la date :** nous ne disposons que d'hypothèses *annuelles*. Le tableau établit un croisement **entre la fin d'A1 et la fin d'A2** ; annoncer un mois précis sans courbe mensuelle de revenus et de coûts serait injustifié. Le ROI cumulé affiché est un **ROI simple, non actualisé, limité aux coûts Data/IA**.

**Contrôle du modèle :** les valeurs exactes derrière les arrondis sont : coûts cumulés A1 `160 483,08 €` et A2 `260 803,08 €`, marge cumulée A2 `280 800,00 €`, solde exact A2 `+19 996,92 €`, d'où `ROI A2 = 19 996,92 / 260 803,08 × 100 = 7,67 %`, affiché **+7,7 %**.

## 5. Graphique des gains et coûts cumulés

![Projection des gains et coûts cumulés du projet Data/IA](07_graphique_roi_cumule_partie2.png)

**Fichier à conserver à côté de ce Markdown :** `docs/07_graphique_roi_cumule_partie2.png`. Le graphique joint a été contrôlé par rapport aux montants exacts du tableau (A1–A5). Ce graphique représente la **marge contributive cumulée**, et non le chiffre d’affaires brut, face au **coût Data/IA cumulé**. Il permet d'identifier visuellement le croisement vers A2 selon les hypothèses retenues. Le point représenté entre A1 et A2 correspond uniquement à une interpolation graphique indicative.

## 6. Analyse de sensibilité : l'effet de la marge

Hypothèses de trafic, de montée en charge et de coût **identiques** à la projection centrale ; seule la marge contributive varie :

| Marge contributive supposée | Solde cumulé A1 | Solde cumulé A2 | Solde cumulé A3 | Première fin d’année rentable |
|---|---:|---:|---:|---|
| 20 % | −113 683 € | −73 603 € | +13 277 € | A3 |
| 30 % | −90 283 € | +19 997 € | +200 477 € | A2 |
| 40 % | −66 883 € | +113 597 € | +387 677 € | A2 |

**Interprétation :** avec **20 %** de marge, le point de rentabilité annuel se décale vers **A3** ; avec **30 % ou 40 %**, il apparaît avant la fin d'**A2**, si toutes les autres hypothèses se vérifient. Cette sensibilité montre pourquoi nous devons obtenir une hypothèse de marge réelle avant de défendre une date devant le COMEX.

### 6.1. Marge minimale pour un solde cumulé positif à la fin d'A2

La marge contributive minimale nécessaire **dans le scénario de gains prévu** pour couvrir les dépenses cumulées des deux premières années est :

`marge_seuil_A2 = 260 803,08 / [936 000 × (25 % + 75 %)] = 27,86 %`.

Ainsi, **30 % se situe à seulement 2,14 points de pourcentage au-dessus du seuil**. La conclusion « rentable dès A2 » est donc sensible aux hypothèses : il ne s'agit pas d'un résultat robuste à tout scénario.

### 6.2. Sensibilité à l'atteinte des objectifs commerciaux

**Variante illustratrice** : toutes les années ne réalisent que **80 %** des gains du scénario central, en conservant la marge de 30 % et les mêmes dépenses. La marge cumulée fin A2 devient `280 800 × 80 % = 224 640 €`, inférieure aux coûts cumulés `260 803 €` ; le solde fin A2 passe à **−36 163 €**. Il redevient positif fin A3 dans cette variante (`561 600 × 80 % − 361 123 = +88 157 €`).

**Interprétation :** une adoption ou une conversion inférieure à la prévision suffit à repousser la rentabilité **à A3**, même sans hausse de prix Azure.

### 6.3. Sensibilité à l'enveloppe Azure en production

Le document 06 présente un scénario de simulation « haut niveau de service / forte utilisation » de **4 000 €/mois** de dépenses Azure, contre **1 800 €/mois** dans le scénario central. Les coûts humains Run de 6 560 €/mois et l'enveloppe initiale de 92 960 € demeurent inchangés dans ce **stress test isolé**.

| Hypothèse Azure en Run | Coût Run Data/IA annuel | Coût cumulé fin A2 | Solde fin A2 (marge 30 %) | Première fin d'année rentable |
|---|---:|---:|---:|---|
| Central : 1 800 €/mois | 100 320 € | 260 803 € | +19 997 € | A2 |
| Stress test : 4 000 €/mois | 126 720 € | 304 972 € | **−24 172 €** | A3 |

Dans le stress test, les coûts A1 sont `92 960 + 126 720 × 35 / 52 = 178 252,31 €`, puis A2 ajoute `126 720 €`. Les montants Azure restent des **provisions hypothétiques non vérifiées** ; le stress test n'est pas un devis ni une prévision de dépense certaine.

### 6.4. Effet du choix batch / online

Le scénario financier suppose une architecture **hybride** : jobs batch pour l'actualisation des embeddings du catalogue et service online pour les nouvelles photos des clients. Les exemples comparant 16 h à 730 h de capacité réservée dans le document 06 **ne sont pas des économies comptabilisables** dans le ROI ; ils n'ont donc pas été soustraits une seconde fois aux dépenses Azure. La preuve économique exigera mesures d'exécution, choix des SKU et coûts effectifs du service.

## 7. Scénarios, hypothèses de scalabilité et risques non inclus

1. **Taux de réalisation des gains :** si l'adoption, la conversion ou la croissance sont inférieures aux projections, le croisement peut être repoussé, voire ne jamais survenir.
2. **Attribution commerciale :** la hausse des ventes peut être influencée par d'autres initiatives ; le marketing doit confirmer la part réellement attribuable à l'application.
3. **Infrastructure à l'usage :** le scénario central du document 06 suppose 400 000 utilisateurs par an et cinq demandes de recommandation annuelles par utilisateur, soit 2 millions de requêtes/an, **non observées**. Les pics, le p95, la disponibilité et la politique d'autoscaling peuvent renchérir l'inférence online, la recherche vectorielle et les transferts d'images.
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

## 9. Harmonisation avec le PowerPoint COMEX existant

La présentation de référence du dépôt est désormais :

`livrables/P11_FashionInsta_Partie2_Cadrage_COMEX_Final.pptx`

Le PowerPoint de travail précédent n'est plus le fichier à enrichir. Dans sa version précédemment établie, il contient déjà les diapositives financières relatives aux **coûts**, à la **comparaison batch / online** et au **ROI** (slides 17–19, annexe slide 20). Avant de remettre une nouvelle version au COMEX :

1. Contrôler que les diapositives utilisent bien **92 960 € de lancement**, **8 360 €/mois** en Run, **936 000 € de CA annuel additionnel cible** et **30 % de marge supposée**.
2. Indiquer que l'équilibre en A2 est **conditionnel** et sensible au taux de marge, à l'adoption et au niveau de dépense Azure ; une mention synthétique du stress test peut être ajoutée aux notes orateur ou à une diapositive.
3. S'assurer que le graphique représente **la marge contributive cumulée** et **les coûts Data/IA cumulés**, non les ventes brutes face aux seuls coûts Azure.
4. Conserver dans le System Design les **deux flux complémentaires** : catalogue **batch** et photos utilisateur **online**, ainsi que les mécanismes de scalabilité et de RGPD.
5. Conserver les diapositives PoC, les responsabilités des profils, la timeline de 17 semaines et les limites du périmètre financier.

**Cette révision modifie uniquement le document 07 et contrôle son graphique ; elle ne modifie pas automatiquement le fichier `.pptx` du projet local.**

## 10. Synthèse à défendre en soutenance

Dans le scénario central **illustratif**, le programme Data/IA nécessite environ **92 960 € de coûts de lancement** et **100 320 € par an** de Run stabilisé. Les objectifs marketing correspondent à **936 000 € de CA annuel additionnel à l'horizon de 24 mois**, qui génèrent **280 800 € de marge contributive annuelle en régime cible** *si* la marge retenue est de 30 %. En tenant compte d'une montée en puissance hypothétique, **les gains cumulés dépasseraient les coûts Data/IA cumulés au cours d'A2**. Ce scénario **ne suffit pas à démontrer la rentabilité de l'application complète** : il reste à valider la marge, la trajectoire de ventes, le chiffrage Azure et les dépenses exclues.

---

**Traçabilité :** calculs fondés sur la synthèse métier d'Alicia, le PDF des profils Data et les documents `03`, `05` et `06` du P11 ; toutes les valeurs qui ne proviennent pas du sujet sont identifiées comme des hypothèses de simulation. **Aucun budget ni scénario de marge n'a été validé par Fashion-Insta.**
