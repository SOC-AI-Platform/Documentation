# Présentation complète — SOC AI Platform
## Plateforme intelligente d'analyse et de réponse aux incidents de cybersécurité
### Projet de fin d'études — STAR Assurances

---

# Objectif de la présentation

Cette présentation ne doit pas seulement montrer une application.

Le but est de convaincre le jury : les responsables IT de star assurances que le projet apporte une réelle valeur opérationnelle à un SOC moderne.

Le fil conducteur est simple :

> Comment transformer des logs bruts en une analyse complète d'incident, fiable, contextualisée et directement exploitable par un analyste SOC ? le but est d'aider l'équipe à gagner du temps , d'effort et rend l'analyse plus fiable et sur grande echelle avec tous les sources necessaires pour toute personne de l'equipe, du junior à l'expert.

---

# Plan de la présentation

1. Contexte et problématique
2. Les objectifs du projet
3. Présentation de la plateforme appelée SAQR
4. Architecture générale
5. Pipeline complet de traitement
6. Architecture Multi-Agent IA
7. Intelligence augmentée par RAG
8. Détection MITRE ATT&CK
9. Reconstruction automatique de la Kill Chain
10. IOC Extraction & IOC Enrichment
11. Live Threat Map
12. Plan de remédiation intelligent
13. Rapport PDF professionnel
14. Technologies utilisées
15. Infrastructure Cloud
16. Sécurité de la plateforme
17. Résultats obtenus
18. Valeur ajoutée
19. Perspectives d'évolution
20. Conclusion

---

# 1. Contexte

Les compagnies d'assurance manipulent quotidiennement des données sensibles :

- données clients
- données financières
- contrats
- informations personnelles

Ces organisations sont donc des cibles privilégiées des cyberattaques :

- Phishing
- Credential Theft
- Malware
- Ransomware
- Exfiltration de données
- Accès non autorisés

Le Security Operations Center (SOC) est chargé de surveiller ces menaces en continu.

---

# Le problème d'un SOC classique

Aujourd'hui un analyste SOC reçoit chaque jour :

- plusieurs milliers voire millions de logs
- des alertes provenant de plusieurs outils
- des IOC à analyser
- des rapports à produire

Les difficultés sont nombreuses :

- énorme volume de logs
- surcharge des analystes
- corrélation manuelle des événements
- recherche OSINT chronophage
- reconstruction de l'attaque difficile
- recommandations souvent génériques
- rapports rédigés manuellement
- temps d'investigation élevé

Résultat :

> Les analystes passent beaucoup de temps à collecter les informations plutôt qu'à prendre des décisions de remédiation.

---

# Objectif du projet

Développer une plateforme capable d'automatiser l'ensemble du cycle d'investigation.

À partir de simples logs, la plateforme est capable de :

- détecter automatiquement le type d'incident
- analyser les événements
- extraire les IOC
- enrichir les IOC grâce à des sources OSINT
- identifier les techniques MITRE ATT&CK
- reconstruire la Kill Chain
- générer un résumé exécutif
- proposer une remédiation contextualisée
- exporter automatiquement un rapport PDF professionnel

L'objectif est de transformer des données brutes en informations directement exploitables.

---

# Présentation de la solution

SAQR est une plateforme d'analyse intelligente destinée à assister les analystes SOC.

Elle ne remplace pas l'analyste.

Elle agit comme un assistant intelligent qui automatise les tâches répétitives et fournit une aide à la décision.

Elle accompagne l'analyste durant tout le cycle de réponse à incident :

- Analyse
- Qualification
- Enrichissement
- Compréhension
- Remédiation
- Documentation

---

# Architecture générale

```
Utilisateur

↓

React + TailwindCSS

↓

FastAPI

↓

Pipeline Multi-Agent IA

↓

Sanitization

↓

IOC Extraction

↓

IOC Enrichment

↓

MITRE Mapping

↓

Kill Chain Builder

↓

RAG

↓

LLM Groq Llama 3.3

↓

Remediation

↓

Report Generator

↓

PostgreSQL + ChromaDB

↓

Export PDF
```

---

