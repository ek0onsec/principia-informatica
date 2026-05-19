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

---

# Économie de tokens — Règles d'efficacité

## Lecture de fichiers

- Toujours lire avec `offset` + `limit` quand on sait quelle section est pertinente. Ne jamais lire un fichier entier pour en utiliser 10 lignes.
- Ne jamais relire un fichier qu'on vient d'éditer — l'outil Edit confirme le succès, c'est suffisant.
- Préférer `grep` ciblé à la lecture de fichiers entiers pour localiser un symbole ou une valeur.
- Sur les gros fichiers de config ou lock (`package-lock.json`, `yarn.lock`, `poetry.lock`), ne jamais les lire sauf si la tâche le requiert explicitement.

## Commandes Bash

- Pipe et filtre directement dans la commande : `grep -n "pattern" file` plutôt que lire le fichier puis chercher.
- `find` avec des critères précis (`-name`, `-path`, `-type`) — ne jamais scanner `/` ou des arborescences larges.
- Pour vérifier l'existence d'un fichier ou d'un symbole : `test -f path`, `grep -q pattern file`. Pas de `cat` pour confirmer qu'un fichier existe.
- Chaîner les commandes indépendantes avec `&&` ou les lancer en parallèle dans un seul appel Bash plutôt que plusieurs appels séquentiels.

## Exploration du codebase

- Avant d'explorer, formuler une hypothèse précise sur où se trouve ce qu'on cherche. Chercher là d'abord.
- Ne pas lire les fichiers de test pour comprendre l'implémentation — lire l'implémentation directement.
- Ne pas lire la documentation pour comprendre le code — lire le code.
- Si `rtk` est actif, lui faire confiance pour optimiser les commandes Bash automatiquement.

## Réponses et communication

- Pas de reformulation de la demande de l'utilisateur avant de répondre.
- Pas de résumé de ce qui vient d'être fait à la fin d'une réponse — le diff parle de lui-même.
- Pas de liste de "prochaines étapes" non demandées.
- Les mises à jour en cours de travail : une phrase, pas un paragraphe.

## Agents et sous-tâches

- Ne pas spawner un agent pour une tâche faisable en 2-3 appels d'outils directs.
- Un agent Explore uniquement si la recherche couvre plus de 3 requêtes distinctes. Sinon, `grep` ou `find` en direct.
- Ne pas déléguer à un sous-agent une recherche dont on connaît déjà le fichier cible.

## Contexte et mémoire

- `context7` uniquement sur les libs qui évoluent vite — ne pas l'invoquer pour des APIs stables (stdlib, protocoles standard).
- `graphify` une fois par projet inconnu, pas à chaque session sur un projet déjà cartographié.
- Ne pas relire les fichiers de configuration déjà lus dans la session courante.

---

# Standards de code — Non négociables

Ces règles s'appliquent à chaque ligne écrite, sur tout projet, sans exception. Elles ne sont pas des recommandations. Si une règle entre en conflit avec une décision rapide, la règle gagne.

---

## Architecture : découplage par défaut

### Ports & Adapters (Hexagonal)
Toute dépendance externe — base de données, API tierce, système de fichiers, service mail — est cachée derrière une interface. Le domaine métier ne connaît jamais le nom d'un driver, d'un ORM, ou d'un SDK.

```
# Mauvais — le domaine dépend de MongoDB
class UserService:
    def __init__(self):
        self.collection = MongoClient()["users"]

# Correct — le domaine dépend d'une interface
class UserService:
    def __init__(self, repo: UserRepository):
        self.repo = repo

class MongoUserRepository(UserRepository): ...
class PostgresUserRepository(UserRepository): ...
class InMemoryUserRepository(UserRepository): ...  # pour les tests
```

**Règle concrète :** passer de MongoDB à Postgres = écrire un nouvel adaptateur + changer une ligne dans le wiring. Zéro touche au domaine.

### Injection de dépendances
Aucune instanciation de dépendance concrète à l'intérieur d'un service ou d'un handler. Toutes les dépendances sont injectées depuis l'extérieur (constructeur, paramètre, conteneur DI). Le `new DbClient()` n'a pas sa place dans la logique métier.

### Séparation des couches — ordre strict
```
Entrée (HTTP/CLI/event) → Handler/Controller → Service/Use case → Repository/Port → Adaptateur
```
- Les handlers ne contiennent pas de logique métier
- Les services ne savent pas comment les données sont stockées
- Les repositories ne savent pas ce que le service va en faire
- Les adaptateurs ne contiennent pas de logique métier

### Configuration externalisée
Toute valeur qui change entre environnements (URL, credentials, feature flags, timeouts) vit dans l'environnement, jamais dans le code. `.env` pour le local, variables d'environnement réelles en prod. Zéro secret hardcodé, zéro URL de base de données en clair dans un fichier versionné.

---

## Qualité du code : règles structurelles

### Nommage — explicite, sans abréviation
Un nom doit révéler l'intention. Si un commentaire est nécessaire pour expliquer ce que fait une variable, le nom est mauvais.
- Fonctions : verbe + objet (`fetchUserById`, `calculateTotalPrice`, `validateEmailFormat`)
- Booléens : préfixe d'état (`isLoading`, `hasPermission`, `canRetry`)
- Pas d'abréviations (`usr`, `mgr`, `req` sont interdits sauf conventions établies du framework)

