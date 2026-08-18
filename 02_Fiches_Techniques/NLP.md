
[Lien TP](C:\Projects\TPNLP)
## 📂 CHAPITRE 1 : Architecture Logicielle & Prétraitement / Nettoyage

### 1.1 Les 5 Principes SOLID (Clean Code)
* **S - Single Responsibility (SRP) :** Une classe ou fonction doit avoir une seule et unique responsabilité.
* **O - Open/Closed (OCP) :** Ouvert à l'extension, fermé à la modification. On configure via des paramètres plutôt que de dupliquer ou réécrire le code de base.
* **L - Liskov Substitution (LSP) :** Une classe fille doit pouvoir remplacer sa classe mère sans casser l'application.
* **I - Interface Segregation (ISP) :** Préférer plusieurs petites interfaces spécifiques à une seule grosse interface générale.
* **D - Dependency Inversion (DIP) :** Dépendre des abstractions (interfaces), pas des implémentations concrètes.

### 1.2 Structure d'un Package Python Déployable (`src/` layout)
```text
mon_package_nlp/
├── pyproject.toml        # Métadonnées, système de build et dépendances
└── src/
    └── nlp_loader/
        ├── __init__.py   # Expose les fonctions publiques via __all__
        └── loaders.py    # Contient les fonctions de nettoyage de code
````

- **Mode Éditable :** `pip install -e .` permet de modifier le code source et de voir les changements en temps réel sans devoir réinstaller le package.
    
- **Bloc Gardien :** `if __name__ == "__main__":` isole le code de test local pour qu'il ne s'exécute pas lors d'un import classique chez l'utilisateur final.
    

### 1.3 Script de Nettoyage de Texte Optimisé (`clean_text`)

Ce script applique un traitement obligatoire (minuscules, sauts de ligne, et marqueurs Project Gutenberg) tout en gérant dynamiquement les choix de l'utilisateur pour les nombres et les mots composés grâce à une reconstruction de regex à la volée.

Python

```
import re

def clean_text(text: str, preserve_compound_words: bool = False, remove_isolated_numbers: bool = True) -> str:
    """
    Nettoie et normalise un texte pour un pipeline NLP en français.
    """
    # 1. Traitements obligatoires systématiques
    text = text.lower()
    text = re.sub(r'_([^_]+)_', r'\1', text)  # Suppression obligatoire des italiques Gutenberg
    text = re.sub(r'[\n\r\t]+', ' ', text)
    
    # 2. Traitements optionnels
    if preserve_compound_words:
        text = re.sub(r'(?<=[a-zàâçéèêëîïôûùüÿñæœ])-(?=[a-zàâçéèêëîïôûùüÿñæœ])', '_', text)
    if remove_isolated_numbers:
        text = re.sub(r'\b\d+\b', ' ', text)
        
    # 3. Construction dynamique de la Regex de filtrage
    allowed_chars = "a-zàâçéèêëîïôûùüÿñæœ'"
    if preserve_compound_words: 
        allowed_chars += "_"
    if not remove_isolated_numbers: 
        allowed_chars += "0-9"
        
    text = re.sub(f"[^{allowed_chars} ]", ' ', text)
    text = re.sub(r"[''`]", "'", text)  # Normalisation des apostrophes typographiques
    return re.sub(r'\s+', ' ', text).strip()
