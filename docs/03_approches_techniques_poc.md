# P11 — Comparaison et choix des approches IA pour le PoC

**Entreprise :** Fashion-Insta  
**Mission :** Réalisez le cadrage d'un projet IA — Partie 1  
**Statut :** Proposition à valider avec Alicia et le mentor  
**Catégorie pilote retenue :** Pantalons et jeans — 120 références (27 % du CA annuel indiqué)

## 1. Rappel de l'objectif du PoC

Vérifier la faisabilité d'une **recommandation de vêtements à partir d'une photographie** : une personne fournit une photo d'un pantalon ou d'un jean et le système classe les articles les plus proches de son style parmi les références du catalogue Fashion-Insta.

Le PoC porte sur le **moteur de recommandation**, et non sur l'application mobile entière. Il ne nécessite ni entraînement d'un modèle à partir de zéro, ni création de comptes clients, ni essayage virtuel.

> **Question de faisabilité :** une méthode d'IA peut-elle recommander, de manière reproductible et jugée pertinente par les métiers, des pantalons et jeans du catalogue à partir de photos représentatives des usages réels ?

## 2. Données d'entrée et de sortie communes aux deux approches

### Catalogue produit

- Jusqu'à **120 références** de pantalons et jeans, à confirmer comme disponibles et exploitables.
- **Plusieurs images par référence** lorsque disponibles (portées, non portées, angles différents).
- Métadonnées existantes : description, prix, tags de style ; idéalement identifiant produit, couleur et coupe si récupérables.
- Regroupement par **référence produit** afin de ne pas présenter plusieurs photos du même article comme des recommandations différentes.

### Photographies de test

- Hypothèse initiale : **30 à 50 photos** indépendantes du catalogue, prises dans des conditions proches des usages envisagés (éclairage, arrière-plan, article porté ou non).
- Le pantalon ou le jean doit être suffisamment identifiable ; pour un premier test, un **recadrage manuel** peut être accepté et documenté. La détection ou segmentation automatique du vêtement est hors périmètre initial.
- Images obtenues légalement, de préférence sans visage ni donnée personnelle inutile, avec consentement lorsque nécessaire.

### Sortie attendue

Pour chaque photo test, une **liste ordonnée de cinq références différentes** du catalogue (`Top 5`) jugées visuellement ou stylistiquement proches.

**Point de vigilance :** « même style » n'est pas équivalent à « même couleur ». Le besoin doit être précisé avec les métiers (coupe, matière, silhouette, formalité, couleur, etc.).

## 3. Approche A — Embeddings visuels et recherche par similarité

### Principe

Un modèle de représentation visuelle **préentraîné**, par exemple **CLIP / OpenCLIP** ou un modèle d'embeddings multimodaux compatible avec l'environnement Azure, convertit les images en vecteurs numériques (*embeddings*).

1. Préparer les images du catalogue et associer chaque image à son identifiant produit.
2. Générer puis stocker les embeddings des images du catalogue.
3. Générer l'embedding d'une photographie test avec **le même modèle** et le même prétraitement.
4. Calculer la **similarité cosinus** avec les embeddings du catalogue.
5. Agréger les scores par référence produit (par exemple, meilleur score parmi ses images), puis retourner cinq références **distinctes**.
6. Faire évaluer leur pertinence par des représentants des métiers.

### Prérequis et services envisageables

- **Azure Blob Storage** pour conserver les photographies autorisées et les métadonnées associées.
- **Azure Machine Learning** ou un environnement de calcul Azure autorisé pour exécuter le modèle préentraîné et comparer les vecteurs.
- **Azure AI Search (optionnel)** pour l'indexation et la recherche vectorielle à plus grande échelle. Pour **120 références**, un calcul de similarité sur une petite collection de vecteurs peut suffire ; imposer d'emblée un service d'indexation managé serait potentiellement disproportionné.

### Avantages

- Répond directement au besoin **image → recommandations de produits**.
- Pas d'entraînement initial sur des milliers d'exemples annotés.
- Classement automatique et reproductible à modèle et données identiques.
- Inférence et calcul de similarité potentiellement économiques à petite échelle.