# Pipeline complet

Les données suivent toujours le même chemin.

## Étape 1

Réception des logs

Le système reçoit :

- logs Windows
- logs Linux
- logs Firewall
- logs Proxy
- logs EDR

---

## Étape 2

Sanitization

Avant toute interaction avec le LLM :

- masquage des emails
- masquage des numéros de contrat
- suppression des données sensibles

Ainsi, aucune donnée confidentielle n'est envoyée au modèle d'IA.

---

## Étape 3

Extraction automatique des IOC

Le système détecte automatiquement :

- IP
- Domaines
- Emails
- Numéros de contrat

Les IOC sont extraits avant toute analyse.

---

## Étape 4

IOC Enrichment

Chaque IOC est automatiquement enrichi.

Sources utilisées :

- VirusTotal
- AlienVault OTX
- AbuseIPDB

Le système récupère notamment :

- réputation
- score de malveillance
- pays d'origine
- ASN
- opérateur réseau
- historique des détections
- campagnes connues
- informations Threat Intelligence

Ainsi, l'analyste dispose immédiatement d'un contexte enrichi sans effectuer de recherches manuelles.

---

# Live Threat Map

Une fonctionnalité différenciante de la plateforme est la **Live Threat Map**.

À partir des IOC enrichis :

- les adresses IP sont géolocalisées
- les domaines malveillants sont associés à leur pays lorsque l'information est disponible
- les données issues de VirusTotal, AbuseIPDB et AlienVault OTX sont corrélées

La carte affiche en temps réel :

- les pays d'origine des menaces
- les adresses IP identifiées
- les domaines impliqués
- les tentatives de connexion vers l'infrastructure surveillée

L'objectif est d'offrir une représentation visuelle immédiate de la provenance des attaques.

Cette visualisation permet aux analystes de :

- identifier rapidement les zones géographiques les plus actives
- détecter des campagnes coordonnées
- comprendre l'origine des IOC
- faciliter les présentations aux responsables sécurité

---

# Architecture Multi-Agent IA

Plutôt qu'un unique prompt gigantesque, le système utilise plusieurs agents spécialisés.

Chaque agent possède une responsabilité unique.

## Agent Sanitizer

- anonymisation
- protection RGPD
- extraction des IOC

---

## Agent Classifier

- détection du type d'incident
- sévérité
- urgence

---

## Agent MITRE Mapper

- détection des techniques ATT&CK
- extraction des preuves
- justification des techniques retenues

---

## Agent Kill Chain

- reconstruction du scénario
- regroupement des événements
- ordre logique des phases

---

## Agent IOC Enrichment

- VirusTotal
- AbuseIPDB
- AlienVault OTX

---

## Agent Remediation

Production d'un plan de réponse complet.

---

## Agent Report Generator

Création automatique du rapport PDF.

---

# Pourquoi une architecture Multi-Agent ?

Cette architecture apporte plusieurs avantages :

- responsabilités clairement séparées
- maintenance facilitée
- ajout de nouveaux agents sans modifier les autres
- débogage plus simple
- meilleure évolutivité
- meilleure réutilisabilité

Chaque agent est indépendant mais collabore avec les autres.

---

# Intelligence augmentée par RAG

Le modèle IA ne travaille pas uniquement avec ses connaissances d'entraînement.

Avant chaque analyse, le système interroge automatiquement une base documentaire.

Référentiels utilisés :

- MITRE ATT&CK
- D3FEND
- CISA
- CAPEC
- OWASP Top 10

Ces documents sont indexés dans ChromaDB sous forme de vecteurs.

Le moteur RAG récupère les passages les plus pertinents et les injecte dans le contexte du LLM.

---

# Pourquoi utiliser un RAG ?

Le RAG permet :

- d'utiliser des connaissances actualisées
- de réduire les hallucinations
- d'améliorer la précision
- d'aligner les recommandations avec les standards internationaux
- de fournir des réponses traçables

Chaque recommandation est donc appuyée par une source documentaire pertinente.

---

# Corrélation MITRE ATT&CK

Le système détecte automatiquement :

- les tactiques
- les techniques
- les sous-techniques