### Fonctions — une seule responsabilité
Une fonction fait une chose. Si sa description nécessite le mot "et", elle doit être découpée. Taille indicative : si une fonction dépasse 30 lignes, c'est un signal de découpage, pas une règle absolue.

### Pas de logique dupliquée
Trois occurrences du même pattern = abstraction obligatoire. Deux = acceptable si le contexte diffère vraiment. Une = inline.

### Gestion des erreurs — explicite, jamais silencieuse
- Pas de `except: pass`, `catch {}`, ou `err != nil` ignoré
- Les erreurs renvoyées vers l'extérieur sont typées et documentées
- Les erreurs inattendues sont loggées avec contexte (quelle opération, quels paramètres, quel état)
- Les erreurs attendues (not found, validation) sont distinctes des erreurs système (timeout, DB down)

### Typage strict
Sur tout projet qui le supporte (TypeScript, Python avec mypy, Go, Rust, Java) : typage complet sans `any`, sans `interface{}` arbitraire, sans cast non justifié. Les types sont la première ligne de documentation.

### Immutabilité par défaut
Préférer les structures immuables. Une variable n'est mutable que si sa mutation est le comportement attendu, pas une commodité.

---

## Sécurité : règles absolues

### Aucun secret dans le code
Variables d'environnement ou gestionnaire de secrets (Vault, AWS Secrets Manager, etc.). Jamais de token, mot de passe, clé API, ou certificat dans un fichier versionné. Même en commentaire. Même dans un fichier de test.

### Validation à chaque frontière
Toute donnée qui entre dans le système depuis l'extérieur (requête HTTP, fichier uploadé, message queue, webhook) est validée et assainie avant traitement. La validation se fait au plus tôt, le rejet au plus tôt.

### Requêtes paramétrées, jamais de concaténation
```python
# Interdit — injection SQL garantie
query = f"SELECT * FROM users WHERE id = {user_id}"

# Obligatoire
query = "SELECT * FROM users WHERE id = $1"
cursor.execute(query, (user_id,))
```
La même règle s'applique aux commandes shell, aux expressions LDAP, aux requêtes NoSQL avec opérateurs.

### Principe du moindre privilège
Chaque composant, service, et utilisateur de base de données n'a accès qu'à ce dont il a strictement besoin. Un service de lecture n'a pas de droit d'écriture. Une API publique n'accède pas directement aux tables internes.

### Authentification et autorisation séparées
L'authentification (qui es-tu ?) et l'autorisation (que peux-tu faire ?) sont deux couches distinctes. L'autorisation se vérifie à chaque action, pas seulement à la connexion.

### Pas de données sensibles dans les logs
Les logs ne contiennent jamais : mots de passe, tokens, numéros de carte, données personnelles non masquées.

---

## Maintenabilité : règles de durée de vie

### Zéro dette technique silencieuse
Un `TODO` dans le code n'existe que s'il est accompagné d'un ticket ou d'une date. Sans référence externe, il sera ignoré pour toujours — autant l'implémenter maintenant ou l'accepter comme permanent.

### Tests comme contrat
Les tests ne vérifient pas l'implémentation, ils vérifient le comportement. Un refactoring interne ne doit pas casser les tests si le comportement externe est identique. Les mocks ne remplacent pas les vraies intégrations pour les chemins critiques.

### Migrations de base de données versionnées
Toute modification de schéma passe par une migration versionnée (Flyway, Alembic, golang-migrate, Prisma Migrate). Jamais de modification de schéma en direct. Les migrations sont réversibles si possible, idempotentes toujours.

### Backwards compatibility explicite
Toute modification d'une API publique, d'un contrat de message, ou d'un schéma partagé est consciente et délibérée. Les breaking changes sont annoncés, pas glissés.

### Structure de projet prévisible
La structure de répertoires reflète l'architecture. Un nouveau développeur doit comprendre où trouver quoi en moins de 5 minutes. Pas de fichiers "utils" fourre-tout, pas de dossiers "misc", pas de logique métier dans les helpers.

---

## Ce qui est interdit sans justification explicite

| Pratique | Raison |
|---|---|
| `SELECT *` | Couplage implicite au schéma, performance |
| Logique métier dans les migrations | Les migrations ne sont pas réversibles proprement |
| Connexions DB sans pool | Épuisement de ressources sous charge |
| Retry sans backoff exponentiel | Amplification de charge sur un service dégradé |
| Parsing de HTML/JSON avec regex | Fragilité et failles de sécurité |
| `eval()` ou équivalent sur données externes | Exécution de code arbitraire |
| Threads partagés sans synchronisation | Race conditions non déterministes |
| Ignorer le code de retour d'une fonction | Erreurs silencieuses |

---

## Checklist avant chaque `Write` ou `Edit`

- [ ] La dépendance externe est derrière une interface
- [ ] Aucun secret ou valeur d'environnement hardcodée
- [ ] Les entrées externes sont validées
- [ ] Les erreurs sont gérées explicitement
- [ ] Le nommage révèle l'intention sans commentaire
- [ ] La logique est dans la bonne couche (pas de SQL dans un controller, pas de HTTP dans un service)
- [ ] Un changement de base de données ne nécessiterait pas de toucher à ce fichier