### Limites et risques

- Le modèle peut privilégier le décor, la posture ou les couleurs plutôt que le **style vestimentaire**.
- Les photos personnelles et les photos commerciales présentent un **décalage de domaine**.
- Les scores cosinus ne constituent **pas** des probabilités de pertinence ni une preuve que les utilisateurs apprécieront les produits.
- Le classement est moins facile à expliquer en termes métier.

## 4. Approche B — Modèle vision-langage et comparaison d'attributs

### Principe

Un **modèle multimodal capable de traiter des images** (par exemple, un modèle vision disponible via Azure OpenAI / Microsoft Foundry) produit une description structurée du vêtement :

- Type d'article : pantalon ou jean.
- Coupe : slim, droite, large, etc., lorsque visible.
- Couleur dominante, motif, matière apparente.
- Style : casual chic, streetwear, professionnel moderne, selon un vocabulaire défini avec les métiers.

Les caractéristiques extraites de la photo test sont comparées aux descriptions, tags et/ou attributs extraits des images des produits. Une règle de classement pondérée ou une recherche hybride produit ensuite un **Top 5** de références différentes.

### Prérequis et services envisageables

- **Modèle vision-langage déployé dans Azure OpenAI / Microsoft Foundry**, sous réserve de sa disponibilité, des droits d'accès, de la région et des coûts.
- Métadonnées produit suffisamment cohérentes ; enrichissement de celles-ci si nécessaire.
- Vocabulaire d'attributs contrôlé, règles de comparaison définies et processus de vérification.
- **Azure AI Search (optionnel)** si une indexation hybride texte/vecteurs apporte une valeur réelle.

### Avantages

- Les recommandations sont plus faciles à **expliquer** grâce à des attributs comme la coupe, le style ou la couleur.
- Possibilité d'intégrer explicitement des règles métier.
- Approche intéressante si les tags et descriptions produits sont exploitables.

### Limites et risques

- Les descriptions générées peuvent être **inexactes, incohérentes ou incomplètes**.
- Les catégories de style demeurent subjectives et doivent être définies.
- L'analyse d'images peut augmenter le **coût et la latence** selon le modèle choisi et le nombre d'appels.
- Une description textuelle seule ne permet pas nécessairement d'effectuer correctement une recherche **image → image** ; un classement explicite reste indispensable.

## 5. Comparaison des deux approches

| Critère | A — Embeddings visuels | B — Vision-langage + attributs |
|---|---|---|
| Entrée | Photo | Photo |
| Représentation | Vecteur numérique | Attributs et description structurée |
| Classement | Similarité cosinus, puis regroupement par produit | Score de correspondance d'attributs ou recherche hybride |
| Données annotées pour entraîner | Non requises au départ | Non requises au départ |
| Explicabilité métier | Limitée | Plus forte |
| Dépendance à la qualité des tags | Faible | Potentiellement élevée |
| Risques | Sensibilité au contexte visuel ; décalage de domaine | Erreurs de description ; subjectivité des attributs |
| Compatibilité Azure | Calcul Azure et éventuellement Azure AI Search | Modèle vision via Azure OpenAI/Foundry, éventuellement Azure AI Search |
| Choix provisoire | **Approche de référence proposée** | **Comparateur / alternative** |

Les performances et les coûts réels **ne sont pas connus** : ils devront être vérifiés expérimentalement lors du PoC.

## 6. Protocole d'évaluation envisagé

Les **mêmes photographies de test** et les **mêmes références** seront utilisées pour comparer les deux approches.

### Construction d'une référence métier (*ground truth*)

- Demander à **deux évaluateurs métier** de juger les recommandations : *non pertinent*, *partiellement pertinent*, *pertinent*.
- Rédiger des consignes communes décrivant « même style » ; arbitrer les désaccords.
- Considérer comme « pertinent » un article classé pertinent selon la règle de consensus définie.
- Ne pas confondre une image test d'un produit catalogue avec une photo totalement indépendante : **éviter les doublons ou les photos quasi identiques** qui fausseraient les résultats.

