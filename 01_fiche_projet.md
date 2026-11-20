---
audience: equipe
maturity: brouillon
maturity_note: "À revalider après validation du périmètre, des technologies et de l'hébergement par le groupe et l'enseignant."
owner: "À compléter — auteur principal"
ia: "IA générative utilisée pour proposer la structure et la rédaction à partir du sujet et du schéma définis par le groupe ; contenu à relire et valider."
---

# Fiche projet — Sécurisation et gouvernance d'un pipeline de données RH

**Formation :** M2 Data Engineer — 2026-2027  
**Module :** Souveraineté et sécurité de la donnée  
**Sujet :** n° 8 — Un tableau de bord RH  
**Date :** 20 novembre 2026  
**Auteur principal :** À compléter  
**Membres du groupe :** À compléter

## 1. Présentation du projet

### 1.1 Contexte

Une entreprise française fictive de **500 salariés** souhaite centraliser certaines données issues de ses logiciels RH afin de créer un tableau de bord destiné à la direction et aux responsables des ressources humaines.

Elle utilise deux systèmes complémentaires :

- **Un SIRH SaaS**, qui gère les salariés, les services, les contrats et les absences.
- **Un logiciel de paie**, qui gère les rémunérations, les primes et les cotisations.

Ces sources contiennent des données personnelles et confidentielles. Le projet doit donc concilier exploitation des informations RH, confidentialité et sécurité des traitements.

### 1.2 Objectifs métier

Le tableau de bord permettra de suivre la **masse salariale**, **l'absentéisme** et les **effectifs**. Les indicateurs envisagés sont :

| Indicateur | Finalité |
| --- | --- |
| Effectif total | Suivre le nombre de salariés actifs à une date donnée |
| Taux d'absentéisme | Suivre les absences sur une période, selon une définition métier commune |
| Masse salariale brute | Suivre l'évolution des rémunérations brutes |
| Salaire brut moyen | Analyser les rémunérations moyennes sur un périmètre autorisé |
| Taux de turnover | Mesurer les départs rapportés à l'effectif moyen |

L'affichage par service sera conditionné aux règles de confidentialité, notamment pour les groupes de faible effectif.

### 1.3 Objectifs de sécurité et de gouvernance

Le projet doit :

- limiter les accès aux données selon les fonctions et les besoins réels ;
- protéger les informations RH et salariales pendant leur transfert, leur stockage et leur consultation ;
- tracer les accès et les opérations sensibles ;
- définir des durées de conservation et des règles de purge ;
- limiter les risques de réidentification dans les indicateurs agrégés ;
- justifier les choix d'hébergement et de fournisseurs dans une perspective de souveraineté ;
- respecter les principes applicables du RGPD.

## 2. Périmètre du projet

**Inclus :** conception d'un pipeline allant des deux logiciels sources jusqu'au tableau de bord Power BI, avec ingestion, stockage brut, intégration, préparation des indicateurs et règles de sécurité associées.

**Exclus :** développement des logiciels sources, gestion effective de la paie, mise en production du pipeline, traitement de données réelles et refonte complète du système d'information RH.

Il s'agit d'une **étude de conception fictive** : aucune installation ni collecte de données réelles n'est prévue.

## 3. Description du pipeline de données

**Flux général :**

`SIRH SaaS + Logiciel de paie → Ingestion → Zone Raw PostgreSQL → ETL/ELT d'intégration → Data Warehouse PostgreSQL → ETL/ELT métier → Data Mart RH PostgreSQL → Power BI`

### 3.1 Sources de données

Le **SIRH SaaS** fournit les identifiants des salariés, leurs services, types de contrats, dates d'entrée et de sortie et données d'absence nécessaires aux indicateurs.

Le **logiciel de paie** fournit, pour chaque période, les données de rémunération nécessaires au calcul de la masse salariale et des moyennes. Le rapprochement entre les deux systèmes utilise un identifiant salarié commun ou une correspondance contrôlée.

Le périmètre exclut les détails médicaux et les informations qui ne sont pas nécessaires aux indicateurs définis.

### 3.2 Ingestion et zone Raw — `raw_rh`

Les données nécessaires sont extraites via des API ou des échanges de fichiers sécurisés, puis chargées dans le schéma PostgreSQL **`raw_rh`**. Cette zone conserve les données reçues **sans transformation métier**, avec des contrôles techniques d'intégrité et de traçabilité.

