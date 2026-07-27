# Contexte — Préparation d'une présentation technique pour les responsables IT

Je termine un stage de fin d'études (8 semaines) à STAR Assurances (compagnie d'assurance en Tunisie), où j'ai construit une plateforme SOC (Security Operations Center) alimentée par l'IA. Je dois présenter ce projet aux **responsables IT de l'entreprise** — un public technique mais pas nécessairement spécialiste en IA/LLM.

Aide-moi à préparer une présentation complète et structurée, couvrant absolument tous les aspects du projet : problématique, conception, architecture, RAG, cloud/infra, IA, sécurité, fonctionnalités. Je veux un support que je peux directement utiliser pour construire mes slides et mon discours.

---

## 1. Problématique métier

Les compagnies d'assurance font face en continu à des menaces cyber : phishing, vol de credentials, fraude, accès non autorisé aux données clients. L'équipe sécurité de STAR avait besoin d'un outil capable de prendre un **log brut** ou une **alerte** en entrée et de produire automatiquement :
- Classification de la menace avec mappage MITRE ATT&CK
- Reconstruction de la chaîne d'attaque (Kill Chain) avec timeline
- Enrichissement des IOC (IP, domaines) via des sources OSINT
- Rapport d'analyse complet avec export PDF officiel
- Recommandations de remédiation opérationnelles, structurées selon NIST SP 800-61 (Containment/Eradication/Recovery/Detection)
- Score de confiance déterministe et honnête (pas une auto-évaluation du modèle IA)

**Contrainte de sécurité stricte imposée dès le départ** : la plateforme ne doit **jamais** envoyer de données sensibles (identifiants de contrats, emails clients) à un LLM externe. Seules les données techniques nécessaires sont transmises, après une couche de sanitization RGPD systématique.

---

## 2. Architecture technique (pipeline en 9 couches)

1. **Frontend** — React 18/19, Tailwind CSS, Axios, d3-geo/topojson-client (carte de menaces géolocalisées en temps réel)
2. **API Gateway** — FastAPI (Python 3.12), authentification JWT
3. **Sanitization Layer** — Regex + Pydantic v2 : masquage RGPD (emails, numéros de contrat) et extraction d'IOC (IP, domaines) avant tout envoi au LLM
4. **IOC Enrichment** — appels parallèles asynchrones (asyncio) à VirusTotal, AbuseIPDB, AlienVault OTX
5. **RAG Knowledge Base** — ChromaDB (base vectorielle) + Sentence-BERT pour l'embedding, **11 688 chunks indexés** provenant de 5 référentiels de sécurité officiels : MITRE ATT&CK, D3FEND, CAPEC, CISA KEV, OWASP Top 10
6. **Multi-Agent Pipeline** — orchestré avec LangGraph, 4 agents IA séquentiels sur Groq (modèle Llama 3.3 70B, température=0 pour un maximum de reproductibilité) : **Triage → Mapping MITRE → Kill Chain → Remediation**
7. **Confidence Scoring** — calcul déterministe (pas d'auto-évaluation du LLM), basé sur 5 critères pondérés
8. **Report Engine** — génération PDF (ReportLab) + Live Threat Map interactive (SVG/d3-geo)
9. **Data Storage** — PostgreSQL (colonnes JSONB pour les données structurées complexes) + Redis

**Infrastructure** : conteneurisation Docker, orchestration Kubernetes (testé sur Minikube en environnement de développement), namespace dédié `soc-platform`.

**Authentification & sécurité applicative** : JWT (access token 1h / refresh token 7 jours), RBAC à 3 rôles (admin/analyst/auditor), politique de mot de passe conforme ANSSI (12 caractères minimum, historique des 5 derniers mots de passe, expiration à 60 jours, blocage après 3 tentatives échouées).

---

## 3. Le cœur du système — les 4 agents IA

**Agent 1 — Triage** : classifie le type d'incident et évalue sa sévérité (1-10), en s'appuyant sur le RAG (ATT&CK/CAPEC/CISA) pour ancrer sa classification dans des référentiels réels plutôt que d'improviser.

**Agent 2 — Mapping** : identifie les techniques MITRE ATT&CK précises (avec sous-techniques, ex. T1003.001 plutôt que T1003 générique), avec description de chaque technique et recommandation de détection spécifique. Chaque technique proposée est validée contre une whitelist des vrais identifiants MITRE (anti-hallucination).

**Agent 3 — Kill Chain** : reconstruit la chronologie de l'attaque, phase par phase (Reconnaissance → Initial Access → Execution → ... → Impact), avec fusion intelligente des événements répétitifs et respect strict de la chronologie réelle des logs.

**Agent 4 — Remediation** : génère un plan de réponse à incident opérationnel structuré selon les 4 phases NIST SP 800-61 (Containment/Eradication/Recovery/Detection Improvements), avec pour chaque action : preuve associée, procédure détaillée, commandes concrètes (PowerShell, EDR, Firewall), impact métier potentiel, et règles de détection SIEM prêtes à l'emploi dans 3 formats (Sigma, Splunk SPL, Microsoft Sentinel KQL).

**Transparence des sources** : le système n'affiche jamais seulement "d'après D3FEND" de manière vague — il expose la vraie liste des extraits de connaissance effectivement récupérés par le moteur RAG pour chaque analyse, garantissant que chaque recommandation est traçable à une source réelle.

---

## 4. Fiabilité et honnêteté du système (point différenciant fort)

C'est un axe que je veux particulièrement mettre en avant : contrairement à beaucoup de démonstrations IA qui affichent une confiance artificiellement élevée, ce système est conçu pour être **honnête sur ses propres limites** :
- Le score de confiance n'est **jamais un simple pourcentage auto-déclaré par le LLM** — il est recalculé de façon déterministe à partir de 5 critères vérifiables : qualité des preuves textuelles, corroboration OSINT réelle (VirusTotal/AbuseIPDB), cohérence entre les techniques MITRE et leurs preuves, cohérence entre les 4 agents entre eux, et pénalités uniquement sur des contradictions factuelles (jamais sur de simples absences d'information).
- Chaque affirmation du rapport est classée en **Confirmé / Probable / Non confirmé**, distinguant explicitement ce que les logs prouvent de ce qui est déduit ou simplement supposé.
- Sur des logs ambigus ou peu détaillés, le système peut afficher un score de confiance bas (ex. 17-48%) plutôt que de prétendre à tort être sûr de lui — comportement volontairement conçu ainsi.

