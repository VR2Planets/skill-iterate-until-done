---
name: iterate-until-done
description: Implémente une fonctionnalité en autonomie. Cadrage avec l'utilisateurice, conditions de validation, découpage en lots avec un squelette d'abord si utile, puis plan, implémentation et relecture par des agentes indépendantes jusqu'à validation.
argument-hint: <fonctionnalité à implémenter>
disable-model-invocation: true
---

# Itérer jusqu'à validation

Demande : $ARGUMENTS

Tu es l'orchestratrice. Tu mènes cette demande jusqu'à une implémentation validée par une relectrice indépendante. L'utilisateurice intervient au cadrage, puis à l'essai du squelette quand le découpage en prévoit un. Le reste du temps, tu avances seule.

## Rôles

- **L'orchestratrice** (toi) : tu cadres, tu écris les conditions et les plans, tu lances les agentes et les contrôles lourds, tu tiens le journal, tu appliques la règle de verdict. Tu n'écris aucun code de la fonctionnalité.
- **La relectrice de plan** confronte le plan d'un lot aux conditions et à la demande.
- **L'implémentrice** réalise un lot et répond de sa cohérence.
- **La relectrice d'implémentation** établit l'état de chaque condition, avec une preuve chaque fois que c'est possible.

Chaque agente est lancée avec l'outil `Agent`, type `general-purpose`, sans paramètre de modèle. Elle démarre sans l'historique de cette conversation : tout passe par son prompt et par les fichiers de travail.

## Fichiers de travail

Tout vit dans `docs/plans/<topic>/`, où `<topic>` est un nom court en kebab-case. Ces fichiers restent hors de l'index git.

| Fichier | Autrice | Contenu |
|---|---|---|
| `demande.md` | orchestratrice | demande d'origine mot pour mot, faits relevés et décisions du cadrage, budget de vérification, retours de l'essai |
| `conditions.md` | orchestratrice | conditions numérotées et rangées par lot, historique des changements |
| `journal.md` | orchestratrice | branche, phase, lot et tour en cours, SHA de base de chaque lot, identifiants des agentes, une ligne par événement avec les tokens et la durée de chaque agente quand l'outil les donne |
| `refus-declines.md` | orchestratrice | refus non prouvés déclinés, avec lot, constat et motif, en deux sections : `Plan` et `Implémentation` |
| `lot-<L>/plan.md` | orchestratrice | plan du lot |
| `lot-<L>/changements-plan-<N>.md` | orchestratrice | passages du plan modifiés depuis la relecture N-1, à partir du tour 2 |
| `lot-<L>/relecture-plan-<N>.md` | relectrice de plan | relecture du tour N |
| `lot-<L>/rapport-implementation-<N>.md` | implémentrice | rapport du tour N |
| `lot-<L>/relecture-<N>.md` | relectrice d'implémentation | relecture du tour N |

Tiens `journal.md` à jour à chaque étape, car il permet la reprise après une compaction du contexte, une interruption ou un redémarrage.

## 1. Cadrage

1. Le dépôt est un dépôt git. Dans le cas contraire, arrête-toi et demande comment procéder.
2. Si `docs/plans/<topic>/journal.md` existe déjà pour ce sujet, résume l'état et propose la reprise avec un nouveau crédit de tours.
3. Explore le code concerné. Les faits se cherchent dans le dépôt, les décisions se demandent à l'utilisateurice. Relève en particulier les faits qui bornent les conditions, et note-les dans `demande.md` :
   - où le résultat s'exécute et comment on y accède : scène, page, plateforme, environnement, entrée de l'utilisateurice ;
   - qui appelle le code touché, et ce qui en dépend ;
   - quels tests couvrent déjà ce comportement ;
   - quelle référence fait foi : spécification, maquette, comportement existant.
4. Pose tes questions par tours : toutes les questions ouvertes d'un coup, numérotées, chacune avec ta recommandation. Limite-toi aux décisions qui changent une condition, le découpage ou le budget de vérification. Continue jusqu'à ce qu'aucune question ne reste.
5. Joins au premier tour les **questions de conduite** auxquelles le dépôt ne répond pas, car leur réponse arrive sinon tard, par des refus répétés. Par exemple :
   - quelle source prévaut quand deux références divergent (spécification et guide, maquette et charte…) ;
   - quand un point ne peut pas être atteint : meilleur effort, avec les exceptions nommées au rapport final, ou arrêt strict ;
   - jusqu'où les tests et les contrôles s'étendent au-delà du périmètre de la fonctionnalité.

   Leurs réponses deviennent des conditions du socle pour cette demande.
