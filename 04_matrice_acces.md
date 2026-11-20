---
audience: equipe
maturity: brouillon
maturity_note: "À revalider après validation de l'inventaire des données, des habilitations et des tests de sécurité."
owner: "Walid Makboul"
ia: "ChatGPT utilisé pour structurer et rédiger la matrice d'accès, proposer les permissions et les mécanismes de sécurité. Les décisions restent à valider par l'auteur et le groupe."
---

# Matrice des accès — Noria Logistique

## 1. Objectif

Cette politique définit les droits d'accès au pipeline
décisionnel RH de Noria Logistique.

Elle repose sur les principes suivants :

- Moindre privilège.
- Refus des accès par défaut.
- Séparation des responsabilités.
- Contrôle des accès par rôle (RBAC).
- Restriction des accès par site (RLS).
- Traçabilité des opérations.
- Révision régulière des habilitations.

## 2. Identification des rôles

| Identifiant du rôle | Acteur | Responsabilité |
|---|---|---|
| role_rh | Direction RH | Pilotage des effectifs et des absences |
| role_rh_paie | RH habilitées | Pilotage de la masse salariale |
| role_finance | Direction financière | Suivi budgétaire et salarial agrégé |
| role_direction | Direction générale | Consultation des indicateurs consolidés |
| role_manager | Responsable de site | Consultation des indicateurs de son site |
| role_data | Data Engineer | Maintenance et contrôle qualité |
| role_dba | Administrateur PostgreSQL | Administration technique |
| role_etl_ingestion | Service ETL | Chargement des données Raw |
| role_etl_integration | Service ETL | Intégration des données dans le DW |
| role_etl_reporting | Service ETL | Alimentation des Data Marts |
| role_rssi | RSSI | Audit et supervision de sécurité |
| role_dpo | DPO | Contrôle de conformité |

## 3. Matrice des accès métier

Légende :

- L : lecture des données autorisées.
- A : lecture des indicateurs agrégés.
- S : lecture limitée au site autorisé.
- T : consultation des vues techniques.
- X : accès interdit.

| Ressource | RH | RH Paie | Finance | DG | Manager | Data Engineer |
|---|---|---|---|---|---|---|
| raw_rh | X | X | X | X | X | X |
| dw_rh - données individuelles | X | X | X | X | X | X |
| dm_pilotage_rh | A | X | X | A consolidé | X | T |
| dm_masse_salariale | X | A | A | A consolidé | X | T |
| dm_management | X | X | X | X | S | T |
| Salaires individuels | X | X | X | X | X | X |
| Motifs médicaux | X | X | X | X | X | X |
| Modification des données métier | X | X | X | X | X | X |
| Export des données | X | X | X | X | X | X |

Les utilisateurs métiers consultent les indicateurs
autorisés dans Power BI, sans connexion SQL directe.

Les données nécessaires aux opérations de paie restent
accessibles dans les applications sources selon leurs
propres politiques d'habilitation.

## 4. Matrice des accès techniques

| Ressource | ETL Ingestion | ETL Integration | ETL Reporting |
|---|---|---|---|
| raw_rh | INSERT | SELECT | Interdit |
| dw_rh | Interdit | SELECT, INSERT, UPDATE | SELECT |
| dm_pilotage_rh | Interdit | Interdit | SELECT, INSERT, UPDATE |
| dm_masse_salariale | Interdit | Interdit | SELECT, INSERT, UPDATE |
| dm_management | Interdit | Interdit | SELECT, INSERT, UPDATE |

Les permissions sont limitées aux tables et colonnes
nécessaires à chaque traitement.

Les comptes techniques sont distincts des comptes humains.
Les opérations de purge utilisent une habilitation dédiée.

## 5. Justification des restrictions

### Direction RH

Accès aux indicateurs sociaux agrégés nécessaires au
pilotage des effectifs, du turnover et de l'absentéisme.

### Direction financière

Accès aux indicateurs agrégés de masse salariale et de
budget, sans accès aux absences individuelles.

### Direction générale

Accès uniquement aux vues consolidées nécessaires
aux décisions stratégiques.