---

## 5. Ce que je veux dans la présentation générée

Structure-moi une présentation complète, avec pour chaque section : le message clé à faire passer, les points techniques à mentionner, et des suggestions de visuels/schémas (ex. schéma du pipeline, capture d'écran type, diagramme d'architecture). Découpe en sections classiques d'une présentation projet :
1. Contexte et problématique
2. Objectifs
3. Architecture globale (avec le pipeline en 9 couches)
4. Zoom sur le RAG et la base de connaissances
5. Zoom sur les 4 agents IA et leur logique
6. Sécurité et conformité (sanitization RGPD, JWT, RBAC)
7. Infrastructure Cloud/Kubernetes
8. Fiabilité et honnêteté du scoring (point différenciant)
9. Démonstration (que montrer en live, dans quel ordre)
10. Limites connues et travaux futurs (sois transparent, ne les cache pas — c'est plus crédible)
11. Conclusion et valeur pour STAR

Pour chaque section, donne-moi aussi 2-3 questions probables que des responsables IT pourraient poser, avec des pistes de réponse — je veux être prête à improviser si on me challenge.





Le projet est une plateforme SOC (Security Operations Center) intelligente appelée **SOC AI Platform**.

Je veux que tu joues le rôle d'un architecte cybersécurité senior et d'un responsable technique qui prépare une soutenance professionnelle.

Cette présentation est destinée à des ingénieurs expérimentés.

Je veux donc une présentation très technique mais pédagogique.

Ne simplifie pas les concepts.

Je veux être capable de répondre aux questions du jury.

---

# Objectif

Prépare une présentation complète du projet depuis zéro.

La présentation doit durer environ 30 à 40 minutes.

Je veux comprendre chaque partie et non apprendre par cœur.

Pour chaque partie :

- expliquer le problème
- expliquer pourquoi cette solution a été choisie
- expliquer les alternatives
- expliquer les avantages
- expliquer les limites
- donner des exemples
- préparer les questions possibles du jury
- proposer les réponses

---

# Je veux que la présentation couvre absolument :

## 1. Présentation générale

- contexte STAR Assurances
- problème rencontré dans un SOC
- pourquoi utiliser l'IA
- objectifs du projet

---

## 2. Architecture générale

Expliquer complètement l'architecture.

Frontend React

↓

Backend FastAPI

↓

Pipeline Multi-Agents

↓

LLM (Groq / Llama)

↓

RAG

↓

ChromaDB

↓

PostgreSQL

↓

Export PDF

↓

Kubernetes

Je veux comprendre précisément le rôle de chaque composant.

---

## 3. Architecture IA

Expliquer :

Pourquoi une architecture Multi-Agent ?

Pourquoi plusieurs agents ?

Que fait chaque agent ?

Classification

MITRE

Kill Chain

Severity

Urgency

IOC Extraction

OSINT

Remediation

Pourquoi ne pas utiliser un seul prompt ?

---

## 4. Pipeline complet

Depuis les logs jusqu'au rapport PDF.

Montrer toutes les étapes.

Les données qui circulent.

Le rôle de chaque étape.

---

## 5. Partie RAG

Expliquer très profondément :

Pourquoi le RAG ?

Pourquoi pas un fine-tuning ?

Pourquoi ChromaDB ?

Comment fonctionne une base vectorielle ?

Embeddings

Chunking

Vector Search

Similarity

Top-K

Pourquoi D3FEND

Pourquoi CISA

Pourquoi OWASP

Comment les documents sont indexés

Comment ils sont retrouvés

Comment ils enrichissent les réponses

Comment éviter les hallucinations

---

## 6. IA Générative

Expliquer :

LLM

Prompt Engineering

Temperature

JSON Output

Pourquoi imposer un schéma JSON

Pourquoi parser le JSON

Pourquoi utiliser parse_llm_json()

Comment éviter les réponses incohérentes

---

## 7. MITRE ATT&CK

Expliquer :

Technique

Tactique

Kill Chain

IOC

Pourquoi MITRE est utilisé dans les SOC

Comment mon IA détecte les techniques

---

## 8. Kill Chain

Expliquer :

Reconnaissance

Initial Access

Execution

Persistence

Privilege Escalation

Defense Evasion

Credential Access

Discovery

Lateral Movement

Collection

Exfiltration

Impact

Pourquoi c'est utile

---

## 9. IOC

Expliquer :

IP

Domain

Email

Hash

Extraction

Sanitization

Enrichment

VirusTotal

AlienVault OTX

Pourquoi enrichir les IOC

---

## 10. Sécurité

Expliquer :

JWT

Authentification

RBAC

Hash des mots de passe

Refresh Token

Protection API

Validation

---

## 11. Kubernetes

Expliquer :

Pourquoi Kubernetes

Deployment

Pod

Service

ConfigMap

Secret

Rolling Update

Image Docker

Pourquoi Kubernetes dans ce projet

---

## 12. Docker

Expliquer :

Dockerfile

Image

Container

Build

COPY

RUN

CMD

Pourquoi Docker

---

## 13. Base de données

PostgreSQL

Pourquoi PostgreSQL

Tables

Incidents

Users

Historique

---

## 14. Frontend

React

Pourquoi React

Organisation

API

Affichage

Export PDF

---

## 15. Fonctionnalités

Présenter toutes les fonctionnalités.

Authentification

Analyse

Historique

Export

Visualisation

MITRE

Kill Chain

Remediation

RAG

---

## 16. Démonstration

Préparer un scénario complet.

Ce que je dois dire pendant la démonstration.

Pourquoi chaque résultat apparaît.

---

## 17. Limites

Hallucinations

Qualité des logs

Limites du RAG

Coût

Performances

---

## 18. Améliorations futures

SIEM réel

SOAR

EDR réel

Threat Intelligence

Streaming

Kafka

Fine-Tuning

Agents autonomes

MCP

---

## 19. Questions difficiles

Préparer au moins 100 questions possibles du jury.

Avec des réponses très techniques.

---

## 20. Conclusion

Résultats obtenus

Valeur ajoutée

Impact pour STAR Assurances

Perspectives