```

### 1.4 Prétraitement Linguistique : Lemmatisation vs Stemming

- **Lemmatisation :** Approche linguistique basée sur un dictionnaire et l'analyse grammaticale (POS Tagging). Renvoie la forme canonique du mot appelée lemme ($chevaux \rightarrow cheval$). Précis mais plus lent.
    
- **Racinisation (Stemming) :** Approche algorithmique brute qui coupe les affixes et suffixes. Renvoie un radical qui peut ne pas exister dans la langue ($chevaux \rightarrow chev$). Ultra-rapide.
    
- **Le Trio d'Outils :** `re` (Regex brutes), `NLTK` (Académique/Historique), et `SpaCy` (Industriel, ultra-rapide via pipelines de traitement automatisés).
    

## 📂 CHAPITRE 2 : Machine Learning Non Supervisé & Statistiques

### 2.1 Isolation Forest (Détection d'anomalies)

- **Principe :** Au lieu de modéliser les données normales pour chercher ce qui en sort, cet algorithme isole explicitement les anomalies.
    
- **Mécanisme :** Il construit des arbres de décision de manière aléatoire. Les anomalies (points extrêmes et rares) se retrouvent isolées très rapidement près de la racine (chemin court). Les points normaux demandent beaucoup plus de découpages (chemin long).
    

### 2.2 Estimateur à Noyau de Parzen (KDE - Kernel Density Estimation)

- **Principe :** Méthode non paramétrique permettant d'estimer la fonction de densité de probabilité d'une variable continue sans présupposer sa loi d'origine, en lissant les données par rapport à un histogramme.
    
- **La Fenêtre $h$ (Bandwidth) :** Paramètre critique de lissage (Compromis Biais-Variance).
    
    - $h$ trop petit $\rightarrow$ Sous-lissage (Forte variance, courbe en dents de scie, surapprentissage).
        
    - $h$ trop grand $\rightarrow$ Sur-lissage (Fort biais, courbe trop plate, sous-apprentissage).
        

### 2.3 Statistiques Inférentielles : Les Tests de Rang

Utilisés lorsque les données ne suivent pas une loi normale ou que les tailles d'échantillons sont trop faibles. Ils transforment les valeurs réelles en classements ordonnés (rangs).

|**Cas d'usage**|**Test de Rang (Non paramétrique)**|**Équivalent Paramétrique (Loi Normale)**|
|---|---|---|
|**2 groupes indépendants**|Test de Mann-Whitney (U-Test)|Test t de Student indépendant|
|**2 groupes appariés (Avant/Après)**|Test des Rangs Signés de Wilcoxon|Test t de Student apparié|
|**$\ge 3$ groupes indépendants**|Test de Kruskal-Wallis|ANOVA|

> ⚖️ **Règle de décision :** Si la $\text{p-value} < 0.05$, on rejette l'hypothèse nulle ($H_0$), prouvant une différence statistiquement significative entre les groupes.

### 2.4 Stratégies de Validation & Ensemble Learning

- **Overfitting (Surapprentissage) :** Le modèle apprend par cœur le bruit du jeu d'entraînement. Le taux d'erreur est excellent sur le `train set` mais catastrophique sur le `test set`. Validé par **Cross-Validation** (découpage en $K$ blocs).
    
- **Stratification :** Lors du split, elle force le respect des proportions des classes de la variable cible dans le `train` et le `test` (crucial pour les données déséquilibrées).
    
- **Ensemble Learning (Règles de Vote) :**
    
    - _Vote Majoritaire (Hard Voting) :_ La classe ayant reçu le plus de votes parmi les modèles gagne.
        
    - _Vote Pondéré (Soft Voting) :_ Moyenne des probabilités (`predict_proba`) de chaque classe. Plus fin.
        
    - _Stacking :_ Les prédictions des modèles de base servent de variables d'entrée (_features_) pour un méta-modèle final.
        

## 📂 CHAPITRE 3 : L'Évolution de la Vectorisation (Du mot au contexte)

### 3.1 Vectorisation Statistique (Fréquentielle)

- **Bag of Words (Sac de mots) :** Matrice de comptage brute. Vecteurs géants et creux (_sparse_). Perd complètement l'ordre et le contexte.
    
- **Les N-grammes :** Séquences continues de $N$ mots consécutifs. Redonnent un contexte local ($"ne" + "pas" \rightarrow "ne\_pas"$). Formule pour une phrase de $X$ mots :
    
    $$\text{Nombre de N-grammes} = X - N + 1$$
    
- **TF-IDF :** Donne du poids aux mots. Il augmente si un mot est fréquent dans un document précis (**TF**), mais diminue s'il est trop commun dans l'ensemble du corpus (**IDF**), neutralisant l'impact des mots vides (_le, la, et_).
    

### 3.2 L'Hypothèse Distributionnelle & Les Embeddings Statiques (1ère Génération)

> _"You shall know a word by the company it keeps" (Firth, 1957)._ Les mots apparaissant dans des contextes similaires partagent un sens proche. Permet de créer des vecteurs denses et réduits (ex: 300 dimensions) capables de gérer des relations linéaires : $\overrightarrow{\text{Roi}} - \overrightarrow{\text{Homme}} + \overrightarrow{\text{Femme}} \approx \overrightarrow{\text{Reine}}$.

- **Word2Vec (Google, 2013) :** Apprentissage local via une fenêtre glissante.
    
    - _CBOW :_ Prédit le mot cible à partir du contexte environnant (rapide, adapté aux mots fréquents).
        
    - _Skip-Gram :_ Prédit le contexte environnant à partir d'un seul mot cible (performant sur les mots rares).
        
- **GloVe (Stanford, 2014) :** Approche globale. Factorisation de la matrice complète de cooccurrence de tout le corpus en se basant sur le rapport de probabilités logarithmiques.
    
- **FastText (Facebook, 2016) :** Décompose chaque mot en un sac de n-grammes de caractères (ex: $<manger> \rightarrow <ma, man, ang...$).
    
    - _Révolution :_ Résout le problème des mots inconnus (**Out-Of-Vocabulary**) et des fautes de frappe en générant un vecteur par somme des sous-mots.
        

> ⚠️ **Limite critique des embeddings statiques :** Un mot = un seul vecteur fixe. Impossible de gérer la polysémie (ex: un _avocat_ en droit vs le fruit).

### 3.3 Le Tokeniseur Moderne : Le Byte-Pair Encoding (BPE)

Avant d'entrer dans un Transformer, le texte brut passe par un **Tokeniseur**.

- **Le concept :** Le BPE trouve le juste milieu entre le découpage par caractères (trop long) et par mots entiers (problème des mots inconnus). Il commence par lister tous les caractères individuels, puis fusionne itérativement les paires de caractères ou de sous-mots les plus fréquentes du corpus pour construire son dictionnaire de **Tokens**.
    
- **Comportement :** Les mots fréquents restent entiers ($"manger" \rightarrow [manger]$), tandis que les mots rares, complexes ou les flexions de conjugaison sont découpés en sous-mots ($"dé-mangerg" \rightarrow [dé], [manger], [g]$).
    
- **Importance en Master 2 :** C'est le BPE qui définit la taille de la **Context Window** d'un LLM. Un token équivaut environ à 4 caractères ou 0,75 mot en anglais.
    

## 📂 CHAPITRE 4 : L'Ère des Transformers, Vision & Écosystème LLM

### 4.1 Le Mécanisme de Self-Attention

L'opération mathématique d'algèbre linéaire qui a remplacé les réseaux récurrents (RNN/LSTM) en permettant un calcul 100% parallèle sur GPU. Chaque mot regarde tous les autres mots d'une phrase pour enrichir son propre vecteur en fonction du contexte.

- **Les 3 vecteurs :** Les requêtes (**Query** $Q$), les clés (**Key** $K$) et les valeurs (**Value** $V$).
    
    $$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V$$
    

### 4.2 L'Architecture Transformer

Le plan de conception complet introduit en 2017. L'Attention pure étant aveugle à l'ordre des mots, le Transformer y ajoute un module de **Positional Encoding** (fonctions sinusoïdales), des couches **Feed-Forward**, des **connexions résiduelles** et des couches de normalisation pour stabiliser la descente de gradient avec l'optimiseur **Adam**.

- **Différence Transformer vs LLM :** Le Transformer est l'architecture de base (le moteur). Le LLM (Large Language Model) est l'application industrielle massive de cette architecture (milliards de paramètres, entraîné sur le web).
    

### 4.3 Typologie des Modèles : BERT, GPT, Llama

- **BERT (Google) :** Basé uniquement sur l'**Encodeur**. Lecture **bidirectionnelle**. Idéal pour la **compréhension** et la classification.
    
- **GPT-4 (OpenAI) :** Basé uniquement sur le **Décodeur**. Lecture **autorégressive/causale** (gauche à droite). Modèle propriétaire géant idéal pour la **génération** et le raisonnement.
    
- **Llama (Meta) :** Basé sur le **Décodeur**. Équivalent **Open-Source** de GPT, permettant un hébergement et un fine-tuning privés en entreprise.
    

### 4.4 Modèles Hybrides & Vision par Ordinateur

- **VGG16 (Classification CNN) :** Réseau convolutif classique combinant des filtres $3 \times 3$ et du Max-Pooling pour extraire les caractéristiques spatiales d'une image.
    
- **YOLO (Détection One-Stage) :** Divise l'image en une grille $S \times S$ pour prédire en un seul passage GPU les classes et les _Bounding Boxes_. Utilise l'**IoU** (taux de superposition) et le **NMS** (Non-Maximum Suppression) pour nettoyer les boîtes redondantes.
    
- **Modèles Hybrides (CNN + LSTM) :** VGG16 extrait le vecteur sémantique d'une image/vidéo (features visuelles), et le LSTM prend ce vecteur en contexte pour générer une description textuelle mot après mot.
    

### 4.5 Les Bases de Données Vectorielles (Vector DB) & L'Architecture RAG

Pour exploiter un LLM sur des données spécifiques sans réentraîner ses milliards de paramètres, on utilise le **RAG (Retrieval-Augmented Generation)**.

- **Le concept :** On découpe les documents de l'entreprise en blocs (_chunks_), on les transforme en vecteurs denses via un modèle d'embedding, et on les stocke dans une **Vector DB** (ex: _ChromaDB, Pinecone, Milvus_).
    
- **Le Pipeline RAG :** 1. L'utilisateur pose une question.
    
    2. La Vector DB effectue une recherche de similarité (calcul de la **distance cosinus**) pour extraire les blocs de texte les plus proches de la question.
    
    3. Ces blocs sont injectés comme "contexte" dans le prompt envoyé au LLM.
    
    4. Le LLM génère une réponse exacte et à jour, éliminant les hallucinations.
    

### 4.6 L'Optimisation de l'Apprentissage : Descente de Gradient & Alignement

- **Moteur d'apprentissage :** La descente de gradient met à jour les poids via le Learning Rate $\eta$. L'optimiseur **Adam** accélère le processus en combinant le **Momentum** (inertie pour franchir les minimums locaux) et le **RMSProp** (adaptation du $\eta$ pour chaque poids).
    
- **Fine-tuning :** Soit global avec un $\eta$ minuscule ($10^{-5}$) pour éviter l'oubli catastrophique, soit léger via **LoRA** (injection de mini-matrices de poids à côté de l'attention).
    
- **L'Évolution de l'Alignement Humain :**
    
    - _RLHF (Historique) :_ On entraîne un second réseau de neurones appelé "Modèle de Récompense" (_Reward Model_) basé sur les préférences humaines, puis on applique du renforcement sur le LLM. Très lourd et instable.
        
    - _DPO (Direct Preference Optimization - Moderne) :_ Révolution mathématique qui élimine le modèle de récompense. Le DPO formule l'alignement directement comme un problème de classification sur des paires de données (Réponse Préférée vs Réponse Rejetée), permettant d'aligner le modèle en une seule étape stable et rapide.
        

### 4.7 Les Métriques d'Évaluation en Master 2 Data/IA

On ne peut pas évaluer la génération de texte avec une simple Accuracy. On utilise des métriques spécifiques :

|**Métrique**|**Type d'application**|**Fonctionnement**|
|---|---|---|
|**BLEU**|Traduction / Génération|Calcule le taux de précision des N-grammes partagés entre le texte généré et une traduction humaine de référence.|
|**ROUGE**|Résumé de texte|Calcule le taux de rappel (_Recall_). Mesure si les mots importants de la référence humaine ont bien été capturés par le LLM.|
|**LLM-as-a-Judge**|Évaluation Générale / RAG|Utilisation d'un LLM de pointe (ex: GPT-4) guidé par un prompt de notation ultra-strict pour évaluer la fidélité, la pertinence et la justesse des réponses de modèles plus petits.|

## 📂 CHAPITRE 5 : DIOA & Automatisation des Bases de Données

### 5.1 Qu'est-ce que le DIOA ?

Le **DIOA (Data Input/Output Automation)** regroupe les techniques, scripts et architectures permettant d'automatiser l'intégration (Input), le traitement interne et la restitution/réplication (Output) des données au sein d'un écosystème de bases de données, sans intervention humaine.

### 5.2 Les 3 Piliers de l'Automatisation I/O

- **L'Automatisation des Inputs (Flux Entrants) :** Traitement via des outils de flux comme **n8n** ou Apache Airflow. Utilisation du **CDC (Change Data Capture)** pour écouter les logs transactionnels d'une base source et répliquer immédiatement chaque modification sans requêter en masse via des `SELECT *`.
    
- **L'Automatisation Interne (Traitements & Triggers) :** * _Les Triggers (Déclencheurs) :_ Blocs de code (PL/pgSQL) exécutés automatiquement avant/après un `INSERT`, `UPDATE` ou `DELETE` pour mettre à jour des statuts ou des tables d'audit.
    
    - _Jobs Planifiés :_ Fonctions complexes exécutées à intervalle régulier (via `pg_cron`) pour archiver ou recalculer des statistiques.
        
- **L'Automatisation des Outputs (Flux Sortants) :** Rafraîchissement automatisé des **Vues Matérialisées** (_Materialized Views_) pour stocker physiquement le résultat de calculs lourds, et mise en place de la réplication vers des serveurs esclaves (_Replica_).
    

### 5.3 Exemple Concret : Automatisation PL/pgSQL (PostgreSQL)

SQL

```
-- 1. Création de la fonction qui va automatiser la journalisation
CREATE OR REPLACE FUNCTION log_changement_salaire()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.salaire <> OLD.salaire THEN
        INSERT INTO audit_salaires(employe_id, ancien_salaire, nouveau_salaire, date_modification)
        VALUES(OLD.id, OLD.salaire, NEW.salaire, NOW());
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 2. Création du trigger qui écoute la table des employés
CREATE TRIGGER trigger_salaire_automatique
AFTER UPDATE ON employes
FOR EACH ROW
EXECUTE FUNCTION log_changement_salaire();
```

## 📂 CHAPITRE 6 : Déploiement, Prototypage & Interfaces (PoC)

### 6.1 Qu'est-ce que Gradio ?

**Gradio** est une bibliothèque Python open-source qui permet de générer des interfaces web interactives pour tes modèles de Machine Learning, tes LLM ou tes scripts de traitement de données, **sans avoir besoin de connaissances en développement Web (HTML/CSS/JS)**. C'est l'outil idéal pour concevoir un prototype (_Proof of Concept_).

### 6.2 Les Deux Approches : Interface vs Blocks

- **`gr.Interface()` :** Approche simple pour les fonctions linéaires (Entrée $\rightarrow$ Traitement $\rightarrow$ Sortie). Gradio gère l'agencement automatiquement.
    
- **`gr.Blocks()` :** Approche avancée et modulaire fonctionnant sous forme de layouts (lignes et colonnes). Indispensable pour concevoir des applications complexes (dashboards multi-onglets, interfaces de chatbots avancées).
    

### 6.3 Exemple d'Interface Gradio pour ton Pipeline

Python

```
import gradio as gr

