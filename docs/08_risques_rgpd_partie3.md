# P11 — Partie 3 : risques RGPD et mesures de protection des données

**Entreprise :** Fashion-Insta  
**Livrable principal :** `livrables/P11_FashionInsta_Partie3_Etape1_RGPD.pptx`, diapositives 24 et 25  
**Statut :** propositions d'architecture et d'organisation **à valider avec le DPO**. Aucun contrôle n'est présenté comme déjà déployé.  
**Périmètre :** PoC initial et vision du service Data/IA cible, avec dépendances fonctionnelles de l'application mobile.

## 1. Objet de la nouvelle demande d'Alicia

Le COMEX demande que Fashion-Insta soit capable d'identifier et de prévenir les risques associés aux données personnelles, en particulier aux images des utilisateurs. **Exigence explicite : les données personnelles des utilisateurs ne doivent pas servir à entraîner les LLMs ou modèles de fondation d'un prestataire.**

Le PoC ne consiste toujours **pas à coder l'application** : cette étape formalise les risques, les mesures à déployer et les vérifications à faire réaliser par le DPO et le Tech Lead.

## 2. Inventaire des données personnelles et de leurs usages

| Catégorie | Exemples | Risque particulier | Traitement projeté |
|---|---|---|---|
| Compte et identifiants | Nom, email, identifiant client | Intrusion, corrélation entre sources | Stockage applicatif, contrôle d'accès et exercice des droits |
| Photos envoyées | Silhouette, visage éventuel, logement/arrière-plan, objets | Identification, divulgation de l'environnement privé | Prétraitement et inférence ; **conservation éphémère par défaut** hors garde-robe volontaire |
| Photos de garde-robe | Collection de vêtements portée ou non | Profil intime, historique personnel | Conservation seulement pour la fonction demandée, accès strict et suppression possible |
| Préférences et feedback | Styles, marques, avis, historiques | Profilage, déduction d'habitudes personnelles | Usage limité à la recommandation et à l'évaluation explicitée |
| Données techniques | IP, jetons, identifiants de session, logs | Traçage, réidentification | Logs minimisés, durée limitée, masquage des identifiants |
| Embeddings associés au compte | Vecteurs de la photo ou du profil, historique de requêtes | Corrélation ou réidentification indirecte | Traitement comme **données personnelles** tant qu'un lien reste possible |

Une photographie n'est **pas automatiquement** une donnée biométrique de catégorie particulière : l'enjeu dépend notamment de son emploi pour identifier de manière unique une personne. La présence éventuelle d'un visage nécessite néanmoins une prudence accrue.

## 3. Principes du RGPD et transposition aux risques Fashion-Insta

| Principe / obligation | Risque dans le projet | Mesure proposée |
|---|---|---|
| Licéité, loyauté, transparence | Upload de photos sans information ou base légale adéquate | Notices compréhensibles et base légale documentée **par finalité** ; le consentement n'est pas automatiquement la seule base possible |
| Limitation des finalités | Données réutilisées pour entraîner un modèle externe ou cibler les utilisateurs pour un objectif non annoncé | Interdiction de réemploi non autorisé ; séparation entre inférence, qualité du service et entraînement |
| Minimisation et exactitude | Transmission de visages, EXIF, localisation, arrière-plans, préférences inexactes | Retirer EXIF, recadrer vêtement et masquer quand c'est compatible ; rectification et contrôle des attributs |
| Limitation de conservation | Images, cache, index, sauvegardes conservés indéfiniment | Calendrier validé par le DPO ; durée courte par défaut pour requêtes ; gestion des sauvegardes et purge |
| Intégrité et confidentialité | Accès indu aux photos de garde-robe et profils | Chiffrement, RBAC / Entra ID, Key Vault, Private Link, cloisonnement et supervision |
| Droits des personnes et responsabilité | Effacement partiel, incapacité de documenter les traitements | Procédure d'accès, rectification, opposition le cas échéant, suppression/désinscription ; registre, preuves et vérification d'AIPD |

