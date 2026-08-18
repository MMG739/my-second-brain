---
techno: Python
concept: Clean Code, POO Avancée & Design Patterns
statut: À apprendre
date_creation: 2026-06-23
---

# 🐍 Python Avancé, Programmation Orientée Objet & Clean Code

## 🎯 Objectif principal
Passer un cap majeur en ingénierie logicielle. Écrire un code Python tellement propre, modulaire et structuré que n'importe quelle équipe tech internationale (USA, Europe) pourra s'y intégrer immédiatement. Préparer le terrain pour la certification PCPP.

## 📝 Notes & Synthèse

### 1. Les Fondations SOLID (Qualité d'Architecture)
- **S (Single Responsibility) :** Une fonction ou une classe ne doit faire qu'une seule et unique chose.
- **O (Open/Closed) :** Ton code doit être facile à étendre sans qu'on ait besoin de modifier son cœur existant.
- **L (Liskov Substitution) :** Une classe enfant doit pouvoir remplacer sa classe parente sans tout casser.
- **I (Interface Segregation) :** Préférer plusieurs petites interfaces ciblées plutôt qu'une immense usine à gaz.
- **D (Dependency Inversion) :** Ton code de haut niveau doit dépendre d'abstractions, pas de détails techniques rigides.

### 2. Notions Python Cruciales
- **Décorateurs :** Fonctions qui enveloppent d'autres fonctions pour ajouter du comportement (logs, sécurité, cache) sans dupliquer le code.
- **Context Managers (`with`) :** Automatiser l'ouverture et la fermeture propre des ressources (fichiers, connexions).

```python
# Exemple de Décorateur simple pour Logger une action
def log_action(func):
    def wrapper(*args, **kwargs):
        print(f"Lancement de la fonction : {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_action
def calculer_paie():
    pass
    
```

## 🛠️ Application Pratique

- [ ] Reprendre un ancien script ou module et le réécrire en appliquant rigoureusement les principes SOLID.
    
- [ ] Configurer un framework de test comme `pytest` pour automatiser la vérification de ton code.