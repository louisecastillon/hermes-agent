# ACC: mémoire révisable pour un profil Hermes

Ce dossier propose un **préréglage à fusionner** dans le `config.yaml` d'un profil Hermes dédié à ACC. Il n'est pas actif par sa présence dans ce dépôt. La branche conserve le moteur Hermes intact, afin de garder les mises à jour amont possibles.

## Ce que fait le préréglage

- La mémoire native `MEMORY.md` / `USER.md` reste disponible. Avec `memory.write_approval: true`, les écritures de l'agent demandent une approbation dans une CLI interactive ou sont mises en attente sur les autres surfaces. Lire `/memory pending`, puis `/memory approve <id>` ou `/memory reject <id>`.
- Les mutations via `skill_manage` sont mises en attente avec `skills.write_approval: true`. Examiner `/skills pending` et `/skills diff <id>`, puis accepter ou rejeter.
- Les rappels périodiques et la revue en arrière-plan restent actifs pour **proposer** des connaissances et méthodes. Ils consomment des appels de modèle. Pour un fonctionnement moins coûteux et entièrement manuel, mettre `memory.nudge_interval: 0`, `skills.creation_nudge_interval: 0` et `auxiliary.background_review.enabled: false`.

## Application

1. Créer ou choisir un profil Hermes **distinct par utilisateur/locataire**. Ses sessions, mémoire, skills et secrets doivent rester séparés.
2. Ouvrir le `config.yaml` de ce profil. Fusionner les clés de [memory-reviewed.fragment.yaml](./memory-reviewed.fragment.yaml) avec les sections existantes ; ne pas remplacer le fichier entier ni inscrire des secrets dans Git.
3. Relancer le profil, faire une demande bénigne qui appelle `memory(add)`, puis vérifier que l'entrée est proposée et absente de la mémoire active avant approbation. Vérifier le même parcours pour un skill. Ne pas activer pour des clients avant un test d'isolation entre deux profils et un test de refus.

## Ce que ce préréglage ne résout pas

Le contrôle `write_approval` couvre les écritures de l'outil mémoire natif et les mutations par `skill_manage`. Un fournisseur de mémoire externe, une écriture directe de fichier par terminal ou d'autres chemins personnalisés demandent leur propre politique ; ce préréglage ne prouve pas leur couverture. Les deux fichiers Markdown n'ont pas de preuve par énoncé, de dates de validité ou de détection fiable des contradictions. ACC doit conserver sa propre mémoire structurée et son contrôle d'accès côté serveur, comme détaillé dans [la PR ACC #59](https://github.com/louisecastillon/acc-live/pull/59).

Ce fork est public, comme son amont. Ne jamais committer de `USER.md`, `MEMORY.md`, `.env`, sessions ou clés personnelles.
