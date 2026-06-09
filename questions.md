# Réponses TP7

## P1

### Q1
Une application peut marcher en local et échouer en production à cause de:
- Variables d'environnement manquantes ou différentes.
- Versions runtime différentes (Node, dépendances système, librairies natives).
- Réseau différent (DNS, firewall, reverse proxy, TLS, CORS).
- Ressources différentes (CPU/RAM/IO) et limites de timeout.

### Q2
Les risques d'un déploiement manuel sont:
- Erreurs humaines: oubli d'étape, mauvaise commande, mauvais serveur cible.
- Faible traçabilité: difficile de savoir qui a déployé quoi et quand.
- Temps de réaction plus long: rollback et diagnostic plus lents en incident.
- Non-répétabilité: la procédure varie selon la personne qui déploie.

### Q3
"Ça marche sur ma machine" signifie que l'environnement local d'un développeur diffère de celui des autres ou de la prod, donc le comportement n'est pas reproductible.
Docker corrige cela avec des images immuables qui encapsulent application + runtime + dépendances, puis des conteneurs reproductibles.

### Q4
Une panne d'une heure un vendredi soir peut causer:
- Perte directe de chiffre d'affaires.
- Hausse de l'abandon panier.
- Atteinte à l'image de marque et perte de confiance.
- Charge support plus élevée et coûts opérationnels additionnels.

## P2

### Q5
Une image est un modèle figé (comme une recette), un conteneur est son instance en exécution (comme le plat servi).

### Q6
Séparer frontend, backend et DB apporte:
- Isolation des pannes.
- Scalabilité indépendante.
- Déploiements indépendants.
- Surface d'attaque réduite et maintenance simplifiée.

### Q7
Exécuter en root est risqué car une compromission applicative peut devenir une compromission système (élévation de privilèges, accès hôte via mauvais montages, altération de fichiers critiques).

### Q8
Optimiser la taille des images réduit:
- Le temps de build/pull/deploy.
- Les coûts stockage/réseau.
- La surface d'attaque (moins de paquets, moins de CVE potentielles).

## P3

### Q9
Docker Compose centralise la définition multi-services, réseaux, volumes, dépendances et healthchecks dans un seul manifeste versionné, au lieu de commandes docker run longues et fragiles.

### Q10
Un volume Docker persiste les données hors cycle de vie du conteneur. Sans volume, les données MongoDB sont perdues au recréation/suppression du conteneur.

### Q11
Un réseau dédié isole les flux applicatifs, limite l'exposition latérale et améliore la lisibilité des communications inter-services.

## P4

### Q12
Bénéfices du reverse proxy:
- Point d'entrée unique.
- Masquage des services internes.
- Routage intelligent (/, /api).
- Ajout d'en-têtes de traçabilité et préparation au TLS, cache, rate limiting.

### Q13
Proxy (forward proxy): agit pour le client (ex: proxy d'entreprise pour filtrer l'accès web sortant).
Reverse proxy: agit pour les serveurs (ex: Nginx devant frontend/backend pour distribuer les requêtes).

## P5

### Q14
Jenkins est un orchestrateur d'automatisation CI/CD qui exécute des jobs/pipelines reproductibles, trace les exécutions et standardise les workflows.
Différence avec GitHub Actions: Jenkins est auto-hébergé et très extensible, GitHub Actions est intégré GitHub et plus managé.

### Q15
Un job Jenkins est une unité d'automatisation exécutable.
Freestyle: configuration majoritairement via UI.
Pipeline: workflow défini en code (Jenkinsfile), versionné et révisable.

### Q16
Jenkins est souvent préféré en grande entreprise pour:
- Contrôle on-prem et conformité.
- Écosystème plugins mature et intégrations legacy.
- Personnalisation fine des agents, nœuds, politiques et pipelines.

### Q17
Le volume /var/jenkins_home persiste:
- Jobs, plugins, credentials, historique, configuration.
Sans volume, toute recréation du conteneur réinitialise Jenkins.

## P6

### Q18
Pipeline as Code = pipeline décrit en code versionné avec l'application.
Avantages: revue via PR, historique Git, reproductibilité, auditabilité et synchronisation code/pipeline.

### Q19
Declarative: syntaxe structurée, lisible, garde-fous intégrés.
Scripted: flexible mais plus libre et plus complexe.
Pour débutant: Declarative, car plus simple et plus maintenable.

### Q20
Le bloc post exécute des actions de fin de pipeline ou de stage.
- always: s'exécute quoi qu'il arrive.
- success: seulement en cas de succès.
- failure: seulement en cas d'échec.

### Q21
Si un stage échoue, le pipeline s'arrête généralement et les stages suivants ne tournent pas (sauf logique explicite).
Dans Stage View, le stage en échec apparaît en rouge et les suivants sont ignorés/non exécutés.

## P7

### Q22
Deploy doit venir après Lint/Test pour empêcher la mise en production d'un code invalide, non conforme ou cassé.
Déployer sans validation augmente le risque d'incident client.

### Q23
Jenkins stocke les secrets dans Credentials Manager et les injecte à l'exécution (ex: withCredentials), avec masquage en logs.
Les écrire dans Jenkinsfile expose les secrets dans Git, logs et historique.

### Q24
Webhook GitHub vers Jenkins:
- git push sur GitHub.
- GitHub envoie une requête HTTP au endpoint webhook Jenkins.
- Jenkins valide l'événement et déclenche le job pipeline.
- Le pipeline checkout la nouvelle révision et exécute les stages.

### Q25
Gains du pipeline vs manuel:
- Temps: automatisation, moins d'attente et d'actions humaines.
- Fiabilité: étapes standardisées, mêmes contrôles à chaque run.
- Traçabilité: historique centralisé des builds, logs, artefacts, responsables.

### Q26
Blue Ocean est une interface Jenkins moderne pour visualiser pipelines et stages de façon plus claire que l'UI classique, surtout pour diagnostiquer rapidement les échecs.

## P8

### Q27
Versionner les images permet de tracer exactement ce qui est déployé, reproduire un état et rollback proprement.
Le tag latest seul est ambigu et mutable: il ne garantit pas la même image dans le temps.

### Q28
Risque du latest seul: un redéploiement tire une image différente sans changement explicite de version, pouvant introduire régression ou incompatibilité inattendue.

## P9

### Q29
Dev: développement rapide, faible contrainte.
Staging: préproduction proche de prod pour valider intégration/recette.
Production: environnement utilisateur final, disponibilité et sécurité prioritaires.

### Q30
Un rollback planifié réduit MTTR et impact business.
Scénario: version v1.1 introduit une fuite mémoire, latence élevée et erreurs 5xx; retour immédiat vers v1.0 stable pendant l'investigation.

### Q31
Démarche de diagnostic backend:
- Vérifier état des conteneurs et healthchecks.
- Lire logs backend et reverse proxy.
- Tester endpoints API via reverse proxy puis backend direct.
- Vérifier connectivité DB et erreurs de connexion.
- Corréler timestamp incident, métriques, et dernier changement déployé.
- Appliquer correctif ou rollback, puis revalider par health checks.
