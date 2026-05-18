# portaki-changelog

Changelog **produit** de [Portaki](https://portaki.app) pour l’espace hôte (`dash.portaki.app`) : cloche **Nouveautés**, page `/dashboard/whats-new`, modal de release et digest e-mail (à venir).

Ce dépôt est **public** et versionné. L’API hôte (`portaki-api`) charge `releases.yaml` sur la branche configurée (par défaut `main`).

> Ce n’est **pas** le changelog des modules catalogue (`portaki-modules` / `portaki.module.json`).  
> Ce n’est **pas** non plus le registre GitHub Releases technique de chaque repo.

---

## Consommateurs

| Composant | Usage |
|-----------|--------|
| **portaki-api** | `GET /api/v1/me/product-updates` (+ unread, featured hero) |
| **portaki-web** | Popover cloche cadeau, timeline, liste brute |
| **Futur** | Digest e-mail bimensuel, flux RSS |

État **lu / non lu** et **abonnement e-mail** : stockés en base côté API (par utilisateur), pas dans ce fichier.

---

## Fichier source

Un seul fichier pour l’instant :

```
releases.yaml
```

Après merge sur `main`, le cache API est rafraîchi (démarrage + tâche planifiée ~6 h). Pas de redéploiement API pour publier une nouveauté.

---

## Publier une release

1. Éditer `releases.yaml`.
2. Ajouter une entrée **en tête de liste** (ordre décroissant par `releasedAt`).
3. Choisir un **`id` UUID stable** (ne jamais le réutiliser) — il sert au marquage « lu » côté hôte.
4. Ouvrir une PR ; valider le texte **FR produit** (pas de copier-coller brut GitHub Releases).
5. Merger sur `main`.

### Checklist release

- [ ] `version` semver (`1.4.2`)
- [ ] `releasedAt` ISO-8601 UTC (`2026-05-20T10:00:00Z`)
- [ ] `category` : `platform` | `guest` | `marketplace` | `auth` | `property`
- [ ] `titleFr` + `summaryFr` courts (popover + timeline)
- [ ] `featured: true` au plus pour **une** release récente (modal hero)
- [ ] `entries[]` avec `changeType` : `nouveau` | `fix` | `perf` | `change`
- [ ] `demoUrl` optionnel (chemin dashboard, ex. `/dashboard/marketplace`)

---

## Schéma YAML (résumé)

```yaml
releases:
  - id: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  # UUID fixe
    version: "1.5.0"
    releasedAt: "2026-05-20T10:00:00Z"
    category: platform
    titleFr: "Titre affiché dans le dashboard"
    summaryFr: "Résumé une ou deux phrases."
    featured: false
    featureHighlightFr: "Texte long pour la modal (si featured)"
    demoUrl: /dashboard/listings
    sourceUrl: https://github.com/PortakiApp/portaki-web/releases/tag/v1.5.0  # optionnel
    entries:
      - changeType: nouveau
        titleFr: "Bullet NOUVEAU / FIX / PERF / CHANGÉ"
```

---

## Catégories

| Valeur | Filtre UI |
|--------|-----------|
| `platform` | Plateforme |
| `guest` | Voyageur |
| `marketplace` | Marketplace |
| `auth` | Auth |
| `property` | Logement |

---

## Relation avec GitHub Releases

Recommandation :

1. Les tags / releases techniques restent sur chaque repo (`portaki-web`, `portaki-api`, …).
2. Ce fichier raconte la **story produit** pour les hôtes (une version = une entrée, même si plusieurs repos ont bougé).
3. Un job futur peut ouvrir une **PR brouillon** à partir des releases GitHub ; la rédaction finale reste humaine ici.

---

## Configuration API

Variables (défauts) dans `portaki-api` :

```yaml
portaki:
  product-updates:
    catalog:
      org: PortakiApp
      repo: portaki-changelog
      ref: main
      path: releases.yaml
      fallback-classpath-enabled: true
```

URL brute (repo public) :

`https://raw.githubusercontent.com/PortakiApp/portaki-changelog/main/releases.yaml`

---

## Licence

Contenu éditorial © Portaki. Structure et exemples : usage interne Portaki ; contributions via PR sur ce dépôt selon process équipe.
