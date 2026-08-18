---
techno: Odoo / PostgreSQL
concept: Optimisation de requêtes & ORM Performance
statut: En cours
date_creation: 2026-06-23
---

# ⚡ Optimisation PostgreSQL & ORM Odoo

## 🎯 Objectif principal
Être le profil de confiance capable de résoudre les problèmes de lenteur critiques sur des bases de données de production volumineuses (indispensable pour les environnements d'envergure internationale).

## 📝 Notes & Synthèse
- **Problème du N+1 :** Éviter absolument de manipuler le `browse()` ou de lire des champs relationnels à l'intérieur d'une boucle `for record in self`. Cela multiplie les requêtes SQL inutiles.
- **Requêtes de masse :** Utiliser préférentiellement `search_read()` pour extraire des listes de dictionnaires lisses, ou `read_group()` pour déléguer les calculs d'agrégation (sommes, moyennes) directement au moteur PostgreSQL.
- **Indexation :** Placer un paramètre `index=True` sur les champs personnalisés lourds fréquemment ciblés par les filtres de recherche (`domain`).

### Bonne pratique ORM vs Mauvaise pratique :
```python
# ❌ MAUVAIS : Génère une requête SQL par ligne de paie
for ligne in self.lines:
    total += ligne.montant

#  BON : Une seule requête SQL optimisée côté base de données
donnees_groupees = self.env['hr.payslip.line'].read_group([('id', 'in', self.ids)], ['montant'], [])
🛠️ Application Pratique
[ ] Activer les logs de requêtes lentes dans ton environnement odoo.conf de dev.

[ ] Inspecter une requête lourde à l'aide de la commande SQL EXPLAIN ANALYZE.