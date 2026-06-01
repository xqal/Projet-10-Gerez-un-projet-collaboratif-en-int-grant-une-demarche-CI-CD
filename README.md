
# Documentation CI/CD — BobApp

## 1. Étapes du workflow CI/CD

### 1.1. CI — Intégration Continue

Le workflow CI est exécuté automatiquement à chaque push ou pull request sur la branche `main`. La pipeline est divisée en deux tâches (Jobs) indépendantes qui s'exécutent en parallèle.

#### Job Backend (Java / Spring Boot)

Ce job valide la conformité, la sécurité et la qualité du code backend.

| Étape | Objectif |
|---|---|
| Checkout du code | Récupérer le code source depuis le dépôt GitHub |
| Configuration Java 11 | Configurer l'environnement d'exécution avec Temurin JDK 11 |
| Exécution des tests & Analyse Sonar | Compiler le back, exécuter les tests et envoyer l'analyse sur SonarCloud |
| Archivage du rapport JaCoCo | Sauvegarder le rapport de couverture en artefact sur GitHub Actions |

#### Job Frontend (Angular)

Ce job installe, teste et analyse l'interface utilisateur de l'application.


| Étape | Objectif |
|---|---|
| Checkout du code | Récupérer le code source depuis le dépôt GitHub |
| Configuration de Node.js | Configurer l'environnement d'exécution Angular |
| Installation des dépendances | Installer les dépendances Angular |
| Exécution des tests | Exécuter les tests Karma/Jasmine et générer le rapport lcov |
| Analyse Sonar | Envoyer les résultats d'analyse sur SonarCloud |
| Archivage du rapport de couverture | Sauvegarder le rapport de couverture en artefact sur GitHub Actions |

### 1.2. CD — Déploiement Continu

Le workflow CD s'exécute automatiquement après la validation du CI sur la branche `main`. La pipeline est divisée en deux tâches (Jobs).

#### Job Backend (back-docker)
Ce job construit et publie l’image de conteneur pour le serveur Spring Boot.

| Étape | Objectif |
|---|---|
| Checkout du code | Récupérer le code source depuis le dépôt GitHub |
| Connexion à Docker Hub | S'authentifier sur Docker Hub via token sécurisé |
| Build et Push de l'image backend | Construire l'image Docker du backend et la pousser sur Docker Hub |

#### Job Frontend (front-docker)

Ce job construit et publie l'image de conteneur pour l'application Angular.


| Étape | Objectif |
|---|---|
| Checkout du code | Récupérer le code source depuis le dépôt GitHub |
| Connexion à Docker Hub | S'authentifier sur Docker Hub via token sécurisé |
| Build et Push de l'image frontend | Construire l'image Docker du frontend et la pousser sur Docker Hub |

## 2. KPIs proposés

Pour suivre la qualité du code, quatre KPIs ont été définis. Ils permettent de détecter rapidement les régressions, les bugs et les failles de sécurité introduits à chaque changement de code.


| KPI | Seuil | Justification |
|---|---|---|
| Couverture de code | >80% | Indique le pourcentage de code exécuté par les tests — >80% est le seuil standard de l'industrie |
| Reliability | A | Indique l'absence de bugs dans le code — la note A signifie 0 bug détecté |
| Security | A | Indique l'absence de vulnérabilités de sécurité dans le code — la note A signifie 0 faille détectée |
| New Blocker Issues | 0 | Indique qu'aucun nouveau bug bloquant n'a été introduit par le dernier changement de code |

## 3. Analyse des métriques

### Backend (Java / Spring Boot)


| Métrique | Valeur | Évaluation |
|---|---|---|
| Security | A — 0 issues | Conforme au KPI |
| Reliability | D — 1 issue | En dessous du KPI |
| Maintainability | A — 7 issues | Acceptable |
| Hotspots Reviewed | E — 0% | À traiter |
| Coverage | 38.8% | En dessous du seuil de 80% |


Le backend présente plusieurs points d'attention. La fiabilité est en note D avec 1 bug détecté, les hotspots de sécurité n'ont pas encore été revus et la couverture de 38.8% est bien en dessous du seuil fixé à 80%.

### Frontend (Angular)

| Métrique | Valeur | Évaluation |
|---|---|---|
| Security | B — 2 issues | En dessous du KPI |
| Reliability | A — 0 issues | Conforme au KPI |
| Maintainability | A — 6 issues | Acceptable |
| Hotspots Reviewed | A — 100% | Excellent |
| Coverage | 28.6% | En dessous du seuil de 80% |

