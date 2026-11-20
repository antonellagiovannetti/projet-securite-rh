# Projet de groupe : sécuriser et gouverner un pipeline de données

Souveraineté et sécurité de la donnée · M2 Data Engineer · 2026-2027

## Sommaire

1. [L'objectif du projet](#1-lobjectif-du-projet)
2. [Choisir un sujet](#2-choisir-un-sujet)
   - [Les groupes](#21-les-groupes)
   - [Un sujet recevable](#22-un-sujet-recevable)
   - [Les sujets proposés](#23-les-sujets-proposés)
3. [Le déroulement](#3-le-déroulement)
   - [Ce que vous livrez, journée par journée](#31-ce-que-vous-livrez-journée-par-journée)
   - [L'incident du 11 décembre](#32-lincident-du-11-décembre)
4. [Ce que vous rendez](#4-ce-que-vous-rendez)
5. [Les règles du projet](#5-les-règles-du-projet)
6. [L'évaluation](#6-lévaluation)

## 1. L'objectif du projet

En groupe, vous prenez un pipeline de données réaliste, sur un sujet que
vous choisissez, et vous élaborez sa politique de sécurité et de
protection des données : ce que contiennent les données, qui y accède,
ce que la loi permet, où elles sont hébergées, quels risques elles
courent, et ce que vous faites quand un incident arrive. Chaque règle
de cette politique est justifiée et tracée dans un dossier.

Le cours montre chaque étape sur un exemple, l'entreprise fictive
Sorvenne Céramiques ; vous l'appliquez ensuite à votre sujet. Il n'y a
rien à installer : le dossier se construit sur papier (schémas,
tableaux, quelques commandes SQL, notes), et chaque choix est justifié.

> **Attention : jamais de données réelles.**
> Il n'y a pas de données à collecter : vous décrivez votre pipeline
> (sources, tables, colonnes, usages) comme s'il existait. N'utilisez
> aucune donnée réelle, ni la vôtre, ni celle d'une organisation, ni
> celle d'un jeu de données trouvé en ligne : ce serait traiter des
> données personnelles sans base légale, ce que ce cours apprend
> justement à éviter. Si vous voulez illustrer, quelques lignes
> fictives suffisent.

> **Attention : vous n'êtes pas du métier de votre sujet.**
> C'est la situation habituelle d'un Data Engineer. Renseignez-vous
> (sources publiques, usages que vous connaissez en tant qu'usager), et
> restez réalistes : un contexte crédible et cohérent compte plus qu'un
> contexte détaillé.

## 2. Choisir un sujet

### 2.1 Les groupes

- Deux groupes, de 4 et de 3 personnes.
- Chaque groupe choisit un sujet différent dans la liste ci-dessous,
  ou propose le sien.
- Choix du sujet le 20 novembre au matin, validation par l'enseignant
  avant la fin de la matinée.

### 2.2 Un sujet recevable

Si vous proposez votre propre sujet, il doit avoir :

1. un pipeline de données : des sources, des transformations, un
   stockage, une exposition (tableau de bord, API, export) ;
2. des données personnelles ;
3. au moins un service cloud ou SaaS ;
4. au moins deux métiers concernés, en plus de l'équipe data ;
5. une taille tenable : un pipeline, pas tout un système
   d'information.

### 2.3 Les sujets proposés

Chaque sujet est une organisation fictive. Vous complétez le contexte :
c'est à vous de décrire les sources, les tables et les usages, de façon
réaliste.

**1. Le suivi des étudiants d'une école**
Notes, absences, plateforme d'apprentissage en ligne, tableau de bord
pour la direction et les enseignants. Vous le vivez de l'intérieur.
Enjeux : qui voit quoi (enseignant, direction, étudiant), durée de
conservation, données de mineurs pour certaines formations.

**2. Une application de santé connectée ou de téléconsultation**
Des mesures (rythme cardiaque, sommeil), des comptes rendus, un
hébergement dans le cloud. Enjeux : données de santé (catégorie
particulière du RGPD), hébergement réglementé, analyse d'impact.
Une ressource sur l'hébergement des données de santé vous sera fournie.

**3. Une plateforme de recrutement**
Des CV, des candidatures, un moteur de tri, des recruteurs clients.
Enjeux : conservation des candidatures, tri automatisé des candidats,
accès des clients.

**4. Une salle de sport avec accès biométrique**
Badges ou empreintes digitales, abonnements, fréquentation, offres
ciblées. Enjeux : biométrie (catégorie particulière), proportionnalité,
alternatives à proposer.

**5. La mobilité urbaine en données ouvertes**
Des trajets de vélos ou de trottinettes en libre-service, publiés en
données ouvertes. Enjeux : anonymiser des trajets, ce qu'un trajet
révèle (domicile, travail), ce qu'on peut publier.

**6. Un assistant IA de service client**
Des conversations avec les clients, réutilisées pour améliorer un
modèle. Enjeux : ce qu'on confie au modèle, ce qu'il retient,
où il est hébergé. Une ressource sur la sécurité des pipelines d'IA vous
sera fournie.

**7. La fidélité et les recommandations d'un e-commerçant**
Historique d'achats, carte de fidélité, recommandations, partenaires
publicitaires. Enjeux : profilage, consentement, durée de conservation,
partage avec des tiers.

**8. Un tableau de bord RH**
Absentéisme, salaires, évaluations annuelles, pour la direction et les
managers. Enjeux : classification restreinte, moindre privilège,
données de santé (arrêts maladie).

**9. Sujet libre**
Accepté s'il respecte les cinq critères ci-dessus.

## 3. Le déroulement

### 3.1 Ce que vous livrez, journée par journée

| Date | Ce que vous livrez |
| --- | --- |
| Vendredi 20 novembre, matin | La fiche projet (modèle fourni), avec les outils que vous utilisez, et le schéma du pipeline |
| Vendredi 20 novembre, après-midi | L'inventaire et la classification des données ; les rôles de gouvernance ; la matrice d'accès de votre pipeline et sa règle de revue ; la règle de purge |
| Lundi 7 décembre | Un extrait du registre des traitements ; une note de choix d'hébergement sous l'angle de la souveraineté |
| Mardi 8 décembre | Une analyse de risques allégée, des exigences, deux fiches de décision d'architecture |
| Vendredi 11 décembre, matin | L'enquête sur un incident, à partir des journaux de votre pipeline |
| Vendredi 11 décembre, après-midi | Examen écrit individuel ; remise du dossier et du rapport final (date limite à préciser) |

Chaque document commence par un en-tête qui dit qui peut le lire, à
quel stade il en est, qui en décide, et ce qui a été produit avec une
IA :

```yaml
---
audience: equipe   # qui peut le lire
maturity: brouillon
maturity_note: "À revalider quand : <condition>"
owner: <nom>       # qui décide de ce document
ia: <ce qui a été produit avec une IA, et comment ; « aucun » sinon>
---
```

### 3.2 L'incident du 11 décembre

Avant la séance, chaque groupe reçoit les journaux d'accès de son
pipeline, générés pour son sujet. Un incident s'y cache. En séance, vous
enquêtez avec des requêtes SQL : que s'est-il passé, depuis quand,
quelles données sont touchées, faut-il prévenir la CNIL, que faire dans
la première heure. Vous en tirez une note pour la direction.

## 4. Ce que vous rendez

Deux pièces, en fin de projet :

- **Le dossier** : les pièces produites séance après séance, dans un
  `.zip` ou un dépôt Git :
  - un fichier Markdown par livrable, avec son en-tête ;
  - les schémas, en image ou en code (diagram as code) ;
  - les requêtes SQL dans des fichiers `.sql`.
- **Le rapport final** : un PDF de quelques pages, rédigé par le
  groupe, qui présente la politique de sécurité et de protection des
  données de votre pipeline, et ce qui la justifie.

## 5. Les règles du projet

- Chaque pièce est signée par son auteur principal ; chacun signe au
  moins une pièce.
- Le travail se fait en séance ; le dossier garde la trace de vos
  décisions, y compris les options écartées.
- Les audits entre groupes portent sur la démarche, jamais sur la
  personne. Ils ne sont pas notés.
- IA générative : autorisée et déclarée. Chaque livrable dit ce qui a
  été produit avec une IA et comment (ligne `ia` de l'en-tête). L'IA
  peut écrire ; c'est vous qui décidez, et vous devez pouvoir justifier
  chaque choix pendant les points d'étape avec l'enseignant.

## 6. L'évaluation

- Le dossier et le rapport final du groupe.
- Un examen écrit individuel, le 11 décembre.
- Les notes écrites en séance ne sont pas notées : elles prouvent votre
  contribution au groupe.
- La grille des écrits et la grille d'audit croisé vous sont remises
  dès la première séance.