**Ne pas assimiler pseudonymisation et anonymisation :** remplacer un nom par un identifiant ou flouter un visage ne garantit pas l'anonymat, notamment lorsque les photos ou embeddings restent reliés à un compte. Le RGPD continue alors à s'appliquer.

## 4. Où placer les contrôles dans le System Design

```text
[Application et compte]
    | Information claire / finalité / choix utilisateurs
    v
[API Data/IA : Privacy Gateway]  <-- AVANT tout fournisseur de modèle
    | Contrôles de formats, retrait EXIF, recadrage vêtements,
    | masquage éventuel et suppression de contenus inutiles
    v
[Azure Machine Learning : inférence privée]
    | Modèle visuel préentraîné (p. ex. CLIP/OpenCLIP)
    | Images de clients exclues des jeux d'entraînement
    v
[Embedding transitoire + comparaison à l'index catalogue]
    | Top 5, pas d'image client dans les journaux techniques
    v
[Application : recommandations et feedback]

Flux séparé, sur demande explicite :
[Compte / garde-robe] -> [Blob/SQL à accès limité]
                                  -> [politique de conservation]
                                  -> [effacement coordonné des données liées]

Transverse : Entra ID/RBAC, chiffrement, réseau privé, Key Vault,
             logs minimisés, alertes, DPO et revue des sous-traitants.
```

La préparation **batch des embeddings du catalogue** reste inchangée, à partir des images commerciales Fashion-Insta. Les photos clients sont traitées **online pour l'inférence**, puis supprimées si aucune fonctionnalité légitime ne nécessite leur conservation.

### 4.1. Anonymiser les statistiques structurées sans perdre le service individuel

Pour répondre explicitement au critère d'évaluation sur **l'anonymisation des données structurées** : distinguer **les données opérationnelles nécessaires au compte** des **données d'analyse et de reporting**. Une photo, un embedding associé à un compte ou un identifiant pseudonymisé ne sont pas réputés anonymes.

| Flux et étape | Traitement recommandé | Ce qu'il reste à protéger |
|---|---|---|
| Compte, garde-robe, préférences pour le service | Conserver les identifiants indispensables dans le SI applicatif ; remplacer l'ID direct par un **identifiant pseudonyme** dans les tables de recommandation ; conserver séparément la clé de correspondance et limiter les accès | Données **personnelles** soumises au RGPD, même pseudonymisées |
| Journaux techniques et tests de robustesse | Ne pas écrire les emails/noms/photos en logs ; minimiser ou tronquer IP/identifiants ; durées courtes et accès restreint | Risque de lien indirect et de réidentification |
| Tableau de bord Business / pertinence agrégée | **Supprimer** nom, email, identifiant utilisateur et tout lien individuel ; produire comptes agrégés par période/catégorie/style, regrouper/supprimer les cellules rares et vérifier le risque de recoupement | Ne qualifier le résultat d'**anonyme** qu'après vérification de l'irréversibilité pratique (individualisation, corrélation, inférence) |
| Suppression d'un compte | Propager suppression ou dissociation dans Blob, SQL, index, cache, exports personnels et processus de sauvegarde | Un indicateur réellement anonymisé, non réidentifiable, n'a pas le même régime qu'une ligne de profil |

**Exemple concret :** pour mesurer l'usage du moteur sans garder l'identité du client, publier « 320 requêtes pour la catégorie jeans cette semaine » plutôt qu'une liste `(email, photo, modèle recommandé)` ; si un segment ne contient que très peu de personnes, le regrouper/supprimer avant diffusion. L'agrégation **n'est pas automatiquement une anonymisation** : le DPO doit documenter le risque de réidentification. Le service personnalisé, lui, ne peut pas supprimer tout lien avec le compte tout en gardant une garde-robe nominative ; sa pseudonymisation et ses droits d'accès sont la mesure appropriée.

