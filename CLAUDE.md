# Règles d'activation des skills et plugins

## Principe fondamental

Chaque skill disponible a une fonction précise. Tu l'utilises dès que la situation le justifie, sans attendre que l'utilisateur te le demande explicitement. Si un skill couvre ta tâche en cours, tu l'invoques avant d'agir.

---

## Quand utiliser chaque skill

### superpowers — socle de toute session
- `brainstorming` : avant de créer une feature, un composant, ou modifier un comportement existant
- `systematic-debugging` : dès qu'un bug, une erreur de test, ou un comportement inattendu apparaît
- `test-driven-development` : avant d'écrire du code d'implémentation pour une feature ou un bugfix
- `writing-plans` : dès qu'une tâche nécessite plusieurs étapes ou fichiers
- `executing-plans` : quand tu as un plan écrit à exécuter
- `verification-before-completion` : avant de dire que c'est terminé, avant tout commit ou PR
- `requesting-code-review` : quand tu termines une feature importante ou avant un merge
- `receiving-code-review` : quand tu reçois du feedback de revue, avant d'implémenter
- `using-git-worktrees` : avant de démarrer du travail de feature qui nécessite isolation
- `dispatching-parallel-agents` : dès que 2+ tâches indépendantes peuvent être parallélisées
- `subagent-driven-development` : pour exécuter des plans avec tâches indépendantes dans la session courante
- `finishing-a-development-branch` : quand l'implémentation est complète et qu'il faut intégrer

### spec-kit — spécification avant le code
Activer dès que : feature complexe, multi-jours, ambiguïté sur le périmètre, plusieurs intervenants.
Phases : `/constitution` → `/specify` → `/clarify` → `/plan` → `/tasks` → implémentation.
Ne pas toucher au code avant que la spec soit validée.

### commit-commands — livraison git propre
- `/commit` : après que `verification-before-completion` a été exécuté et validé
- `/commit-push-pr` : pour enchaîner push + ouverture de PR en une commande

### code-review — revue multi-agents
Utiliser `/review` dès qu'une PR mérite une revue structurée. Ce plugin complète les skills `requesting-code-review` et `receiving-code-review` de superpowers — pas de conflit, usage complémentaire.

### security-guidance — hook passif
Actif automatiquement sur chaque `Write` et `Edit`. Aucune action requise. Si le hook remonte un avertissement, le traiter immédiatement avant de continuer.

### frontend-design — toute tâche UI
Activer sur : composants, pages, dashboards, landing pages, tout ce qui est rendu dans un navigateur. Objectif : interfaces distinctives production-grade, pas des grilles grises génériques.

### theme-factory — identité visuelle cohérente
Activer sur : livrables visuels avec une identité de marque (docs, présentations, rapports, sites). Complémentaire à frontend-design : frontend-design produit le code des composants, theme-factory définit l'identité globale.

### graphify — cartographie de corpus dense
Activer avec `/graphify` dès que : nouveau codebase inconnu, architecture à comprendre, corpus de documents à synthétiser. À faire avant de toucher au code sur un projet non familier.

### context7 — documentation à jour
Utiliser `use context7` dans les prompts dès qu'une tâche implique une bibliothèque dont les APIs évoluent : Next.js, React, Tailwind, Shadcn, Prisma, et tout ce qui change rapidement. Critique pour éviter les hallucinations d'API.

---

## Ordre de priorité quand plusieurs skills s'appliquent

1. **Skills de processus en premier** (brainstorming, systematic-debugging, writing-plans) — ils définissent comment aborder la tâche
2. **Skills d'implémentation ensuite** (frontend-design, spec-kit, TDD) — ils guident l'exécution
3. **Skills de livraison en dernier** (verification-before-completion, commit, code-review)

---

## Flux type d'une session

```
Nouvelle tâche reçue
    │
    ├─ Feature complexe ou ambiguë ?
    │       └─ spec-kit : spec d'abord, code ensuite
    │
    ├─ Feature courte et claire ?
    │       └─ brainstorming → TDD → implémentation → verification-before-completion
    │
    ├─ Bug ou comportement inattendu ?
    │       └─ systematic-debugging avant toute proposition de fix
    │
    ├─ Codebase inconnu ?
    │       └─ graphify pour mapper, context7 pour la doc
    │
    ├─ Tâche frontend ?
    │       └─ frontend-design systématiquement
    │               └─ theme-factory si livrable avec identité visuelle
    │
    └─ Fin de feature ?
            └─ verification-before-completion → commit → code-review si PR importante
```

---

## Ce qui ne nécessite pas d'action explicite

- `security-guidance` — hook PreToolUse, toujours actif, traiter ses avertissements immédiatement
- `rtk` — binaire installé, optimise les appels Bash en arrière-plan
- `context7` — MCP disponible, à invoquer explicitement avec `use context7` quand une lib évolue vite