Pour chaque technique détectée, la plateforme fournit :

- son identifiant MITRE
- une description
- les preuves présentes dans les logs
- la justification du choix
- des recommandations de détection

L'objectif est de standardiser l'analyse et de faciliter la communication entre analystes.

---

# Reconstruction automatique de la Kill Chain

Une simple chronologie des événements n'est pas suffisante.

Le système :

- regroupe les événements similaires
- supprime les répétitions
- respecte l'ordre logique d'une attaque
- reconstruit le scénario complet

L'analyste comprend immédiatement la progression de l'attaquant.

---

# IOC Enrichment

Les IOC extraits sont automatiquement enrichis.

Sources :

- VirusTotal
- AlienVault OTX
- AbuseIPDB

Les informations récupérées incluent :

- réputation
- géolocalisation
- pays
- ASN
- opérateur
- campagnes connues
- niveau de risque
- historique de détection

Toutes ces informations sont intégrées directement dans le rapport.

---

# Remédiation intelligente

La plateforme ne se limite pas à proposer :

> "Bloquer l'adresse IP"

Elle génère un véritable plan de réponse.

Chaque action contient :

- priorité
- phase NIST SP 800-61
- justification
- preuves
- procédures détaillées
- commandes PowerShell
- commandes Firewall
- impact métier
- règles SIEM

Le résultat est directement exploitable par une équipe SOC.

---

# Rapport PDF professionnel

Le rapport contient automatiquement :

- résumé exécutif
- type d'incident
- sévérité
- urgence
- score de confiance
- MITRE ATT&CK
- Kill Chain
- IOC enrichis
- Live Threat Map
- remédiation
- règles SIEM
- synthèse finale

Le rapport est exportable au format PDF.

Il peut être transmis directement aux responsables sécurité.

---

# Technologies utilisées

## Backend

- FastAPI
- Python
- LangChain
- LangGraph
- Groq
- Llama 3.3 70B
- PostgreSQL
- ChromaDB
- Redis

---

## Frontend

- React
- TailwindCSS
- React Query
- Axios
- D3.js
- d3-geo
- topojson-client

---

## Infrastructure

- Docker
- Kubernetes
- Minikube
- ConfigMap
- Secret
- Namespace dédié

---

# Sécurité de la plateforme

La sécurité est intégrée dès la conception.

Fonctionnalités :

- JWT
- RBAC
- Refresh Token
- Hash des mots de passe
- Validation Pydantic
- Sanitization RGPD
- Protection des API

Les données sensibles ne quittent jamais l'environnement sécurisé.

---

# Résultats obtenus

La plateforme permet :

✔ Détection automatique des incidents

✔ Analyse entièrement automatisée

✔ Extraction automatique des IOC

✔ Enrichissement OSINT instantané

✔ Visualisation géographique des menaces

✔ Reconstruction automatique de la Kill Chain

✔ Corrélation MITRE ATT&CK

✔ Génération immédiate d'un rapport PDF

✔ Remédiation contextualisée

✔ Aide à la décision pour les analystes SOC

✔ Réduction significative du temps d'investigation

✔ Standardisation des rapports d'incident

---

# Valeur ajoutée

La plateforme apporte une valeur opérationnelle à plusieurs niveaux :

- gain de temps
- homogénéisation des analyses
- amélioration de la qualité des rapports
- aide à la décision
- réduction des tâches répétitives
- meilleure compréhension des attaques
- meilleure exploitation de la Threat Intelligence

Elle transforme un processus largement manuel en un flux automatisé, cohérent et traçable.

---

# Perspectives d'évolution

## Court terme

- Support avancé Sigma
- Plus de connecteurs SIEM
- Tableau de bord temps réel
- Authentification SSO
- Gestion multi-utilisateurs
- Alertes en temps réel
- Historique enrichi

---

## Moyen terme

- Détection comportementale
- Corrélation multi-incidents
- Graphes d'attaque
- Analyse de malware
- Score de risque dynamique
- Threat Hunting assisté

---

## Long terme

