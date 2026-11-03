# Notes pour les mainteneurs

Ce dépôt porte le profil de l'organisation (`profile/README.md`) et les fichiers
communautaires par défaut des dépôts `spar-x-fr` (SECURITY.md, modèles de tickets et de
PR).

## Réglages appliqués sur GitHub

- Fusion par squash uniquement, suppression automatique des branches ; wiki et projets
  désactivés.
- Jeton des workflows en lecture seule ; les workflows ne peuvent pas approuver de PR.
- Actions limitées à celles de GitHub et à une liste blanche
  (`step-security/harden-runner`, `trufflesecurity/trufflehog`, `ossf/scorecard-action`),
  SHA complet obligatoire.
- Graphe de dépendances, alertes et correctifs de sécurité Dependabot, scan de secrets
  avec blocage des pushes, signalement privé des vulnérabilités.
- Ruleset `Protection de main`, versionné dans
  [`.github/rulesets/protect-main.json`](../.github/rulesets/protect-main.json) : PR
  obligatoire, pas de suppression ni de force-push, historique linéaire, conversations
  résolues, checks `zizmor` et `trufflehog` sur une branche à jour.

```bash
gh api -X PUT repos/spar-x-fr/.github/rulesets/<id> --input .github/rulesets/protect-main.json
```

## CI

- `security.yml` : zizmor (workflows et Dependabot) et TruffleHog sur tout l'historique.
- `scorecard.yml` : OpenSSF Scorecard (dépôt public uniquement).
- CodeQL n'est pas configuré : le dépôt ne contient aucun code analysable.

## Mettre à jour les épinglages

Dependabot propose chaque semaine les mises à jour des actions et de zizmor
(`.github/requirements-ci.txt`). À la main : SHA du tag via
`gh api repos/<owner>/<action>/git/ref/tags/<tag>`, version en commentaire ; hachages
PyPI vérifiés avec `pip install --require-hashes --no-deps -r .github/requirements-ci.txt`.
La version de l'image TruffleHog (`version:` dans `security.yml`) suit le SHA de l'action.
