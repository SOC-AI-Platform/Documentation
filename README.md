# # Journal de stage — Semaine 5 (Jours 21 à 25)

## Contexte

Poursuite du développement de la plateforme **SOC AI**. Cette semaine a principalement été consacrée à :

- La migration complète de l'environnement de développement vers le PC de stage.
- Le déploiement complet de l'infrastructure Kubernetes.
- La mise en production des comptes utilisateurs.
- Le début de la correction des bugs IA identifiés lors de la semaine précédente.

---

# Réalisations

## 1. Migration et déploiement de l'infrastructure (PC de stage)

- Reconstruction complète de l'environnement de développement :
  - Environnement virtuel Python (`venv`)
  - Dépendances Node.js (`node_modules`)
  - Secrets Kubernetes (`k8s/postgres/secret.yaml`), volontairement absents du dépôt Git pour des raisons de sécurité.

- Correction de plusieurs erreurs de déploiement Kubernetes :
  - Namespace manquant.
  - Utilisation de `kubectl apply -R` afin d'appliquer récursivement les manifestes.
  - Correction d'un nom d'image Docker incohérent.

- Migration du driver PostgreSQL :
  - Passage de **psycopg2** à **psycopg v3**, incompatible avec l'environnement de build précédent.
  - Allègement du fichier `requirements-docker.txt`.

- Résolution d'un conflit **CUDA / PyTorch** :
  - La carte graphique du poste n'était pas compatible avec la version installée.
  - Installation d'une version **CPU-only** de PyTorch.
  - Aucun impact sur la qualité des embeddings, uniquement sur les performances locales.

- Ré-ingestion complète de la base RAG :
  - **11 688 chunks** indexés avec succès.
  - Sources intégrées :
    - MITRE ATT&CK
    - MITRE D3FEND
    - CAPEC
    - CISA
    - OWASP

---

## 2. Gestion des comptes et sécurité

- Création du premier compte **Super Admin** via un script de bootstrap dédié (`create_first_admin.py`).

- Cette approche permet de contourner la dépendance circulaire de l'endpoint `/register`, qui nécessite déjà un administrateur authentifié.

- Validation complète du système d'authentification JWT :
  - Login
  - Register
  - Gestion des rôles utilisateurs

---

## 3. Tests fonctionnels du pipeline IA

Validation de **10 scénarios d'incidents** :

- Brute Force
- Phishing
- SQL Injection
- Malware / Command & Control
- Data Exfiltration
- Ransomware
- Reconnaissance
- Account Takeover
- DDoS
- Insider Threat

Le pipeline IA a été validé de bout en bout :

1. Sanitization
2. Enrichissement IOC
3. Triage
4. Mapping MITRE ATT&CK
5. Kill Chain
6. Remediation
7. Risk Scoring
8. Persistance
9. Génération du rapport PDF

Validation également de la **Live Threat Map** avec géolocalisation réelle sur plusieurs pays.

---

## 4. Bugs identifiés et corrigés

| Bug | Statut |
|------|--------|
| Export PDF renvoyait **401 Not Authenticated** (token transmis en query parameter au lieu du header `Authorization`) | ✅ Corrigé : passage à une requête Axios authentifiée avec téléchargement via Blob |
| Extraction des IOC limitée uniquement aux adresses IP | ✅ Corrigé : ajout d'une expression régulière pour les noms de domaine dans `sanitizer.py`, indépendante des IP et dédupliquée par rapport aux emails |
| L'agent Remediation recommandait systématiquement le blocage des IP, y compris pour les campagnes de phishing | 🔄 En cours : ajout d'une règle déterministe privilégiant le blocage du domaine, en attente de validation |
| Kill Chain répétait plusieurs fois la même phase (ex. : *Initial Access* répété trois fois) | 🔜 Identifié : refonte du prompt de `kill_chain.py` à réaliser |
| Les techniques MITRE ATT&CK étaient insuffisamment détaillées (absence de sous-techniques et preuves limitées) | 🔜 Identifié : amélioration du prompt de Mapping prévue |

---

# Points de vigilance et décisions techniques

- Validation avec l'encadrant sécurité :
  - Pour un incident de **phishing**, le blocage doit cibler le **nom de domaine** et non l'adresse IP seule, afin d'éviter les faux positifs sur des infrastructures mutualisées.

- Décision concernant la sanitization :
  - Les IOC techniques (adresses IP, domaines, URL) restent visibles et sont transmis au modèle IA (Groq).
  - Seules les véritables données personnelles (emails, numéros de contrat, cartes bancaires, secrets, etc.) sont anonymisées afin de respecter les exigences RGPD.

---

# Prochaines étapes

- Finaliser puis tester la règle de priorité du domaine dans `remediation.py`.
- Corriger la progression logique du **Cyber Kill Chain**.
- Enrichir le niveau de détail du **Mapping MITRE ATT&CK**.
- Vérifier la boucle d'enrichissement dans `pipeline.py` afin de prendre en charge l'ensemble des IOC (IP, domaines, URL, hashes, etc.), et non uniquement les adresses IP.