- Agent SOC autonome
- Réponse automatique SOAR
- Intégration Microsoft Sentinel
- Splunk Enterprise Security
- IBM QRadar
- Cortex XSOAR
- MISP
- OpenCTI
- Streaming Kafka
- MCP (Model Context Protocol)

---

# Les points différenciants

La plateforme ne se contente pas de détecter des incidents.

Elle accompagne l'analyste durant tout le cycle de réponse :

- Analyse
- Qualification
- Enrichissement
- Compréhension
- Visualisation
- Remédiation
- Documentation

Elle agit comme un véritable assistant SOC intelligent plutôt qu'un simple moteur de détection.

---

# Les 5 messages clés à transmettre au jury

## 1. Une architecture IA modulaire

Chaque agent est spécialisé dans une étape précise de l'investigation, ce qui rend la plateforme évolutive, maintenable et facilement extensible.

---

## 2. Des décisions fondées sur des référentiels reconnus

Les analyses et recommandations sont enrichies par MITRE ATT&CK, D3FEND, CISA, CAPEC et OWASP grâce au RAG, garantissant des résultats contextualisés et conformes aux bonnes pratiques.

---

## 3. Une automatisation de bout en bout

À partir de logs bruts, la plateforme produit automatiquement :

- la classification
- les IOC enrichis
- les techniques MITRE
- la Kill Chain
- le plan de remédiation
- le rapport PDF

sans intervention manuelle.

---

## 4. Une architecture pensée pour un environnement industriel

La plateforme repose sur des technologies modernes :

- React
- FastAPI
- PostgreSQL
- ChromaDB
- Docker
- Kubernetes

Elle est conçue pour être déployée, maintenue et faire évoluer un SOC réel.

---

## 5. Un impact opérationnel concret

Le projet permet :

- de réduire le temps d'investigation
- d'améliorer la prise de décision
- de standardiser les rapports
- de fournir une vision globale des attaques grâce à la Live Threat Map
- d'augmenter l'efficacité des analystes SOC sans remplacer leur expertise

---

# Conclusion

SOC AI Platform démontre qu'il est possible d'associer intelligence artificielle, Threat Intelligence, référentiels de cybersécurité et architecture moderne pour automatiser l'ensemble du cycle de réponse à incident.

Le projet ne cherche pas uniquement à détecter des attaques. Il vise à fournir aux analystes un assistant intelligent capable de transformer des logs bruts en une analyse complète, contextualisée, documentée et directement exploitable.

Cette approche répond aux enjeux actuels des SOC : faire face à l'augmentation du volume des alertes, réduire le temps d'investigation, homogénéiser les analyses et améliorer la qualité des décisions en s'appuyant sur des standards reconnus et des informations de Threat Intelligence enrichies.


2026-09-16 19:15:07,046 INFO sqlalchemy.engine.Engine select pg_catalog.version()
2026-09-16 19:15:07,047 INFO sqlalchemy.engine.Engine [raw sql] {}
2026-09-16 19:15:07,051 INFO sqlalchemy.engine.Engine select current_schema()
2026-09-16 19:15:07,052 INFO sqlalchemy.engine.Engine [raw sql] {}
2026-09-16 19:15:07,055 INFO sqlalchemy.engine.Engine show standard_conforming_strings
2026-09-16 19:15:07,055 INFO sqlalchemy.engine.Engine [raw sql] {}
2026-09-16 19:15:07,067 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-09-16 19:15:07,070 INFO sqlalchemy.engine.Engine SELECT users.id AS users_id, users.username AS users_username, users.email AS users_email, users.hashed_password AS users_hashed_password, users.role AS users_role, users.is_active AS users_is_active, users.failed_login_attempts AS users_failed_login_attempts, users.password_updated_at AS users_password_updated_at, users.reset_token_hash AS users_reset_token_hash, users.reset_token_expires_at AS users_reset_token_expires_at 
FROM users
2026-09-16 19:15:07,070 INFO sqlalchemy.engine.Engine [generated in 0.00020s] {}
username = 'Admin' | role = 'admin'
2026-09-16 19:15:07,076 INFO sqlalchemy.engine.Engine ROLLBACK
