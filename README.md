# Copilot Planner

Copilot Planner est une extension open source qui permet de planifier et d’exécuter des tâches de développement étape par étape, directement depuis GitHub Copilot Chat. Elle vise à automatiser la gestion des TODO, l’analyse de codebase, et l’exécution séquentielle des tâches pour accélérer le développement collaboratif.

## 🚀 Fonctionnalités principales

- **Planification intelligente** : Génère automatiquement un plan d’action détaillé à partir d’une demande utilisateur.
- **Analyse de codebase** : Détecte la structure, les technologies et les dépendances du projet.
- **Gestion avancée des TODO** : Crée, met à jour et coche les tâches dans un fichier `todo.md` au format standardisé.
- **Exécution guidée** : Exécute les tâches une à une, avec validation utilisateur pour les étapes importantes.
- **Intégration GitHub** : Peut créer/modifier des fichiers, ouvrir des pull requests, et interagir avec les issues (v2).

## 📦 Installation

1. **Cloner le dépôt**
   ```sh
   git clone https://github.com/votre-utilisateur/copilot-planner.git
   cd copilot-planner
   ```
2. **Installer les dépendances**
   ```sh
   pnpm install
   ```
3. **Configurer l’extension**
   - Suivre les instructions de configuration de la GitHub App dans le fichier `todo.md`.

## 🛠️ Utilisation

Dans GitHub Copilot Chat, lancez une planification :

```
@copilot-planner #plananddo créer une API REST pour gérer les utilisateurs avec authentification JWT
```

L’extension analysera le projet, générera un plan dans `todo.md` et exécutera les tâches étape par étape.

## 🧩 Architecture

- `src/` : Code source principal
  - `tools/` : Outils (planification, analyse, gestion TODO)
  - `utils/` : Utilitaires (fichiers, parsing markdown)
- `todo.md` : Plan d’exécution et suivi des tâches
- `README.md` : Ce fichier

## 🤝 Contribution

Les contributions sont les bienvenues ! Pour contribuer :

1. Forkez le projet
2. Créez une branche (`feature/ma-fonctionnalite`)
3. Commitez vos modifications
4. Ouvrez une Pull Request

Merci de respecter le format du code et d’ajouter des tests si nécessaire.

## 📄 Licence

Ce projet est sous licence MIT. Voir le fichier [LICENSE](LICENSE).

## 🙏 Remerciements

- [GitHub Copilot](https://github.com/features/copilot)
- [Octokit](https://github.com/octokit/octokit.js)
- La communauté open source ❤️