L'accès est réservé aux comptes techniques habilités. La durée de conservation doit rester limitée et justifiée.

### 3.3 Data Warehouse — `dw_rh`

Un traitement ETL/ELT assure les contrôles de qualité, l'harmonisation des formats, le rapprochement entre les sources et l'intégration des données dans le schéma PostgreSQL **`dw_rh`**.

Le Data Warehouse contient les données RH détaillées nécessaires aux analyses, avec un historique limité aux besoins justifiés et un accès strictement encadré.

### 3.4 Data Mart RH — `dm_rh`

Un second traitement ETL/ELT prépare les données de restitution : jointures, calculs des KPI et agrégations par période ou service.

Le schéma PostgreSQL **`dm_rh`** contient principalement des résultats agrégés, adaptés aux besoins du reporting. Des règles limitant les risques de réidentification sont prévues, notamment pour les petits groupes.

### 3.5 Tableau de bord — Power BI

Power BI exploite les informations préparées dans le Data Mart. Les utilisateurs accèdent aux seules vues correspondant à leurs habilitations. Le tableau de bord n'interroge pas directement les tables brutes.

**Schéma d'architecture :** joindre ici l'image `schemas/pipeline_rh.png` issue du diagramme du groupe.

## 4. Technologies envisagées

| Composant | Technologie proposée | Fonction |
| --- | --- | --- |
| Source RH | SIRH SaaS fictif | Salariés, contrats, services, absences |
| Source de paie | Logiciel de paie fictif | Salaires, primes et cotisations |
| Ingestion | API / Python | Extraction et chargement |
| Orchestration | Apache Airflow (envisagé) | Planification et supervision |
| Zone Raw | PostgreSQL — `raw_rh` | Données extraites |
| Data Warehouse | PostgreSQL — `dw_rh` | Données intégrées et structurées |
| Transformations métier | SQL / Python | Calculs, jointures et agrégations |
| Data Mart | PostgreSQL — `dm_rh` | Données de reporting |
| Visualisation | Power BI | Tableau de bord RH |

Les trois schémas PostgreSQL sont distincts **logiquement**, sans être nécessairement isolés physiquement. Des droits séparés seront définis. Les technologies restent à valider par le groupe.

## 5. Acteurs concernés

| Acteur | Intervention dans le projet |
| --- | --- |
| Direction générale | Consultation des indicateurs globaux |
| Direction RH | Définition des besoins et validation des indicateurs |
| Responsable paie | Validation des informations de rémunération |
| Managers | Consultation d'indicateurs limités à leur périmètre |
| Équipe Data | Conception, intégration et qualité du pipeline |
| Administrateur PostgreSQL | Gestion technique, sauvegardes et habilitations |
| RSSI | Définition et suivi des exigences de sécurité |
| DPO | Conseil et suivi des enjeux de protection des données |

## 6. Principaux enjeux de sécurité

Le projet présente plusieurs points de vigilance :

- **Confidentialité :** éviter qu'un manager ou un utilisateur non habilité consulte des données salariales individuelles.
- **Moindre privilège :** séparer les droits entre la Raw, le Data Warehouse, le Data Mart et Power BI.
- **Protection des absences :** éviter de collecter des motifs médicaux détaillés et encadrer les informations susceptibles de révéler l'état de santé.
- **Réidentification :** empêcher qu'un tableau agrégé révèle une rémunération individuelle, notamment pour un service de très petite taille.
- **Traçabilité et intégrité :** journaliser les accès sensibles et contrôler les traitements.
- **Conservation :** définir une purge adaptée à chaque couche et aux finalités du traitement.

Les mesures détaillées seront précisées dans l'inventaire, la matrice des accès, la politique de purge et l'analyse de risques.

## 7. Hébergement envisagé

Le SIRH est proposé en mode **SaaS**, ce qui satisfait le besoin d'intégrer un service cloud au pipeline. Les trois couches PostgreSQL pourraient être hébergées sur une infrastructure localisée dans l'Union européenne, sous réserve de validation.

La future étude de souveraineté devra vérifier la localisation effective des données et sauvegardes, les accès des prestataires, les transferts éventuels hors EEE, les garanties contractuelles, le chiffrement et la réversibilité. **Un hébergement dans l'UE ne garantit pas, à lui seul, la souveraineté.**
