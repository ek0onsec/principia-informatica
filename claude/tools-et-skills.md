# Mes outils Claude Code — Usage quotidien & Guide d'installation

> Rédigé du point de vue de Claude : ce sont les outils et skills que j'utilise activement dans chaque session de travail. Je décris ici à quoi ils me servent concrètement, quand les déclencher, et comment les grouper sans conflit.

---

## Vue d'ensemble rapide

| Outil | Type | Rôle principal |
|---|---|---|
| [superpowers](#superpowers) | Plugin / Skills framework | Méthodologie & discipline de dev |
| [spec-kit](#spec-kit) | Plugin / Skill | Dev piloté par la spec (7 phases) |
| [commit](#commit) | Plugin officiel | Automatisation git |
| [code-review](#code-review) | Plugin officiel | Revue de PR multi-agents |
| [security-guidance](#security-guidance) | Plugin officiel | Garde-fou sécurité automatique |
| [frontend-awesome](#frontend-awesome) | Skill | UI production-grade |
| [theme-factory](#theme-factory) | Skill | Thèmes visuels cohérents |
| [graphify](#graphify) | Skill / MCP | Graphes de connaissance |
| [context7](#context7) | MCP Server | Docs à jour injectées en contexte |
| [rtk](#rtk) | CLI hook | Réduction de tokens sur Bash |

---

## Les outils en détail

### superpowers

**Repo :** [obra/superpowers](https://github.com/obra/superpowers) · [obra/superpowers-marketplace](https://github.com/obra/superpowers-marketplace)

C'est mon socle. Superpowers n'est pas un simple plugin — c'est un framework de skills qui me donne une méthodologie structurée pour chaque type de tâche. Dès qu'une session démarre, il charge `using-superpowers` qui me force à chercher le bon skill avant de répondre à quoi que ce soit.

**Ce que ça change concrètement :**
- Avant de coder quoi que ce soit, je passe par `brainstorming` pour explorer l'intention réelle
- Quand un bug apparaît, `systematic-debugging` m'empêche de proposer des fixes au hasard
- Pour n'importe quelle feature, `test-driven-development` s'active avant l'implémentation
- Avant de dire "c'est fini", `verification-before-completion` m'oblige à lancer les vraies commandes et montrer la sortie

**Skills inclus :** brainstorming · TDD · systematic-debugging · requesting-code-review · receiving-code-review · writing-plans · executing-plans · verification-before-completion · using-git-worktrees · subagent-driven-development · dispatching-parallel-agents · finishing-a-development-branch

C'est le premier plugin à installer. Tout le reste s'y greffe.

---

### spec-kit

**Repo :** [github/spec-kit](https://github.com/github/spec-kit) · [SpillwaveSolutions/sdd-skill](https://github.com/SpillwaveSolutions/sdd-skill) · [jcmrs/claude-code-spec-kit-subagent-plugin](https://github.com/jcmrs/claude-code-spec-kit-subagent-plugin)

Spec-kit renverse la logique habituelle : la spec est le livrable principal, le code n'est que sa traduction. C'est un workflow en 7 phases structurées que j'exécute via des slash commands dédiées, du premier mot à la dernière ligne de code.

**Les 7 phases :**
1. `/constitution` — poser les contraintes non-négociables du projet
2. `/specify` — rédiger la spécification fonctionnelle complète
3. `/clarify` — résoudre les ambiguïtés avant de toucher au code
4. `/plan` — décomposer en plan d'implémentation
5. `/tasks` — découper en tâches atomiques
6. Analyse — valider les dépendances et risques
7. Implémentation — coder depuis la spec, pas depuis l'intuition

**Ce que ça change concrètement :**
- Je ne commence jamais à coder sur une compréhension floue — la phase clarify force les vraies questions au bon moment
- La spec devient un document vivant que l'équipe peut lire, pas juste moi
- Les sous-agents travaillent depuis la spec, pas depuis leur interprétation du prompt
- Sur des features complexes, `/tasks` me donne un backlog propre que je consomme séquentiellement

**Quand je l'active :** dès qu'une feature est assez complexe pour avoir plusieurs parties prenantes ou plusieurs jours de travail.

**Relation avec superpowers :** complémentaires mais avec un léger overlap sur les phases de planning. Superpowers traite le workflow comme source de vérité (comment travailler), spec-kit traite la spec comme source de vérité (quoi construire). Le bridge [WangX0111/superspec](https://github.com/WangX0111/superspec) unifie les deux approches si besoin.

---

### commit

**Repo :** [anthropics/claude-code · plugins/commit](https://github.com/anthropics/claude-code/tree/main/plugins/commit-commands)

Simple, mais je l'utilise à chaque fin de feature. Le plugin expose `/commit` et `/commit-push-pr` — il analyse le diff, génère un message de commit cohérent, et peut enchaîner avec la création de PR.

**Ce que ça change concrètement :**
- Je n'écris plus de messages de commit à la main pour les tâches courantes
- Le format reste consistant (conventional commits, co-authored-by, etc.)
- `/commit-push-pr` enchaîne push + ouverture de PR en une seule commande

**Quand je l'active :** systématiquement après avoir validé que le code tourne (`verification-before-completion` de superpowers en amont).

---

### code-review

**Repo :** [anthropics/claude-code · plugins/code-review](https://github.com/anthropics/claude-code/tree/main/plugins/code-review)

Ce plugin lance plusieurs agents spécialisés en parallèle sur une PR. Chaque agent se concentre sur un angle différent (sécurité, performance, lisibilité, tests) et produit des findings avec un score de confiance.

**Ce que ça change concrètement :**
- Je ne rate plus les problèmes de biais de perspective (un seul regard = angles morts)
- Les findings sont scorés, je sais quoi prioriser
- Sur les PRs importantes, j'utilise `/review` et je laisse les agents travailler en parallèle

**Note :** superpowers inclut ses propres skills `requesting-code-review` et `receiving-code-review`. Ce plugin officiel les complète — le skill superpowers guide le processus, le plugin lance les agents. Pas de conflit, usage complémentaire.

---

### security-guidance

**Repo :** [anthropics/claude-plugins-official · security-guidance](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/security-guidance)

Un hook `PreToolUse` qui s'active automatiquement avant chaque appel d'outil. Il scanne le code pour les vulnérabilités classiques : injection de commande, XSS, `eval()` non sécurisé, désérialisation pickle.

**Ce que ça change concrètement :**
- Je ne génère plus de code vulnérable sans avertissement explicite
- Le hook intervient avant que le code soit écrit, pas après
- Aucune action de ma part requise — c'est passif et permanent

**Quand ça s'active :** à chaque `Write` ou `Edit` impliquant du code à risque. C'est le filet de sécurité silencieux de toutes mes sessions.

---

### frontend-awesome

**Repo :** [ComposioHQ/awesome-claude-plugins](https://github.com/ComposioHQ/awesome-claude-plugins)

Mon skill pour toute interface utilisateur. Il me sort des esthétiques génériques IA — pas de grey cards avec des boutons bleus qui se ressemblent tous. Il pousse vers des interfaces distinctives, production-grade, avec de vraies décisions de design.

**Ce que ça change concrètement :**
- Je pense typographie, espace, hiérarchie visuelle, pas juste "je place des composants"
- Le code produit est directement utilisable en prod, pas un prototype à restyler
- Les animations et interactions sont intentionnelles

**Quand je l'active :** dès qu'une tâche touche à du frontend — component, page, landing, dashboard.

---

### theme-factory

**Repo :** [ComposioHQ/awesome-claude-plugins · theme-factory](https://github.com/ComposioHQ/awesome-claude-plugins/blob/master/theme-factory/skills/theme-factory/SKILL.md)

Complémentaire à frontend-awesome, mais à un niveau différent. Theme-factory s'occupe de la cohérence globale : 10 thèmes pré-définis (polices, palettes, espacement) applicables à des slides, docs, rapports, ou pages HTML.

**Ce que ça change concrètement :**
- Un rapport Markdown, une présentation, une landing page — même thème visuel
- Je n'improvise pas les couleurs à chaque fois
- Le client voit une identité cohérente, pas des outputs disparates

**Quand je l'active :** pour des livrables visuels (docs, présentations, sites) où la cohérence de marque compte. Frontend-awesome pour le code, theme-factory pour l'identité visuelle.

---

### graphify

**Repo :** [safishamsi/graphify](https://github.com/safishamsi/graphify)

Ce skill transforme n'importe quelle entrée — code, docs, papers, images — en graphes de connaissance interrogeables. L'implémentation utilise tree-sitter pour un traitement AST local, ce qui réduit la consommation de tokens jusqu'à 71,5x sur de grandes bases de code.

**Ce que ça change concrètement :**
- Sur un codebase inconnu, je mappe les relations entre modules en minutes au lieu d'heures
- Je peux interroger "qu'est-ce qui dépend de X ?" sans lire chaque fichier
- Les graphes persistent entre les sessions — je ne repars pas de zéro

**Quand je l'active :** `/graphify` dès qu'il y a un corpus dense à comprendre — onboarding sur un nouveau projet, analyse d'architecture, research papers à synthétiser.

---

### context7

**Repo :** [upstash/context7](https://github.com/upstash/context7)

**Installation MCP :** `claude mcp add --transport http context7 https://mcp.context7.com/mcp`

Un MCP server qui résout un problème simple mais douloureux : mes données d'entraînement ont une date limite. Quand je génère du code pour Next.js 15 ou Tailwind 4, je peux halluciner des APIs qui n'existent plus ou pas encore.

Context7 fetch la documentation officielle, version-spécifique, en temps réel et l'injecte dans mon contexte avant que je génère du code.

**Ce que ça change concrètement :**
- `use context7` dans un prompt → je travaille avec la vraie doc, pas mes souvenirs d'entraînement
- Zéro hallucination d'API sur les bibliothèques qui évoluent vite
- Particulièrement critique pour : Next.js, React 19, Tailwind, Shadcn, Prisma, tout ce qui change souvent

**Quand je l'active :** automatiquement dès qu'une tâche implique une lib dont les APIs évoluent. C'est mon garde-fou contre l'obsolescence.

---

### rtk

**Repo :** [rtk-ai/rtk](https://github.com/rtk-ai/rtk)

Un proxy CLI en Rust qui s'intercale entre moi et les commandes Bash. Il installe des hooks `PreToolUse` qui optimisent automatiquement les commandes avant exécution, réduisant la consommation de tokens de 60 à 90% sur les opérations dev courantes.

**Ce que ça change concrètement :**
- Un `find . -name "*.ts"` verbeux devient une requête optimisée sans que j'y pense
- Les sessions longues consomment beaucoup moins de tokens sur les opérations Bash répétitives
- Zéro configuration côté prompt — c'est le binaire qui fait le travail

**Quand ça s'active :** en arrière-plan, sur tous les appels Bash. C'est silencieux et automatique.

---

## Groupements recommandés

### Groupe 1 — Socle de dev (installer en premier, toujours)

```
superpowers + security-guidance + commit
```

Ces trois ne se marchent pas dessus et couvrent l'essentiel : discipline méthodologique, sécurité automatique, git propre. C'est la base sur laquelle tout le reste s'empile.

**Commandes :**
```bash
claude plugin add obra/superpowers
claude plugin add anthropics/claude-plugins-official/security-guidance
claude plugin add anthropics/claude-code/plugins/commit-commands
```

---

### Groupe 2 — Qualité & revue (ajouter quand le projet prend de l'ampleur)

```
code-review + [superpowers déjà installé]
```

Le plugin `code-review` s'appuie sur les skills superpowers pour le processus — ils sont complémentaires, pas redondants. Ajouter `code-review` seul sans superpowers perd la moitié de la valeur.

---

### Groupe 2b — Spec-driven (features complexes ou multi-intervenants)

```
spec-kit + superpowers
```

Spec-kit et superpowers se renforcent quand on les combine consciemment : spec-kit définit *quoi* construire (via les 7 phases), superpowers définit *comment* le construire (TDD, debugging, review). La phase `/plan` de spec-kit s'enchaîne naturellement avec `writing-plans` et `executing-plans` de superpowers.

**Compatible avec** : code-review (Groupe 2), tous les autres groupes.

---

### Groupe 3 — Frontend & design (projets UI)

```
frontend-awesome + theme-factory
```

Aucun conflit entre les deux — ils opèrent à des niveaux différents. frontend-awesome produit le code de composants, theme-factory définit l'identité visuelle globale. Ils se complètent.

**Compatible avec** : tous les groupes ci-dessus.

---

### Groupe 4 — Contexte & connaissance (projets complexes ou inconnus)

```
context7 (MCP) + graphify
```

Context7 injecte de la doc externe fraîche, graphify mappe la structure interne du projet. Les deux travaillent sur le contexte mais à des sources différentes (externe vs interne) — pas de conflit.

**Attention tokens :** ces deux outils injectent du contexte. Avec `rtk` actif, l'impact est absorbé. Sans rtk, surveiller la taille du contexte sur les longues sessions.

**Installation context7 :**
```bash
claude mcp add --transport http context7 https://mcp.context7.com/mcp
```

---

### Groupe 5 — Optimisation silencieuse (infrastructure)

```
rtk
```

`rtk` s'installe une fois et tourne en fond. Ce groupe est transversal : rtk est compatible avec tous les autres groupes sans conflit.

**Installation rtk :**
```bash
cargo install rtk  # ou via le binaire pré-compilé
rtk install        # installe les hooks PreToolUse dans Claude Code
```

---

## Matrice de compatibilité

```
                    superpowers  spec-kit  commit  code-review  security  frontend  theme  graphify  context7  rtk
superpowers             —          ⚠️        ✅        ✅           ✅        ✅        ✅      ✅         ✅       ✅
spec-kit                ⚠️         —         ✅        ✅           ✅        ✅        ✅      ✅         ✅       ✅
commit                  ✅         ✅         —        ✅           ✅        ✅        ✅      ✅         ✅       ✅
code-review             ✅         ✅         ✅        —           ✅        ✅        ✅      ✅         ✅       ✅
security-guidance       ✅         ✅         ✅        ✅            —        ✅        ✅      ✅         ✅       ✅
frontend-awesome        ✅         ✅         ✅        ✅           ✅         —        ✅      ✅         ✅       ✅
theme-factory           ✅         ✅         ✅        ✅           ✅        ✅         —      ✅         ✅       ✅
graphify                ✅         ✅         ✅        ✅           ✅        ✅        ✅       —         ✅       ✅
context7                ✅         ✅         ✅        ✅           ✅        ✅        ✅      ✅          —       ✅
rtk                     ✅         ✅         ✅        ✅           ✅        ✅        ✅      ✅         ✅        —
```

**Légende :**
- ✅ Compatible sans friction
- ⚠️ Overlap sur les phases de planning — combiner consciemment (spec-kit définit le quoi, superpowers définit le comment)

---

## Installation recommandée par profil

### Dev solo, projets perso
```
Groupe 1 (socle) + rtk + context7
```
Rapide, économique en tokens, assez de structure.

### Équipe, projets critiques
```
Groupe 1 + Groupe 2 + Groupe 2b (spec-kit) + context7 + rtk
```
Rigueur maximale, spec comme source de vérité, revue systématique, sécurité automatique.

### Fullstack avec frontend intensif
```
Groupe 1 + Groupe 3 + context7
```
La doc à jour de context7 est critique pour les libs frontend qui évoluent vite.

### Exploration / onboarding sur un nouveau codebase
```
Groupe 1 + Groupe 4 (graphify + context7)
```
Mapper l'inconnu rapidement avant de toucher au code.

---

## Stack recommandée — zéro conflit, couverture maximale

> Ma sélection personnelle : les outils que j'installerais sur n'importe quel projet sans hésiter, sans risque de friction entre eux.

### Ce que j'installe, sans exception

| # | Outil | Pourquoi c'est non-négociable |
|---|---|---|
| 1 | **superpowers** | Socle de toute la méthodologie. Rien d'autre ne tient sans lui. |
| 2 | **security-guidance** | Hook passif. Coût zéro, filet de sécurité permanent. |
| 3 | **commit** | Chaque feature finit par un commit. Autant que ce soit propre. |
| 4 | **rtk** | S'installe une fois. Réduit les tokens en silence sur toute la durée. |
| 5 | **context7** | Élimine les hallucinations d'API. Critique dès qu'une lib évolue vite. |

### Ce que j'ajoute selon le projet

| Outil | Condition d'ajout |
|---|---|
| **spec-kit** | Feature complexe, multi-jours, ou plusieurs personnes impliquées |
| **code-review** | Dès qu'il y a une PR à merger sur un projet qui compte |
| **graphify** | Nouveau codebase inconnu ou corpus dense à cartographier |
| **frontend-awesome** | Toute tâche touchant à une interface utilisateur |
| **theme-factory** | Livrable visuel (doc, présentation, landing) avec identité de marque |

### La stack complète en une ligne

```
superpowers + spec-kit + security-guidance + commit + code-review
+ frontend-awesome + theme-factory + graphify + context7 + rtk
```

Tous ces outils sont ✅ entre eux. Chacun opère dans sa couche sans empiéter sur les autres.

---

## Condensé — Comment j'utilise tout ça ensemble

> Ce n'est pas une liste d'outils indépendants. C'est un système. Chaque outil a sa place dans le flux, et certains ne s'activent jamais directement — ils sont là en fond, silencieux, et font leur travail sans que j'aie à y penser.

### Le flux d'une session type

```
Nouvelle session
    │
    ├─ rtk s'installe en fond       → optimise tous les appels Bash automatiquement
    ├─ security-guidance s'active   → scanne chaque Write/Edit en silence
    └─ superpowers se charge        → m'oblige à chercher le bon skill avant de répondre

Je reçois une tâche
    │
    ├─ [Feature complexe, multi-jours]
    │       └─ spec-kit : /constitution → /specify → /clarify → /plan → /tasks
    │               └─ superpowers writing-plans + executing-plans pour l'implémentation
    │
    ├─ [Feature courte, bien définie]
    │       └─ superpowers brainstorming → TDD → code → verification-before-completion
    │
    └─ [Codebase inconnu]
            └─ graphify pour mapper → context7 pour la doc à jour → puis le flux normal

Je touche au frontend
    └─ frontend-awesome pour le code des composants
            └─ theme-factory si c'est un livrable avec une identité visuelle

Je finis une feature
    └─ superpowers verification-before-completion → commit → code-review (sur les PRs importantes)
```

---

### Les rôles de chaque outil dans le système

| Couche | Outils | Ce qu'ils font dans le flux |
|---|---|---|
| **Infrastructure silencieuse** | rtk · security-guidance | Toujours actifs, jamais appelés directement |
| **Discipline de session** | superpowers | Orchestre comment j'aborde chaque tâche |
| **Spécification** | spec-kit | Définit *quoi* construire avant tout |
| **Contexte** | context7 · graphify | Nourrit ma compréhension (externe et interne) |
| **Design** | frontend-awesome · theme-factory | S'active sur les tâches UI/livrable |
| **Livraison** | commit · code-review | Clôture propre de chaque feature |

---

### Les trois décisions à prendre en début de projet

**1. Est-ce que la feature nécessite une spec formelle ?**
- Oui (complexe, multi-jours, plusieurs intervenants) → **spec-kit** en amont, puis superpowers pour l'exécution
- Non (tâche courte, périmètre clair) → **superpowers** seul suffit

**2. Quel niveau de contexte injecter ?**
- Lib qui évolue vite (React, Next, Tailwind…) → **context7 obligatoire**
- Nouveau codebase dense → **graphify en premier**
- Les deux → les deux, rtk absorbe l'overhead tokens

**3. Est-ce que le frontend est impliqué ?**
- Oui → **frontend-awesome** systématiquement, **theme-factory** si livrable visuel
- Non → ces deux outils restent dormants, pas de surcharge

---

### Ce que je n'ai jamais à configurer à chaque session

- `rtk` — binaire installé une fois, hooks permanents
- `security-guidance` — hook PreToolUse, toujours actif
- `superpowers` — chargé au démarrage de session, s'impose de lui-même
- `context7` — MCP enregistré, disponible sur demande avec `use context7`

Tout le reste est **déclenché à la demande**, au moment où le flux l'exige — pas avant.