### Managers

Accès limité aux indicateurs collectifs du site dont
ils sont responsables.

Un manager de Lille ne peut pas consulter les
indicateurs des sites de Lyon ou Nantes.

### Équipe Data

Accès aux métadonnées, métriques de qualité et vues
techniques nécessaires à la maintenance.

L'accès exceptionnel aux données individuelles doit
être justifié, approuvé, temporaire et journalisé.

### Administrateur PostgreSQL

Administration de l'infrastructure et des permissions.

Les privilèges élevés sont réservés aux interventions
nécessaires, avec journalisation et contrôle renforcé.

### DPO et RSSI

Accès aux éléments nécessaires aux missions de contrôle,
sans droit permanent de consultation de l'ensemble
des données individuelles.

## 6. Mécanismes de sécurité

### RBAC

Les permissions sont attribuées selon le rôle
professionnel de chaque utilisateur.

### RLS

Les managers disposent d'un filtrage dynamique selon
leur site d'affectation :

- Lyon.
- Lille.
- Nantes.

Le filtrage est contrôlé dans les couches d'exposition
PostgreSQL et Power BI selon le mode de connexion retenu.

### Restriction des colonnes

Les vues métier n'exposent pas les identifiants
individuels, les rémunérations individuelles ni les
informations médicales.

### Protection contre la réidentification

Un seuil minimal de diffusion de 10 salariés est
proposé pour les indicateurs sensibles.

Ce seuil reste à valider par le groupe et le DPO fictif.

Les filtres, totaux et comparaisons entre périodes
doivent également être contrôlés afin d'éviter
la réidentification par recoupement.

### Authentification

- Comptes utilisateurs individuels.
- Authentification multifacteur.
- Gestion centralisée des identités.
- Interdiction du partage de comptes.
- Protection et rotation des secrets techniques.

## 7. Cycle de vie des habilitations

| Situation | Action |
|---|---|
| Arrivée | Attribution des droits après validation |
| Changement de poste | Suppression des anciens droits et attribution des nouveaux |
| Changement de site | Modification du périmètre autorisé |
| Accès exceptionnel | Autorisation temporaire, motivée et journalisée |
| Départ | Révocation des accès au moment du départ |
| Compte compromis | Suspension immédiate et investigation |

La demande d'accès est formulée par le responsable
hiérarchique, validée par le responsable des données,
puis appliquée par l'administrateur habilité.

## 8. Règle de revue des accès

Une revue des habilitations est réalisée tous les trois
mois, ainsi qu'après chaque changement organisationnel
ou incident de sécurité significatif.

La revue comprend :

- Vérification des comptes actifs.
- Vérification des rôles attribués.
- Vérification des périmètres géographiques.
- Suppression des permissions inutilisées ou injustifiées.
- Vérification des accès temporaires.
- Contrôle des comptes techniques.
- Conservation des preuves de validation.

La Direction RH valide les besoins métier.
Le RSSI contrôle la conformité des habilitations.
L'administrateur applique les modifications.

## 9. Journalisation et audit

Les opérations suivantes doivent être tracées :

- Connexions réussies et échouées.
- Modifications des permissions.
- Consultations privilégiées.
- Tentatives d'accès non autorisées détectables.
- Exports autorisés exceptionnellement.
- Utilisation des comptes techniques.

Les journaux contiennent uniquement les informations
nécessaires à l'audit, sans recopier les données RH.

## 10. Décisions et options écartées

| Option écartée | Justification |
|---|---|
| Accès direct au DW pour les managers | Risque de divulgation |
| Compte partagé entre plusieurs utilisateurs | Absence de traçabilité individuelle |
| Accès permanent aux données Raw pour les Data Engineers | Privilèges excessifs |
| Export libre depuis Power BI | Risque de diffusion non contrôlée |
| Accès à tous les sites pour les managers | Non-respect du moindre privilège |

## 11. Validation

Les permissions doivent être vérifiées à travers
des scénarios de tests autorisés et interdits.

Les scripts SQL et le schéma de contrôle des accès
seront associés à ce document.

Auteur principal : Walid Makboul
