# portaki-changelog

Changelog **produit** de [Portaki](https://portaki.app) destiné aux hôtes : il alimente la cloche **Nouveautés**, la page `/whats-new` et la modal de release du dashboard hôte.

Le dépôt ne contient qu'un fichier de données : [`releases.yaml`](releases.yaml). Pas de code, pas de build.

> Ce n'est **pas** le changelog des modules (`portaki-modules`), ni les GitHub Releases techniques de chaque dépôt.

## Comment il est lu

| Composant | Rôle |
|-----------|------|
| **portaki-platform** (orchestrateur) | Télécharge `releases.yaml` depuis `raw.githubusercontent.com` (sans token), le met en cache et l'expose sous `/api/v1/me/product-updates` (liste, non-lus, release mise en avant, abonnement e-mail). |
| **portaki-web** (dashboard hôte) | Cloche, page `/whats-new`, modal hero. |

- Le cache est rechargé au démarrage, par une tâche planifiée et à la lecture s'il a plus de 6 h. **Publier = merger sur `main`**, aucun redéploiement.
- Si GitHub est injoignable, l'orchestrateur retombe sur une copie embarquée (`product-updates/releases.yaml` dans le classpath).
- Une release dont `releasedAt` est dans le futur n'est pas affichée.
- Un hôte ne voit comme nouveautés que les releases publiées après la création de son compte.
- L'état lu / non lu et l'abonnement e-mail sont stockés côté API, pas ici.

Variables côté orchestrateur (défauts) : `CHANGELOG_GITHUB_ORG` (`PortakiApp`), `CHANGELOG_GITHUB_REPO` (`portaki-changelog`), `CHANGELOG_GITHUB_REF` (`main`), `CHANGELOG_FALLBACK_CLASSPATH` (`true`).

## Publier une release

1. Ajouter une entrée **en tête** de `releases.yaml` (ordre décroissant par `releasedAt`).
2. Donner un `id` UUID **nouveau et stable** : il sert au marquage « lu », ne jamais le réutiliser.
3. Rédiger un texte **produit en français** (pas un copier-coller de GitHub Releases).
4. Ouvrir une PR, la faire relire, merger sur `main`.

### Schéma

```yaml
releases:
  - id: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  # UUID fixe
    version: "1.5.0"                            # semver
    releasedAt: "2026-05-20T10:00:00Z"          # ISO-8601 UTC
    category: platform                          # voir ci-dessous
    titleFr: "Titre court (cloche + timeline)"
    summaryFr: "Résumé en une ou deux phrases."
    featured: false                             # true pour une seule release récente (modal hero)
    featureHighlightFr: "Texte long de la modal" # si featured
    demoUrl: /listings                          # optionnel, chemin du dashboard
    sourceUrl: https://github.com/PortakiApp/…   # optionnel
    entries:
      - changeType: nouveau                     # nouveau | fix | perf | change
        titleFr: "Une ligne par changement"
```

| `category` | Filtre affiché |
|------------|----------------|
| `platform` | Plateforme |
| `guest` | Voyageur |
| `marketplace` | Marketplace |
| `auth` | Auth |
| `property` | Logement |

## Licence

Contenu éditorial © Portaki. Les contributions passent par PR sur ce dépôt.