**Emplacement dans le System Design :** à la sortie des tables SQL/événements de feedback, dédier un job d'agrégation/anonymisation **avant** l'entrepôt de reporting ou le tableau de bord, isolé du flux online ; aucun export brut de photos/IDs vers la BI. En production, vérifier la destruction des données de travail intermédiaires.

Référence : [CNIL — Identifier les données personnelles](https://www.cnil.fr/fr/identifier-les-donnees-personnelles) et [CNIL — L'anonymisation de données personnelles](https://www.cnil.fr/fr/technologies/lanonymisation-de-donnees-personnelles).

## 5. Garantie de non-entraînement et alternative aux API publiques

**Option privilégiée :** héberger un modèle de vision préentraîné (CLIP/OpenCLIP) dans Azure Machine Learning, dans un environnement réseau contrôlé. Configurer les flux pour **ne jamais incorporer les photos client, prompts, embeddings personnels ou feedback aux jeux d'entraînement** sans analyse juridique et décision spécifique. Des règles d'accès, de journalisation et de gouvernance doivent rendre cette interdiction vérifiable.

**Si une offre Azure Foundry / Azure OpenAI est envisagée** pour la vision-langage ou une extension générative, vérifier **l'offre et le type de déploiement exacts** : dans la documentation Microsoft sur les *Models sold by Azure*, les données clients ne sont pas utilisées pour entraîner des modèles de fondation sans autorisation/instruction. Cela **ne dispense pas** de contrôler les clauses contractuelles, la durée éventuelle de conservation pour la détection d'abus, les flux transfrontières, la région et les configurations.

**Alternative à une API tierce publique :** modèle OpenCLIP auto-hébergé sur un endpoint Azure ML privé, avec politique de sortie réseau restreinte et sans appel direct aux services externes depuis l'application. Ne pas promettre « zéro stockage fournisseur » sans vérification technique et contractuelle.

### 5.1. Protection spécifique de la génération d'images et des sorties

Une option d'essayage virtuel reposant sur un modèle de diffusion peut traiter **deux catégories de données** : photographie de l'utilisateur (entrée) et photographie artificielle de la personne (sortie), toutes deux potentiellement personnelles. Le masquage EXIF et le recadrage sont à appliquer **avant l'API ou le GPU**, mais ne suffisent pas à anonymiser les images. Le pipeline doit prévoir un contrôle des contenus, une limitation des quotas, une conservation éphémère par défaut, une suppression coordonnée (prompts/images/rendus/logs/caches), et l'interdiction du fine-tuning sur les photos clients sans décision autonome et licite.

**Option prioritaire :** modèle génératif hébergé dans Azure ML sur un réseau contrôlé ; **alternative conditionnelle :** offre Azure Foundry « Models sold by Azure », après vérification du contrat, de la région et des conditions applicables au modèle exact. Microsoft documente l'absence d'utilisation des prompts/réponses pour entraîner les modèles de fondation dans cette catégorie de services, mais les politiques de surveillance des abus et de rétention éventuelle doivent également être contrôlées. **Ne pas promettre une absence absolue de stockage sans preuve contractuelle et technique.**

Le chiffrage de l'option génération est **séparé** dans `06_dimensionnement_couts_azure_partie2.md` ; aucune fonctionnalité de génération n'est déclarée développée ou déjà conforme.

## 6. Mesures de gouvernance et validation DPO

1. **Cartographier les traitements** (compte, photos, garde-robe, préférences, avis, logs, embeddings) ; documenter les finalités, les acteurs responsable/sous-traitants et les bases légales.
2. **Décider avec le DPO** si le traitement est susceptible de présenter un risque élevé et exige une **AIPD avant production** (article 35 du RGPD). Ni l'usage de photos ni l'usage d'IA ne suffisent à conclure automatiquement à une obligation pour tous les cas.
3. **Contractualiser** avec les fournisseurs : avenants de traitement, absence de réemploi pour entraînement, localisation des traitements, sous-traitants ultérieurs, rétention et procédure d'incident.
4. **Mettre en place les droits des personnes** : retrouver un utilisateur dans Blob / SQL / index / cache ; assurer rectification, suppression, désinscription et propagation contrôlée aux sauvegardes selon leur cycle.
5. **Concevoir la sécurité** : chiffrement au repos et en transit, accès minimaux par rôle, secrets dans Key Vault, séparation des environnements, tests d'accès, contrôles anti-exfiltration, surveillance.
6. **Définir les durées** : pas de conservation illimitée ; conserver les photos de garde-robe uniquement pour la durée liée au service et décidée avec le DPO ; limiter les journaux aux signaux techniques utiles.
7. **Organiser la réponse aux incidents** et la gestion documentaire du RGPD, avec procédures de notification réglementaire si applicable.

## 7. Lien avec les étapes précédentes

- `01_analyse_besoins_metier.md` et `02_perimetre_poc.md` : fonctionnalités demandées, hors périmètre du PoC lorsque nécessaire.
- `03_approches_techniques_poc.md` : approche CLIP/OpenCLIP, **batch pour catalogue / online pour requête** ; pas de réentraînement sur les photos clients.
- `05_chiffrage_ressources_humaines_partie2.md` : tâches de Data Engineer, Tech Lead, MLOps et DPO consulté.
- `06_dimensionnement_couts_azure_partie2.md` : stockage, logs, contrôles de réseau, gestion du cycle de vie.
- `07_analyse_roi_rentabilite_partie2.md` : les nouveaux contrôles peuvent créer des charges ou coûts supplémentaires ; **les 92 960 € de lancement et 100 320 €/an restent des hypothèses existantes et ne constituent pas un chiffrage validé des mesures RGPD supplémentaires**.

Les deux nouvelles diapositives couvrent l'exigence pédagogique **sans modifier silencieusement le budget**. Les éventuels coûts additionnels (revue DPO, mécanismes de purge, isolation réseau, stockage, exploitation, AIPD) devront être chiffrés et arbitrés lors de la revue d'architecture si l'on souhaite établir un budget complet.

## 8. Décisions à obtenir avant le COMEX / déploiement

| Décision | Responsable proposé | Statut |
|---|---|---|
| Base légale, information et éventuelles options de consentement | Alicia / DPO | À valider |
| Photos conservées dans la garde-robe : oui/non, finalité et durée | Produit / DPO | À valider |
| Entraînement sur les photos utilisateurs interdit par défaut | Tech Lead / DPO / fournisseur | À contractualiser et contrôler |
| AIPD nécessaire ou non | DPO / responsable de traitement | À apprécier avant mise en œuvre |
| Région Azure, modèle exact, conservation/monitoring du fournisseur | Tech Lead / sécurité / DPO | À confirmer |
| Effacement dans SQL, Blob, index, cache, sauvegardes | Data Engineer / MLOps | À concevoir et tester |
| Impact financier et planning des contrôles supplémentaires | Alicia / Tech Lead / Finance | À estimer |

## Sources officielles consultées

- [CNIL — Les six grands principes du RGPD](https://www.cnil.fr/fr/comprendre-le-rgpd/les-six-grands-principes-du-rgpd).
- [CNIL — Analyse d'impact relative à la protection des données](https://www.cnil.fr/fr/definition/analyse-dimpact-aipd).
- [CNIL — Différence entre anonymisation et pseudonymisation](https://www.cnil.fr/fr/comment-prevenir-les-risques-et-organiser-la-securite-de-vos-donnees).
- [CNIL — Périmètre des recommandations IA](https://www.cnil.fr/fr/quel-est-le-perimetre-des-fiches-pratiques-sur-lia).
- [Microsoft Learn — Data privacy pour les modèles vendus par Azure](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy?view=foundry-classic).
- [Microsoft Learn — Isolation réseau Azure Machine Learning](https://learn.microsoft.com/en-us/azure/machine-learning/concept-secure-online-endpoint?view=azureml-api-2).

*Document de cadrage pédagogique ; validation juridique et opérationnelle nécessaire avant toute utilisation réelle de données de clients.*