Le frontend est globalement plus satisfaisant avec une fiabilité et une maintenabilité en note A et tous les hotspots de sécurité ont été revus. La sécurité est en note B avec 2 problèmes à corriger et la couverture de 28.6% reste en dessous du seuil fixé à 80%.

## 4. Retours utilisateurs et priorisation des problèmes

### Avis

L'application obtient une note de **2/5** — les retours sont majoritairement négatifs :

> "Je mets une étoile car je ne peux pas en mettre zéro ! Impossible de poster une suggestion de blague, le bouton tourne et fait planter mon navigateur !"

> "#BobApp j'ai remonté un bug sur le post de vidéo il y a deux semaines et il est encore présent ! Les devs vous faites quoi ????"

> "Ca fait une semaine que je ne reçois plus rien, j'ai envoyé un email il y a 5 jours mais toujours pas de nouvelles..."

> "J'ai supprimé ce site de mes favoris ce matin, dommage, vraiment dommage."


### Problèmes identifiés

-   Le bouton de soumission de suggestion de blague fait planter le navigateur.
-   Un bug sur le post de vidéo est présent depuis au moins 2 semaines.
-   Le support ne répond pas aux utilisateurs.


### Priorisation globale

En croisant les retours utilisateurs avec les métriques issues de SonarCloud, les tâches ont été priorisées ainsi :

 **Priorité 1 — Bugs bloquants et sécurité**

-   **Crash lors de la soumission d'une suggestion (Avis)** : "Impossible de poster une suggestion de blague, le bouton tourne et fait planter mon navigateur !"
-   **Bug de fiabilité (Sonar)** : Le backend présente 1 bug de fiabilité (note D) qui doit être corrigé immédiatement pour stabiliser l'application.
-   **Vulnérabilités de sécurité (Sonar)** : Le frontend présente 2 vulnérabilités de sécurité (note B) à corriger pour restaurer un niveau de sécurité optimal (note A).

**Action corrective** : Investiguer et corriger le crash lors de la soumission d'une suggestion de blague, corriger le bug de fiabilité sur le backend (note D), appliquer les correctifs de sécurité sur le frontend (note B) et ajouter des tests associés pour prévenir toute réapparition.


**Priorité 2 — Bugs fonctionnels**

-   **Bug sur le post de vidéo (Avis)** : "#BobApp j'ai remonté un bug sur le post de vidéo il y a deux semaines et il est encore présent !"
dz
**Action corrective** : Identifier et corriger le bug sur le post de vidéo, puis ajouter un test de non-régression.


**Priorité 3 — Qualité et support**

-   **Absence de réponse du support (Avis)** : "Ca fait une semaine que je ne reçois plus rien, j'ai envoyé un email il y a 5 jours mais toujours pas de nouvelles..."
-   **Hotspots de sécurité non revus (Sonar)** : Les hotspots de sécurité du backend n'ont pas encore été revus (note E).

**Action corrective** : Mettre en place un suivi des demandes utilisateurs et procéder à la revue manuelle des hotspots de sécurité.


**Priorité 4 — Amélioration continue**

-   **Couverture de code insuffisante (Sonar)** : La couverture est de 38.8% sur le backend et 28.6% côté frontend, bien en dessous du seuil fixé à 80%.
-   **Maintenabilité (Sonar)** : 7 code smells sur le backend et 6 sur le frontend.

**Action corrective** : Augmenter la couverture de code en ajoutant des tests unitaires et d'intégration pour atteindre le seuil minimum et réduire les code smells identifiés.

---

# BobApp

Clone project:

> git clone XXXXX

## Front-end 

Go inside folder the front folder:

> cd front

Install dependencies:

> npm install

Launch Front-end:

> npm run start;

### Docker

Build the container:

> docker build -t bobapp-front .  

Start the container:

> docker run -p 8080:8080 --name bobapp-front -d bobapp-front

## Back-end

Go inside folder the back folder:

> cd back

Install dependencies:

> mvn clean install

Launch Back-end:

>  mvn spring-boot:run

Launch the tests:

> mvn clean install

### Docker

Build the container:

> docker build -t bobapp-back .  

Start the container:

> docker run -p 8080:8080 --name bobapp-back -d bobapp-back 
