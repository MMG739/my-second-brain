---
techno: IA / Python
concept: Retrieval-Augmented Generation (RAG)
statut: À apprendre
date_creation: 2026-06-23
---

# 🧠 Concepts RAG (Retrieval-Augmented Generation) & Vector DB

## 🎯 Objectif principal
Mettre ton Master en IA au service du business en connectant concrètement la puissance des modèles de langage (LLM) aux données internes et hautement confidentielles d'un ERP ou d'une entreprise.



## 📝 Notes & Synthèse
- **Le problème résolu :** Un LLM classique ne connaît pas les données privées d'une boîte. Le RAG lui injecte le bon contexte en temps réel sans nécessiter un réentraînement lourd et coûteux.
- **Le Pipeline RAG :** Document source ➡️ Découpage en blocs (Chunking) ➡️ Transformation en vecteurs numériques (Embeddings) ➡️ Stockage dans une base de données vectorielle.
- **Bases de données vectorielles :** Utilisation de l'extension `pgvector` (pour rester dans l'écosystème PostgreSQL familier à Odoo) ou ChromaDB en local.

## 🛠️ Application Pratique
- [ ] Installer l'extension `pgvector` sur ton instance locale PostgreSQL (sous WSL2).
- [ ] Écrire un script Python autonome pour vectoriser une série de phrases de test et exécuter une recherche de similarité (Cosine Similarity).