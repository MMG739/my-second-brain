---
techno: Odoo / Javascript
concept: Odoo Web Library (OWL)
statut: En cours
date_creation: 2026-06-23
---

# 🛠️ Odoo OWL Framework (Odoo Web Library)

## 🎯 Objectif principal
Devenir totalement autonome sur le frontend d'Odoo. Être capable de concevoir des interfaces graphiques complexes, des vues sur mesure et des dashboards ultra-dynamiques sans subir les limites des vues XML classiques.

## 📝 Notes & Synthèse
- OWL est le framework moderne d'Odoo, fortement inspiré de React et Vue (architecture par composants).
- Fonctionne avec un **Virtual DOM** pour optimiser les rendus et gère un cycle de vie précis (`setup`, `onWillStart`, `onMounted`).
- **Gestion de l'état :** Utilisation du hook `useState` pour lier les données de l'interface à la logique Javascript.

### Structure d'un composant OWL standard :
```javascript
import { Component, useState } from "@odoo/owl";

export class MonComposantPerso extends Component {
    setup() {
        // Déclaration d'un état réactif
        this.state = useState({ compteur: 0 });
    }
    incrementer() {
        this.state.compteur++;
    }
}
MonComposantPerso.template = "mon_addon.MonComposantTemplate";

```

## 🛠️ Application Pratique

- [ ] Créer un petit module Odoo local contenant un widget OWL personnalisé.
    
- [ ] Connecter ce widget à une méthode Python du backend via un appel `rpc`.