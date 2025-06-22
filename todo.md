## 🎯 **Copilot Planner Extension - TODO List**

### 1. Initialisation du projet

- [x] Créer le dossier `copilot-planner`
- [ ] Initialiser le projet :
  ```sh
  pnpm init
  pnpm add -D typescript ts-node @types/node
  npx tsc --init
  ```
- [ ] Créer la structure :
  ```
  copilot-planner/
    src/
      index.ts
      tools/
        planAndDo.ts
        codebaseAnalyzer.ts
        todoManager.ts
      utils/
        fileUtils.ts
        markdownParser.ts
    package.json
    README.md
    .gitignore
  ```

### 2. Installation des dépendances spécifiques

- [ ] SDK Copilot et outils GitHub :
  ```sh
  pnpm add @copilot-extensions/preview-sdk @octokit/core @octokit/rest
  ```
- [ ] Outils pour l'analyse de code :
  ```sh
  pnpm add glob fast-glob ignore
  pnpm add -D @types/glob
  ```

### 3. Implémentation du tool principal `#plananddo`

- [ ] **Créer le tool planAndDo** (`src/tools/planAndDo.ts`) :

```typescript
import { Tool } from "@copilot-extensions/preview-sdk";

export const planAndDoTool: Tool = {
  name: "plananddo",
  description: "Analyse le projet, crée un plan dans todo.md et exécute les tâches étape par étape",
  parameters: {
    type: "object",
    properties: {
      userRequest: {
        type: "string",
        description: "La demande de l'utilisateur à planifier et exécuter",
      },
      projectPath: {
        type: "string",
        description: "Chemin vers le projet (optionnel, détecté automatiquement)",
      },
    },
    required: ["userRequest"],
  },
  handler: async (params, context) => {
    // 1. Analyser la codebase
    const codebaseAnalysis = await analyzeCodebase(params.projectPath);

    // 2. Créer le plan dans todo.md
    const todoList = await createTodoPlan(params.userRequest, codebaseAnalysis);

    // 3. Exécuter les tâches une par une
    return await executePlanStepByStep(todoList, context);
  },
};
```

### 4. Analyseur de codebase

- [ ] **Créer l'analyseur** (`src/tools/codebaseAnalyzer.ts`) :

```typescript
export interface CodebaseAnalysis {
  structure: FileStructure[];
  technologies: string[];
  mainFiles: string[];
  dependencies: Record<string, string>;
  readme?: string;
}

export async function analyzeCodebase(projectPath?: string): Promise<CodebaseAnalysis> {
  // 1. Détecter la structure du projet
  // 2. Identifier les technologies (package.json, requirements.txt, etc.)
  // 3. Lister les fichiers principaux
  // 4. Analyser les dépendances
  // 5. Lire le README si présent
}
```

### 5. Gestionnaire de TODO

- [ ] **Créer le gestionnaire** (`src/tools/todoManager.ts`) :

```typescript
export interface TodoTask {
  id: string;
  description: string;
  completed: boolean;
  priority: "high" | "medium" | "low";
  dependencies?: string[];
  estimatedTime?: string;
}

export class TodoManager {
  async createTodoFile(tasks: TodoTask[]): Promise<void> {
    // Créer/mettre à jour todo.md avec format markdown
  }

  async markTaskCompleted(taskId: string): Promise<void> {
    // Cocher une tâche dans todo.md
  }

  async getNextTask(): Promise<TodoTask | null> {
    // Récupérer la prochaine tâche non complétée
  }
}
```

### 6. Agent principal

- [ ] **Créer l'agent** (`src/index.ts`) :

```typescript
import { createAgent } from "@copilot-extensions/preview-sdk";
import { serve } from "@copilot-extensions/preview-sdk/serve";
import { planAndDoTool } from "./tools/planAndDo";

const agent = createAgent({
  name: "Copilot Planner",
  description: "Extension pour planifier et exécuter des tâches de développement étape par étape",
  tools: [planAndDoTool],
  systemPrompt: `
    Tu es Copilot Planner, un assistant qui aide à planifier et exécuter des tâches de développement.
    
    Quand un utilisateur utilise #plananddo :
    1. Analyse d'abord entièrement la codebase
    2. Crée un plan détaillé dans todo.md
    3. Exécute les tâches une par une en les cochant
    4. Demande validation avant chaque étape importante
  `,
});

serve(agent, { port: process.env.PORT || 3000 });
```

