---
audience: equipe
maturity: brouillon
maturity_note: "À revalider après accord du groupe et validation de l'enseignant sur le périmètre, les données, les habilitations et les choix d'hébergement."
owner: "À compléter — auteur principal"
ia: "ChatGPT utilisé pour proposer un contexte d'entreprise fictive, structurer l'architecture et rédiger cette première version. Les hypothèses, choix et règles restent à vérifier et à valider par le groupe."
---

# Fiche projet — Sécurisation et gouvernance d'un pipeline décisionnel RH

**Sujet :** n° 8 — Tableau de bord RH  
**Formation :** M2 Data Engineer — Souveraineté et sécurité de la donnée (2026-2027)  
**Date :** 20 novembre 2026  
**Organisation :** **Noria Logistique** (entreprise entièrement fictive)  
**Auteur principal :** À compléter  
**Groupe :** À compléter

## 1. Contexte de l'entreprise

**Noria Logistique** est une entreprise française fictive de logistique et de préparation de commandes pour des clients professionnels. Elle compte **500 salariés** répartis entre un siège administratif à Lyon et trois entrepôts situés à Lyon, Lille et Nantes. Ses équipes travaillent selon des organisations horaires différentes, avec des pics d'activité saisonniers.

*Hypothèses pédagogiques inventées pour ce projet* : 360 salariés travaillent dans la préparation et les opérations d'entrepôt, 70 dans la coordination du transport et des expéditions, 45 dans les fonctions support (RH, finance, informatique) et 25 dans l'encadrement. Ces effectifs sont illustratifs et ne constituent pas des données réelles.

Les directions RH, financière et générale doivent piloter les effectifs, les absences et les dépenses de personnel. Les responsables d'entrepôt doivent suivre les ressources disponibles pour organiser l'activité. Aujourd'hui, les informations sont dispersées entre quatre applications et sont rapprochées manuellement dans des fichiers de reporting, ce qui crée des risques d'erreur, de partage excessif et de diffusion de données confidentielles.

L'entreprise souhaite donc **concevoir un pipeline analytique RH unique**, depuis les sources jusqu'aux tableaux de bord métiers. Le projet de cours ne mettra aucun outil en production et n'utilisera **aucune donnée réelle**.

## 2. Problématique et objectifs

**Problématique :** comment centraliser et fiabiliser des données RH issues de plusieurs systèmes pour alimenter des tableaux de bord adaptés à chaque métier, **sans exposer les salaires individuels, les informations d'absence ou les données d'autres sites aux personnes non habilitées** ?

### 2.1 Objectifs métier

| Utilisateur | Question métier | Indicateurs envisagés |
|---|---|---|
| Direction RH | Comment évoluent les effectifs et les absences selon les sites ? | Effectif actif, entrées/sorties, turnover, taux d'absentéisme agrégé |
| Direction financière | Les dépenses de personnel respectent-elles le budget ? | Masse salariale brute, budget vs réalisé, écarts mensuels |
| Direction générale | Quelle est la situation sociale et budgétaire globale ? | Synthèse consolidée des effectifs, absences et coûts |
| Responsables d'entrepôt | Dispose-t-on des effectifs suffisants pour piloter les équipes ? | Effectifs et absences agrégés de leur seul site |

### 2.2 Objectifs prioritaires de sécurité

1. **Minimiser** les données dès l'extraction : pas de RIB, numéro de sécurité sociale, bulletins de paie complets ni justificatifs médicaux.
2. **Séparer les accès** aux données brutes, intégrées et de reporting ; différencier les rôles RH, finance, direction et managers.
3. **Prévenir les divulgations indirectes** : petits effectifs, croisements de filtres, comparaison entre périodes ou exports.
4. **Assurer la traçabilité** des chargements, consultations privilégiées et exports sensibles, sans journaliser inutilement le contenu des données.
5. **Protéger les traitements et le stockage** : chiffrement, comptes techniques, secrets, sauvegardes et contrôle des privilèges.
6. **Définir des durées de conservation et de purge**, adaptées aux finalités et justifiées.
7. **Documenter la conformité et la souveraineté** : base légale à confirmer, information des salariés, sous-traitants, localisation des données et éventuels transferts hors EEE.
8. **Préparer la gestion d'incident**, afin de déterminer rapidement les données touchées et les mesures de réponse.

