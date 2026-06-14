# Guide d'utilisation — Claude SEO

> Guide pratique en français pour utiliser le skill **Claude SEO** dans Claude Code.
> Référence officielle (anglais) : `README.md` et `CLAUDE.md` à la racine.

---

## 1. C'est quoi

Extension (skill + agents) pour **Claude Code** qui ajoute la commande `/seo`. Elle fait du **diagnostic SEO** (audit technique, contenu, backlinks, performance) et **génère des artefacts concrets** (sitemap XML, schema JSON-LD, architecture de contenu, rapports PDF).

- **Version** : v2.2.0 — **25 skills**, **18 sous-agents**, **50 scripts Python**, 326 tests.
- **Stack** : Python (scripts dans `scripts/`), pas de JavaScript.
- **Licence** : MIT — auteur original `AgriciDaniel`. Ton fork : `github.com/yanisyano05/claude-seo`.
- **Ce n'est pas** un projet web. C'est un outil qui s'installe dans `~/.claude/`.

---

## 2. État de l'installation (chez toi)

| Élément | Emplacement | État |
|---|---|---|
| 17 sous-agents `seo-*` | `~/.claude/agents/` | ✅ **Actifs** (visibles en session) |
| 25 skills `seo*` | `~/.claude/skills-disabled/` | ⚠️ **Désactivés** |

> ⚠️ **Ton install local est ancien** (issu d'une v1.9.x : 17 agents). La v2.2.0 en ship **18** (ajout `seo-flow`). Réinstaller depuis ton fork à jour est recommandé (voir Option B).

**Conséquence** : la commande `/seo` **ne fonctionne pas tant que les skills sont désactivés**. Les agents seuls ne suffisent pas à router les commandes.

### Activer les skills

Option A — déplacer les skills hors du dossier `disabled` :
```bash
# Git Bash
mv ~/.claude/skills-disabled/seo* ~/.claude/skills/
```

Option B — installer proprement en plugin (recommandé, gère les mises à jour) :
```bash
# Dans Claude Code — depuis ton fork à jour (v2.2.0)
/plugin marketplace add yanisyano05/claude-seo
/plugin install claude-seo@yanisyano05-claude-seo

# ou depuis l'original
/plugin marketplace add AgriciDaniel/claude-seo
/plugin install claude-seo@agricidaniel-claude-seo
```

Après activation, relancer Claude Code puis tester : `/seo` (doit lister les sous-commandes).

---

## 3. Prérequis Python

Les scripts ont besoin de dépendances (voir `requirements.txt`) :
```bash
pip install -r requirements.txt
# ou venv dédié : ~/.claude/skills/seo/.venv/
```
Principales : `matplotlib` (charts), `weasyprint` (PDF), `playwright` (screenshots), libs Google API.

> Les fonctions Google APIs (Search Console, GA4, PageSpeed) demandent des credentials dans
> `~/.config/claude-seo/google-api.json`. Voir `skills/seo-google/`. Non requis pour un audit de base.

---

## 4. Commandes principales

| Commande | Ce qu'elle fait |
|---|---|
| `/seo audit <url>` | Audit complet du site (jusqu'à 15 agents en parallèle) |
| `/seo page <url>` | Analyse approfondie d'une seule page |
| `/seo technical <url>` | Audit technique (crawl, index, sécurité — 9 catégories) |
| `/seo content <url>` | Qualité de contenu + E-E-A-T |
| `/seo schema <url>` | Détecte et **génère** le JSON-LD Schema.org |
| `/seo sitemap <url>` | Analyse un sitemap existant |
| `/seo sitemap generate` | **Génère** un sitemap (templates par industrie) |
| `/seo images <url>` | Optimisation des images |
| `/seo geo <url>` | SEO pour moteurs IA (ChatGPT, Perplexity, AI Overviews) |
| `/seo local <url>` | SEO local (Google Business, citations, avis, map pack) |
| `/seo maps [cmd]` | Intelligence Maps (geo-grid, audit GBP, concurrents) |
| `/seo backlinks <url>` | Profil de backlinks (Moz, Bing, Common Crawl) |
| `/seo cluster <mot-clé>` | Clustering sémantique + architecture hub-and-spoke |
| `/seo sxo <url>` | Search Experience Optimization (personas, page-type) |
| `/seo hreflang <url>` | SEO international / hreflang |
| `/seo google [cmd] <url>` | APIs Google (GSC, PageSpeed, CrUX, GA4) |
| `/seo plan <type>` | Plan stratégique SEO par industrie |
| `/seo content-brief <kw>` | **(v2)** Génère un brief de contenu structuré |
| `/seo flow [cmd]` | **(v2)** Bibliothèque de prompts FLOW (41 prompts, sync GitHub) |
| `/seo drift baseline\|compare\|history <url>` | Monitoring de régressions SEO (17 règles, SQLite) |
| `/seo ecommerce <url>` | SEO e-commerce (schema produit, marketplaces) |

> L'orchestrateur route **25 commandes** au total. Liste complète : `CLAUDE.md` → section « Commands ».

---

## 5. Ce qu'il construit vs ce qu'il diagnostique

**Construit (artefacts à intégrer)**
- Sitemap XML
- Schema.org JSON-LD (à coller dans les pages)
- Architecture de contenu + matrice de liens internes (`/seo cluster`)
- Pages programmatiques, pages comparaison concurrents
- Rapports PDF/HTML (`scripts/google_report.py`)

**Diagnostique seulement (recommandations)**
- Technique, contenu, backlinks, Core Web Vitals, GEO, local

**Ne fait PAS**
- Modifier directement le code de ton site. Il te **donne** le markup / sitemap / arbo —
  l'intégration dans le projet Next.js reste manuelle (ou déléguée à Claude).

---

## 6. Workflow type pour un projet Next.js déployé

1. **Auditer** le site en ligne :
   ```
   /seo audit https://salimab.fr
   ```
2. **Générer le schema** et l'intégrer :
   ```
   /seo schema https://salimab.fr
   ```
   → coller le JSON-LD dans un Server Component (`app/layout.tsx` ou page concernée).
3. **Sitemap** : Next.js App Router a déjà `app/sitemap.ts` natif (préférer le natif à un XML statique).
4. **Performance** : `/seo google pagespeed <url>` pour les Core Web Vitals.
5. **Rapport client** : générer un PDF récapitulatif.

> **Limite importante** : l'outil **fetch l'URL en ligne**. Un projet local (`localhost`)
> n'est pas crawlable. Il faut un site **déployé** (ex. salimab.fr, pactassurance.fr).

---

## 7. État GitHub de ce dossier

- Ce dossier est un **clone** du dépôt original `github.com/AgriciDaniel/claude-seo`.
- Le `remote origin` pointe vers **AgriciDaniel**, pas vers ton compte (`yanisyano05`).
- **Aucun dépôt `claude-seo` n'existe sous ton compte GitHub** → l'outil n'est **pas**
  initialisé sur ton GitHub. C'est juste une copie locale en lecture.

### Si tu veux ton propre dépôt

Forker l'original (garde le lien upstream pour les mises à jour) :
```bash
gh repo fork AgriciDaniel/claude-seo --clone=false --remote
```
Ou créer un dépôt perso à partir de ce dossier (perd le lien upstream) :
```bash
gh repo create yanisyano05/claude-seo --private --source=. --remote=origin --push
```

---

## 8. Désinstaller

```bash
# Unix / Git Bash
bash uninstall.sh
# Windows
powershell -ExecutionPolicy Bypass -File uninstall.ps1
```