def pipeline_nlp(texte_brut, supprimer_nombres):
    if not texte_brut:
        return "Veuillez entrer du texte.", "N/A"
    texte_propre = texte_brut.lower().strip()
    if supprimer_nombres:
        import re
        texte_propre = re.sub(r'\b\d+\b', '', texte_propre)
    statut = f"Texte traité avec succès ({len(texte_propre)} caractères)"
    return texte_propre, statut

with gr.Blocks(title="Pipeline NLP - Master Data IA") as demo:
    gr.Markdown("# 📑 Prototype de Prétraitement NLP")
    with gr.Row():
        with gr.Column():
            input_text = gr.Textbox(label="Texte brut à analyser", placeholder="Entrez votre texte...", lines=5)
            check_num = gr.Checkbox(label="Supprimer les nombres isolés", value=True)
            btn_soumettre = gr.Button("Lancer le traitement", variant="primary")
        with gr.Column():
            output_clean = gr.Textbox(label="Texte nettoyé", interactive=False, lines=5)
            output_status = gr.Label(label="Statut du pipeline")
            
    btn_soumettre.click(fn=pipeline_nlp, inputs=[input_text, check_num], outputs=[output_clean, output_status])

if __name__ == "__main__":
    demo.launch(share=False)
```

### 6.4 Fonctionnalités Avancées pour la Démo

- **`share=True` :** Génère une URL publique sécurisée temporaire (active 72 heures) pour permettre à n'importe quel collaborateur ou examinateur de tester ton modèle tournant sur ta machine locale.
    
- **`gr.ChatInterface` :** Composant de haut niveau configuré spécifiquement pour connecter des modèles génératifs et reproduire une interface de discussion de type ChatGPT/Claude.