## 3. Périmètre et sources de données

Le projet porte sur **un seul pipeline de reporting RH**, comportant plusieurs sources et trois espaces de restitution métiers, et non sur l'ensemble du système d'information.

| Source fictive | Fonction | Données strictement nécessaires | Données exclues |
|---|---|---|---|
| **SIRH SaaS** | Référentiel salariés et contrats | Identifiant technique, site, service, type de contrat, dates d'entrée et de sortie | Adresse personnelle, contacts privés, évaluations individuelles, NIR |
| **Logiciel de paie** | Rémunération | Identifiant technique, période, salaire brut et primes utiles au reporting | RIB, bulletin complet, taux de prélèvement personnel |
| **GTA (gestion des temps et absences)** | Temps et absences | Identifiant technique, période, jours théoriques travaillés et nombre de jours d'absence relevant du périmètre retenu | Certificats médicaux, diagnostics, commentaires libres |
| **Outil budgétaire / ERP** | Prévisions financières | Site ou service, période, budget de masse salariale agrégé | Données nominatives non nécessaires |

L'**identifiant technique commun** permet le rapprochement des données SIRH, paie et GTA au niveau nécessaire aux calculs ; il n'est pas exposé dans les tableaux de bord. Les catégories d'absence retenues devront être validées avec les RH et le DPO, car certaines informations peuvent révéler indirectement des données de santé.

**Exclusions :** paie opérationnelle, gestion des dossiers médicaux, recrutement, évaluations individuelles, suivi de productivité par salarié, classement automatisé des personnes et données réelles.

## 4. Architecture du pipeline

**Flux logique :**

`SIRH SaaS + Paie + GTA + Budget → Ingestion sécurisée → Raw PostgreSQL → ETL/ELT d'intégration → Data Warehouse PostgreSQL → ETL/ELT métier → 3 Data Marts PostgreSQL → Power BI`

### 4.1 Ingestion sécurisée

Les données sont récupérées par API ou dépôts de fichiers selon des échanges planifiés. Chaque extraction utilise un compte technique dédié, un transport chiffré, une vérification du format et des journaux de chargement. Les échecs et doublons sont suivis sans insérer de données sensibles dans les logs.

### 4.2 Zone Raw — `raw_rh`

La zone Raw conserve **temporairement les extraits minimisés des sources**, sans transformation métier. L'accès est réservé aux traitements et aux personnels exceptionnellement habilités. La durée de conservation et la purge restent à justifier dans un livrable spécifique.

### 4.3 ETL/ELT d'intégration et Data Warehouse — `dw_rh`

Le traitement harmonise les identifiants, vérifie la qualité, gère les rejets et rapproche les sources. Le Data Warehouse héberge les informations intégrées et les historiques strictement nécessaires. Les tables relatives aux rémunérations et absences bénéficient de permissions particulièrement restrictives.

### 4.4 ETL/ELT métier et Data Marts

Les calculs métiers produisent des données agrégées par période et site/service : effectifs, jours d'absence, masse salariale et écarts au budget. Les transformations appliquent des contrôles de confidentialité avant la mise à disposition. **Les petits groupes et les combinaisons de filtres susceptibles d'identifier des salariés ne sont pas diffusés.**

| Data Mart PostgreSQL | Usage | Accès métier | Restitution Power BI |
|---|---|---|---|
| `dm_pilotage_rh` | Effectifs, contrats, mouvements, absentéisme | Direction RH | **Dashboard RH** : dynamique des effectifs et absences |
| `dm_masse_salariale` | Masse salariale, budget vs réalisé, écarts | Finance et RH habilitées | **Dashboard Finance** : pilotage budgétaire |
| `dm_management` | Effectifs et absentéisme agrégés par site | Managers habilités sur leur site | **Dashboard Managers** : pilotage des équipes |