### Mesures proposées

1. **Precision@5 (P@5)** : proportion moyenne d'articles jugés pertinents parmi les cinq recommandations de chaque photo test.
2. **Success@5** : pourcentage de photos test pour lesquelles au moins **un** des cinq articles est jugé pertinent.
3. **Analyse qualitative** : cas d'échec par coupe, couleur, arrière-plan, pose ou photographie portée/non portée.
4. **Temps de réponse et coût estimé par requête** : critères complémentaires pour anticiper l'industrialisation, sans en faire une exigence de production dans ce PoC.

### Critère de succès proposé — à faire valider par Alicia

- **P@5 moyen ≥ 60 %** (en moyenne, au moins trois recommandations pertinentes sur cinq) ;
- **Success@5 ≥ 80 %** ;
- et une validation qualitative de l'utilité métier, avec des résultats examinés sur plusieurs situations photographiques.

**Ces seuils sont des propositions de cadrage, non des objectifs déjà validés par Fashion-Insta.** Leur faisabilité dépend notamment du nombre de vêtements réellement pertinents disponibles dans le catalogue pour chaque photo. Ils pourront être ajustés **avant le lancement des essais**, en concertation avec les métiers. Un comparatif avec une méthode de référence simple (par exemple filtres de tags disponibles) serait utile pour démontrer la valeur ajoutée de l'IA.

## 7. Protection des données et limites du périmètre

- Préférer des photos de vêtements **sans visage identifiable** ; limiter et sécuriser le traitement des photos personnelles.
- Documenter l'origine, le consentement ou la base légale, les autorisations d'usage et les durées de conservation appropriées.
- Prévoir suppression des photos de test à l'issue de leur usage autorisé et restriction des accès.
- Ne pas créer d'application mobile, de gestion de compte, de garde-robe numérique ou d'essayage virtuel pendant le PoC.
- Ne pas exiger un entraînement lourd, une infrastructure de production ni un service destiné aux 400 000 utilisateurs projetés.

## 8. Décision technique provisoire

**Retenir l'approche A (embeddings visuels préentraînés + similarité cosinus) comme hypothèse principale de PoC**, et envisager l'approche B (vision-langage + comparaison d'attributs) comme comparateur si le calendrier, les ressources et les accès Azure le permettent.

Le choix final ne sera confirmé que par l'évaluation métier des résultats, leur reproductibilité et une appréciation du coût / effort.

## 9. Points à soumettre à Alicia ou au mentor

1. Que signifie précisément « un vêtement du même style » pour Fashion-Insta ?
2. Les 120 références disposent-elles d'images et de tags suffisamment exploitables ?
3. Qui fournit les 30 à 50 photos test représentatives ?
4. Les photos seront-elles prises **sur une personne** ou pourront-elles montrer **le vêtement seul** ?
5. Qui seront les deux évaluateurs métier et quelle grille de pertinence utiliseront-ils ?
6. Les seuils P@5 et Success@5 proposés sont-ils acceptables pour autoriser un PoC puis envisager la suite ?
7. Quels services Azure, quotas et budgets sont autorisés pour l'expérimentation ?

## 10. Sources techniques pour justifier les choix

- Microsoft Learn — [Multimodal search in Azure AI Search](https://learn.microsoft.com/en-us/azure/search/multimodal-search-overview) : recherches multimodales et embeddings directs image → vecteur.
- Microsoft Learn — [Vector Search Overview](https://learn.microsoft.com/en-us/azure/search/vector-search-overview) : principe de la recherche vectorielle et filtres.
- Microsoft Learn — [How to use vision-enabled chat models](https://learn.microsoft.com/azure/ai-services/openai/how-to/gpt-with-vision) : capacités d'analyse d'images des modèles multimodaux.

---

**Suite prévue :** étape 5 — formaliser les critères de succès, les profils à mobiliser, les jours-homme et un calendrier court et défendable devant le COMEX.