### 7. Configuration GitHub App

- [ ] Créer une GitHub App sur https://github.com/settings/apps
- [ ] Configurer :
  - **Nom** : `copilot-planner`
  - **Description** : "Plan and execute development tasks step by step"
  - **Homepage URL** : URL de votre repo
  - **Callback URL** : URL de votre serveur déployé
  - **Permissions** :
    - GitHub Copilot Chat: Read-only
    - Contents: Read & Write (pour créer/modifier todo.md)
    - Pull requests: Write (pour créer des PRs si nécessaire)
  - **Type** : Agent
  - **Agent URL** : URL de votre endpoint

### 8. Format du fichier todo.md

- [ ] **Définir le format standard** :

```markdown
# 📋 Plan d'exécution - [Date/Heure]

## 🎯 Objectif

[Description de la demande utilisateur]

## 📊 Analyse du projet

- **Technologies détectées** : React, TypeScript, Node.js
- **Structure principale** : src/, components/, utils/
- **Fichiers clés** : package.json, tsconfig.json, README.md

## ✅ Tâches à réaliser

### Phase 1 : Préparation

- [x] ~~Analyser la structure du projet~~ ✅ Terminé
- [ ] 🔄 **EN COURS** : Installer les dépendances manquantes
- [ ] Configurer l'environnement de développement

### Phase 2 : Développement

- [ ] Créer le composant principal
- [ ] Implémenter la logique métier
- [ ] Ajouter les tests unitaires

### Phase 3 : Finalisation

- [ ] Mettre à jour la documentation
- [ ] Tester l'intégration
- [ ] Créer la pull request

## 📝 Notes d'exécution

- [16:25] Analyse terminée - 15 fichiers TypeScript détectés
- [16:30] Installation en cours...
```

### 9. Workflow d'exécution

- [ ] **Implémenter le workflow** :

```typescript
async function executePlanStepByStep(todoList: TodoTask[], context: any) {
  for (const task of todoList) {
    if (task.completed) continue;

    // 1. Afficher la tâche en cours
    await context.stream(`🔄 **Exécution** : ${task.description}`);

    // 2. Demander confirmation si tâche importante
    if (task.priority === "high") {
      const confirmation = await context.confirm(`Procéder à : ${task.description} ?`);
      if (!confirmation) continue;
    }

    // 3. Exécuter la tâche (déléguer à Copilot ou outils)
    const result = await executeTask(task, context);

    // 4. Marquer comme terminée
    await todoManager.markTaskCompleted(task.id);

    // 5. Rapport de progression
    await context.stream(`✅ Terminé : ${task.description}`);
  }
}
```

### 10. Tests et déploiement

- [ ] **Tests locaux** :
  ```sh
  # Test avec ngrok
  npx ngrok http 3000
  ```
- [ ] **Déployer sur Railway/Vercel** :
  ```sh
  # Railway
  railway login
  railway init
  railway up
  ```

### 11. Utilisation

- [ ] **Dans Copilot Chat** :
  ```
  @copilot-planner #plananddo créer une API REST pour gérer les utilisateurs avec authentification JWT
  ```

### 12. Fonctionnalités futures (v2)

- [ ] Intégration avec les issues GitHub
- [ ] Templates de plans prédéfinis
- [ ] Estimation automatique des temps
- [ ] Génération de rapports de progression
- [ ] Support des dépendances entre tâches

## 🚀 Points clés pour votre extension

1. **Nom d'invocation** : `@copilot-planner #plananddo`
2. **Fichier central** : `todo.md` créé à la racine du projet
3. **Progression visuelle** : Tâches cochées en temps réel
4. **Confirmation utilisateur** : Demande validation avant actions importantes
5. **Analyse contextuelle** : Comprend le projet avant de planifier

Cette approche vous permettra de créer une extension puissante qui transforme les demandes vagues en plans d'action structurés et exécutables !