La **direction générale** dispose d'un **dashboard exécutif consolidé**, alimenté par des vues agrégées et autorisées des deux premiers Data Marts : pas besoin d'un quatrième magasin de données.

> **Point de sécurité essentiel :** la séparation en schémas PostgreSQL n'est qu'une séparation logique. Des comptes, privilèges, vues et contrôles de sécurité adaptés sont nécessaires. Un indicateur agrégé n'est pas automatiquement anonyme.

## 5. Technologies et hébergement envisagés

| Composant | Choix proposé | Point de vigilance |
|---|---|---|
| Sources | Applications fictives, dont un **SIRH SaaS** | Contrats, habilitations, localisation des services |
| Ingestion / ETL | Python et SQL ; orchestration Apache Airflow | Comptes techniques, secrets, chiffrement des échanges |
| Raw / DW / Data Marts | PostgreSQL, schémas séparés | Permissions, audit, sauvegardes, chiffrement |
| Tableau de bord | Power BI | SSO/MFA, droits d'accès, export, emplacement des données et configuration du service |
| Journaux et supervision | Journalisation centralisée à définir | Accès contrôlé et intégrité des journaux |

**Hypothèse d'hébergement :** PostgreSQL serait hébergé sur une infrastructure située en France ou dans l'Union européenne ; les conditions exactes d'hébergement du SIRH et de Power BI devront être étudiées. **La localisation dans l'UE ne suffit pas à garantir la souveraineté** : les accès des prestataires, les sauvegardes, la sous-traitance et les éventuels transferts hors EEE devront être examinés. Aucun fournisseur n'est définitivement retenu dans cette fiche.

## 6. Acteurs et responsabilités

| Acteur | Rôle dans le projet |
|---|---|
| Direction générale | Valide les grandes finalités et consulte la synthèse exécutive |
| Direction RH | Définit et valide les indicateurs sociaux et les règles d'usage |
| Responsable paie / direction financière | Valide les indicateurs de rémunération et de budget |
| Managers d'entrepôt | Consultent uniquement des indicateurs collectifs de leur périmètre |
| Équipe Data | Conçoit les flux, les contrôles de qualité et la sécurité des transformations |
| Administrateur PostgreSQL | Administre le stockage et les privilèges techniques |
| RSSI | Valide les exigences de sécurité et suit les incidents |
| DPO | Conseille sur la conformité RGPD, les données d'absence et la nécessité d'une AIPD |

L'entreprise fictive est supposée déterminer les finalités et moyens essentiels du traitement : elle est donc le **responsable de traitement** dans ce scénario. Les rôles précis des fournisseurs restent à qualifier.

## 7. Risques majeurs et décisions à instruire

| Scénario concret | Enjeu | Orientation envisagée |
|---|---|---|
| Un manager de Lille accède aux indicateurs RH du site de Nantes | Accès inter-sites non autorisé | Autorisations par rôle et périmètre, tests de filtrage côté données |
| Un service de deux salariés affiche un salaire moyen | Réidentification par déduction | Masquage des petits groupes, limitation des filtres et tests de recoupement |
| Un compte ETL compromis exporte la table de paie | Violation massive de confidentialité | Moindre privilège, coffre de secrets, logs et alertes |
| Un fichier Raw reste disponible trop longtemps | Conservation excessive | Durée limitée, purge vérifiable et sauvegardes maîtrisées |
| Une actualisation Power BI diffuse des données dans un mauvais espace | Mauvaise configuration de diffusion | Validation des jeux de données, groupes habilités et contrôle des exports |
| Un prestataire peut accéder aux données depuis l'étranger | Risque de transfert et de souveraineté | Analyse des sous-traitants, garanties et localisation effective |

Les seuils de diffusion, durées de conservation, bases légales précises, mécanismes de notification et choix d'hébergement **seront justifiés dans les livrables dédiés**, plutôt que fixés arbitrairement dans cette fiche.

**Schéma d'architecture associé :** `schema_pipeline_rh_v3.png` (à joindre au dossier).
