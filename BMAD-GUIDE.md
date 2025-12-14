# Guide B-MAD - Structure des Documents

Ce document explique la structure des documents générés par la méthode B-MAD (BMAD Multi-Agent Development) dans ce projet.

**Repo officiel :** https://github.com/bmad-code-org/BMAD-METHOD

**Vidéo explicative (1h) :** https://www.youtube.com/watch?v=LorEJPrALcg
*(Vidéo du créateur de BMAD - jouable en x2 si vous êtes pressés)*

---

## Qu'est-ce que B-MAD ?

B-MAD est une méthodologie qui utilise plusieurs agents IA spécialisés pour guider un projet du brainstorming jusqu'au code déployé. Chaque agent a un rôle précis (Business Analyst, Product Manager, Architect, etc.) et produit des livrables structurés qui alimentent l'agent suivant.

---

## Installation de B-MAD

Une seule commande suffit :

```bash
npx bmad-method install
```

Ensuite, laissez-vous guider ! Ouvrez votre IDE (Cursor, Windsurf, Claude Code...) et lancez `*workflow-init` pour que BMAD analyse votre projet et vous propose le workflow adapté :

- **Quick Flow** (< 5 min) : corrections de bugs, petites fonctionnalités
- **BMad Method** (< 15 min) : produits, plateformes
- **Enterprise** (< 30 min) : conformité, scalabilité

---

## Structure des Documents

Tous les documents générés se trouvent dans le dossier `docs/`.

### 1. Brainstorming (`brainstorming-session-results.md`)

**Généré par :** Business Analyst Mary

C'est le **point de départ** de tout projet B-MAD. Ce document capture une session de brainstorming structurée utilisant plusieurs techniques :

- **Role Playing** : Exploration des besoins via 3 personas (Dev Junior, Dev Senior, Moldu curieux)
- **Five Whys** : Creuser en profondeur pour comprendre les vraies motivations
- **Analogical Thinking** : Trouver des analogies pour clarifier le concept

Le document contient les idées brutes, les insights découverts, et les premières recommandations pour le produit.

---

### 2. PRD - Product Requirements Document (`prd.md`)

**Généré par :** Product Manager John

Le PRD est le document de **spécifications produit**. Il transforme les insights du brainstorming en exigences concrètes :

- **Goals & Background** : Contexte et objectifs du projet
- **Requirements** : Exigences fonctionnelles (FR) et non-fonctionnelles (NFR)
- **Technical Assumptions** : Stack technique, architecture choisie
- **Epic List** : Découpage en grands blocs de travail
- **Stories détaillées** : User stories avec critères d'acceptation

Ce document fait ~15 000 caractères et contient tout ce dont un développeur a besoin pour comprendre QUOI construire.

---

### 3. Architecture (`architecture.md`)

**Généré par :** Architect Winston

Le document d'architecture définit COMMENT le système sera construit :

- **High Level Architecture** : Vue d'ensemble, patterns utilisés (ETL, Serverless)
- **Tech Stack** : Technologies choisies avec justifications
- **Data Models** : Structures de données TypeScript
- **Components** : Description de chaque module du système
- **External APIs** : Intégrations (Bright Data, Claude, Notion)
- **Core Workflows** : Flux de données et séquences d'exécution
- **Error Handling** : Stratégie de gestion des erreurs
- **Testing Strategy** : Approche de test (Unit + Integration)
- **Security** : Bonnes pratiques de sécurité

Ce document fait ~30 000 caractères et sert de **blueprint technique** pour le développement.

---

### 4. PRD Shardé (`docs/prd/`)

Le PRD monolithique est également disponible en version **"shardée"** (éclatée) pour une navigation plus facile :

```
docs/prd/
├── index.md                      # Table des matières
├── goals-and-background-context.md
├── requirements.md
├── technical-assumptions.md
├── epic-list.md
├── epic-1-foundation-scraping.md
├── epic-2-llm-filtering-scoring.md
├── epic-3-notion-output-pipeline.md
├── out-of-scope-v2.md
├── checklist-results-report.md
└── next-steps.md
```

Chaque fichier correspond à une section du PRD. L'`index.md` sert de table des matières avec des liens vers chaque section.

---

### 5. Architecture Shardée (`docs/architecture/`)

Même principe pour l'architecture :

```
docs/architecture/
├── index.md                      # Table des matières
├── introduction.md
├── high-level-architecture.md
├── tech-stack.md
├── data-models.md
├── components.md
├── external-apis.md
├── core-workflows.md
├── source-tree.md
├── infrastructure-and-deployment.md
├── error-handling-strategy.md
├── coding-standards.md
├── test-strategy-and-standards.md
├── security.md
├── checklist-results-report.md
└── next-steps.md
```

Cette version shardée est particulièrement utile pour les agents IA qui ont besoin de lire uniquement certaines sections sans charger tout le document.

---

### 6. Stories (`docs/stories/`)

**Générées par :** Product Manager John (puis raffinées)

Les stories sont des **unités de travail** prêtes à être implémentées. Chaque fichier suit le format `{epic}.{story}.story.md` :

```
docs/stories/
├── 1.1.story.md   # Project Setup
├── 1.2.story.md   # Reddit Scraper
├── 1.3.story.md   # Hacker News Scraper
├── 1.4.story.md   # Unified Post Format & Deduplication
├── 2.1.story.md   # Claude API Integration
├── 2.2.story.md   # Benjamin Profile Prompt
├── 2.3.story.md   # Post Analysis & Scoring
├── 2.4.story.md   # Top Posts Selection
├── 2.5.story.md   # (ajoutée en cours de dev)
├── 3.1.story.md   # Notion API Integration
├── 3.2.story.md   # Notion Entry Format
├── 3.3.story.md   # Main Pipeline Orchestration
└── 3.4.story.md   # GitHub Actions Automation
```

Chaque story contient :
- **User Story** au format "As a... I want... So that..."
- **Acceptance Criteria** numérotés
- **Tasks/Subtasks** avec checkboxes
- **Dev Notes** pour guider l'implémentation
- **References** vers les docs d'architecture pertinents

---

### 7. QA Gates (`docs/qa/gates/`)

**Utilisées par :** QA Agent

Les quality gates sont des **checklists de validation** au format YAML :

```
docs/qa/gates/
├── 1.1-project-setup.yml
└── 2.1-claude-api-integration.yml
```

Chaque gate liste les critères à vérifier avant de considérer une story comme "Done".

---

## Flux de la Méthode B-MAD

```
┌─────────────────────────────────────────────────────────────────┐
│                         BRAINSTORMING                           │
│                   (Business Analyst Mary)                       │
│         Techniques: Role Playing, Five Whys, Analogies          │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                            PRD                                  │
│                   (Product Manager John)                        │
│      Goals, Requirements, Epics, Stories, Acceptance Criteria   │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                       ARCHITECTURE                              │
│                    (Architect Winston)                          │
│    Tech Stack, Data Models, Components, Workflows, Security     │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                         STORIES                                 │
│                     (Dev Agent Ready)                           │
│              Prêtes à être implémentées une par une             │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                       IMPLEMENTATION                            │
│                       (Dev Agent)                               │
│           Code, Tests, Validation via QA Gates                  │
└─────────────────────────────────────────────────────────────────┘
```

---

*Ce projet a été entièrement spécifié et développé en utilisant la méthode B-MAD avec Claude comme agent IA.*