6. Fixe le **budget de vérification** :
   - contrôles bon marché (test ciblé, script ad hoc, vérification d'un fichier) : libres pendant les itérations ;
   - contrôles lourds (compilation complète, suite de tests entière, scénario de bout en bout) : nomme-les, et demande s'ils sont autorisés.
7. Fixe le **découpage**, avec ta recommandation :
   - **direct** : un seul lot. Il convient à une fonctionnalité locale que les conditions suffisent à juger.
   - **squelette d'abord** : le lot 1 est une tranche fine qui traverse tout le système de bout en bout. Chaque composant existe et se branche aux autres avec un comportement minimal. Les lots suivants épaississent le squelette (contenu, textes, équilibrage, cas fins). Recommande ce mode quand la fonctionnalité traverse plusieurs composants, ou quand l'utilisateurice doit l'essayer pour la juger.
8. Si l'arbre de travail contient des modifications non commitées, demande quoi en faire.

## 2. Conditions de validation

Écris `conditions.md` à partir de la demande et du cadrage, avant tout plan. Une condition est un énoncé observable de ce qui est vrai quand le lot est terminé. Elle décrit uniquement un résultat attendu. Les moyens d'y arriver et de le vérifier (fichiers, structure du code, algorithme, bibliothèque, test, outil) appartiennent au plan. Par exemple, « la commande `list` affiche les entrées triées par date » est une condition, et « trier avec `sorted` dans `list.py` » est un moyen. Quand la demande porte elle-même sur un moyen, par exemple remplacer une bibliothèque, ce moyen est le résultat attendu.

Numérote les conditions `C1`, `C2`… et range-les par lot. Un lot de squelette porte des conditions de structure : le chemin complet fonctionne de bout en bout, et chaque pièce est présente. Les détails de finition appartiennent aux lots suivants. Une condition acquise à un lot reste due aux lots suivants.

Le socle s'applique à chaque lot :

- `S1` Le diff du lot se limite au périmètre du lot.
- `S2` Aucun test existant n'est supprimé ou affaibli.
- `S3` Le diff n'introduit aucun reste de débogage ni code mort.
- `S4` Le code, les commentaires, les messages de commit et la documentation respectent les conventions du projet (`CLAUDE.md`, `.claude/rules/`, style du code voisin, découplage).
- `S5` `docs/<feature>.md` décrit l'état de la fonctionnalité au commit relu.

Les conditions de conduite issues du cadrage s'y ajoutent : `S6`, `S7`…

Présente le découpage et les conditions à l'utilisateurice et attends sa validation. Ensuite, tu peux ajouter ou préciser une condition. Seule l'utilisateurice retire ou assouplit une condition validée, et elle le fait à l'essai. Note chaque changement dans `conditions.md`.

Quand une relectrice signale une ambiguïté ou une décision qui reviendrait à l'utilisateurice, tranche toi-même dans le sens qui respecte toutes les conditions validées. Précise la condition et consigne la décision pour le rapport final.

## 3. Boucle d'un lot

Au premier lot, crée la branche `feature/<topic>`, ou suis la convention visible dans les branches du dépôt. À chaque lot, crée `lot-<L>/` et note le SHA de base du lot dans `journal.md`.

### Plan

Écris `lot-<L>/plan.md`. Une implémentrice qui n'a pas vu cette conversation doit pouvoir l'exécuter : choix d'architecture, comportements attendus, décisions de produit ou de design, ordre des étapes, et les faits du code sur lesquels reposent ces choix. Cite un fichier, une fonction ou une ligne quand un fait fonde un choix. Le reste des moyens se décrit au niveau où l'implémentrice le retrouvera dans le code, car chaque détail prescrit est un détail de plus à relire et à contredire. Reprends les remarques non bloquantes des lots précédents qui concernent ce lot.

Pour chaque point démontrable, le plan propose son critère de validation et l'outil qui le vérifie, existant ou à développer, dans le respect du budget. Le plan d'un lot de squelette propose en général un test de bout en bout, que les lots suivants étendent. Un point sans démonstration possible reste dans le plan : la relecture le jugera au mieux.

Les moyens du plan sont des suggestions. L'implémentrice peut en choisir d'autres qui atteignent les mêmes conditions, à condition de déclarer l'écart. Seules les conditions engagent.

### Relecture du plan (3 relectures au plus)

Lance une relectrice de plan neuve et applique la règle de verdict, avec toi comme autrice.

À partir du tour 2, écris d'abord `lot-<L>/changements-plan-<N>.md` : la liste des passages du plan modifiés depuis la relecture précédente, chacun repéré par sa section ou son étape, sans motif ni avis. La relectrice les examine en premier, puis fait sa passe globale, sans lire les relectures précédentes.

Pour corriger un refus, change le choix qu'il met en cause, et ajoute un détail de moyen seulement si le refus porte sur ce détail.

Relance une relecture seulement si un refus prouvé t'a fait changer une étape ou un choix du plan. Une correction ponctuelle (un nom, une référence `fichier:ligne`, un commentaire) et un refus non prouvé intégré se font sans nouvelle relecture, car la relecture d'implémentation jugera le résultat contre les conditions.

Si la troisième relecture laisse un refus prouvé de fond, arrête-toi et remets le rapport final.

### Implémentation

Lance l'implémentrice et traite son statut :

- `TERMINÉ` : passe à la relecture.
- `BLOQUÉE` : réponds à sa question, ou amende le plan sans nouvelle relecture de plan, puis reprends-la. Un second blocage sur le même point arrête le skill : remets le rapport final.

### Relecture de l'implémentation et passe lourde (5 tours au plus)

À chaque tour :

1. Si le budget autorise des contrôles lourds, lance-les en arrière-plan (`Bash`, `run_in_background: true`) sur le commit à relire.
2. Lance la relectrice. Tour 1 du lot : une relectrice neuve, pour une relecture complète. Tours suivants : la même relectrice, pour une relecture ciblée sur le correctif.
3. Lis `relecture-<N>.md` et attends la fin des contrôles lourds. Un échec de contrôle lourd devient un `REFUS PROUVÉ`, avec sa sortie comme preuve.
4. Applique la règle de verdict. S'il reste un refus, renvoie la relecture et les échecs à l'implémentrice. Tours 1 à 3 : reprends la même implémentrice (`SendMessage` vers son identifiant). Tours 4 et 5 : lance une implémentrice neuve, pour sortir d'un éventuel blocage.
5. Lis `rapport-implementation-<N>.md` et copie chaque refus décliné, son constat et son motif dans `refus-declines.md`.
6. Si le code a changé, lance le tour suivant.

Le lot est accepté quand la règle de verdict accepte la dernière relecture et que les contrôles lourds ont réussi sur le même commit. Passe alors au lot suivant, ou à l'essai après un lot de squelette.

## Règle de verdict

La relectrice donne un état à chaque condition :

| État | Effet |
|---|---|
| `VALIDÉE PAR PREUVE` | acquise |
| `VALIDÉE PAR LECTURE` | acquise, signalée au rapport final |
| `REFUS PROUVÉ` | l'autrice corrige |
| `REFUS NON PROUVÉ` | l'autrice décide : intégrer, ou décliner avec un motif écrit |
| `NON VÉRIFIABLE` | contrôle manuel listé pour l'utilisateurice au rapport final |

L'autrice est l'implémentrice pour le code, et toi pour le plan. Un refus est légitime seulement quand l'observé (code, spécification, faits) ne permet pas d'accomplir une condition du lot ou un objectif de la demande. Le reste est une remarque non bloquante, y compris un détail que l'implémentrice peut trancher en implémentant : consigne-la pour un lot suivant ou pour le rapport final.

Une relecture est acceptée quand elle ne contient aucun `REFUS PROUVÉ` et que l'autrice a décliné tous les `REFUS NON PROUVÉ` restants. Le code est alors inchangé depuis la relecture, donc aucune nouvelle relecture n'est nécessaire.

Dès que le code change après une relecture, une nouvelle relecture est nécessaire. Si le plafond est atteint à ce moment, arrête-toi : le travail reste sur sa branche et le rapport final indique l'arrêt au plafond. Tu déclares acceptée seulement une implémentation que cette règle accepte.

## 4. Essai du squelette

Après l'acceptation du lot de squelette, arrête-toi et remets à l'utilisateurice :

- ce que fait le squelette, et ce qui manque volontairement ;
- comment l'essayer : commandes, scénario à suivre ;
- les lots de finition prévus, avec leurs conditions.

Note ses retours dans `demande.md`. Ajuste avec elle les conditions et la liste des lots de finition, puis reprends la boucle seule.

## 5. Clôture

Remets le rapport final à l'utilisateurice dans la conversation :

1. Verdict : accepté, arrêt au plafond, ou arrêt sur blocage, et le lot concerné.
2. Après un arrêt, les verrous : regroupe les refus restants et la question du blocage en verrous, pour que l'utilisateurice arbitre sans lire les fichiers des agentes. Pour chaque verrou :
   - observé : ce que montrent le code ou la spécification ;
   - voulu : ce que demande la condition ;
   - nœud : pourquoi les deux ne tiennent pas ensemble ;
   - pistes : les options, avec ta recommandation.
3. Tableau des conditions avec leur état et leur preuve.
4. Contrôles manuels à faire : conditions `NON VÉRIFIABLE` et `VALIDÉE PAR LECTURE`.
5. Points à arbitrer : refus déclinés (constat de la relectrice et motif de l'autrice), décisions que tu as prises seule, changements des conditions, écarts au plan déclarés par l'implémentrice, décisions de l'implémentrice avec leur justification et leurs écarts à la spécification, remarques non bloquantes restantes.
6. Résultat des contrôles lourds, ou mention de leur absence.
7. Branche et SHA de base du premier lot.
8. Métriques : relectures de plan et tours d'implémentation par lot ; refus prouvés trouvés par la relecture de plan, par la relecture complète, par les relectures ciblées et par les contrôles lourds ; refus non prouvés intégrés et déclinés ; tokens et durée cumulés des agentes.

Après une acceptation, supprime `docs/plans/<topic>/`. Après un arrêt, laisse le dossier en place pour la reprise.

Laisse la branche telle quelle : merge, push et pull request reviennent à l'utilisateurice. Le rapport au backlog revient à toi seule.

## Lancer une agente

Utilise ce prompt, complété, sans rien y ajouter. Un avis de ta part sur le travail orienterait son jugement.

```
Tu es <la relectrice de plan | l'implémentrice | la relectrice d'implémentation>.
Lis d'abord tes instructions : ${CLAUDE_SKILL_DIR}/prompts/<relectrice-plan | implementrice | relectrice-implementation>.md

Dépôt : <chemin absolu>
Dossier de travail : docs/plans/<topic>/
Lot : <L> (<squelette | finition | unique>)
Branche : <branche>
SHA de base du lot : <sha>
Tour : <N>
```

Pour reprendre l'implémentrice après une relecture, envoie-lui : « Tour N. Lis `lot-<L>/relecture-<N-1>.md` et applique la section "Tours suivants" de tes instructions. » Joins les sorties des contrôles lourds en échec. Une implémentrice neuve reçoit le prompt complet, avec cette même phrase en plus.

Pour reprendre la relectrice, envoie-lui : « Tour N. Commit relu au tour précédent : <sha>. Applique la section "Relecture ciblée" de tes instructions. »

Une agente qui n'est plus joignable, après un redémarrage par exemple, est remplacée par une agente neuve : prompt complet, plus la phrase de reprise. Une implémentrice qui remplace une autre en cours de tour reçoit en plus : « Reprise en cours de tour : applique la section "Reprise" de tes instructions. »

Lance chaque agente au premier plan (`run_in_background: false`), car ton étape suivante dépend de son résultat. Une reprise par `SendMessage` s'exécute en arrière-plan : attends sa notification avant de continuer.

Chaque agente répond par un message court : son statut et le chemin du fichier qu'elle a écrit.
