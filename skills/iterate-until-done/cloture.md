# Clôture

Tu arrives ici à l'acceptation du dernier lot, ou à un arrêt : plafond atteint, refus prouvé de fond au plan, second blocage. Le skill se termine avec ce fichier.

## Conventions à écrire

Avant de rédiger le rapport, relis les refus et les remarques de tous les lots. Quand plusieurs portent sur le même thème et révèlent une convention du projet écrite nulle part, ajoute une proposition : le texte à ajouter à `CLAUDE.md` ou à `.claude/rules/`, et les refus qui la motivent.

## Rapport final

Remets le rapport final à l'utilisateurice dans la conversation. Le corps contient ce qu'elle doit lire ou trancher, et l'annexe garde le reste pour qui veut vérifier.

Corps :

1. Verdict : accepté, arrêt au plafond, ou arrêt sur blocage, et le lot concerné.
2. Ce qui a été livré, en langage naturel, en suivant le récapitulatif point par point : ce qui est fait, ce qui s'en écarte et pourquoi.
3. Après un arrêt, les verrous : regroupe les refus restants et la question du blocage en verrous, pour que l'utilisateurice arbitre sans lire les fichiers des agentes. Pour chaque verrou :
   - observé : ce que montrent le code ou la spécification ;
   - voulu : ce que demande le récapitulatif ;
   - nœud : pourquoi les deux ne tiennent pas ensemble ;
   - pistes : les options, avec ta recommandation.
4. Propositions : celles qui attendent l'utilisateurice, chacune avec ce qu'elle change, son gain, son coût et ta recommandation, puis en une ligne celles que tu as adoptées.
5. Contrôles manuels à faire, avec leurs étapes : conditions `NON VÉRIFIABLE` et `VALIDÉE PAR LECTURE`, décrites par ce que l'utilisateurice vérifiera.
6. À trancher : refus déclinés (constat de la relectrice et motif de l'autrice), décisions que tu as prises seule, conditions retirées ou assouplies après la première relecture de plan.
7. Branche, SHA de base du premier lot, et résultat des contrôles lourds ou mention de leur absence.

Annexe :

- écarts au plan déclarés par l'implémentrice ;
- décisions de l'implémentrice, avec leur justification et leurs écarts à la spécification ;
- remarques non bloquantes restantes ;
- métriques : relectures de plan et tours d'implémentation par lot ; refus prouvés trouvés par la relecture de plan, par la relecture complète, par les relectures ciblées et par les contrôles lourds ; refus non prouvés intégrés et déclinés ; tokens et durée cumulés des agentes ;
- tableau des conditions avec leur état et leur preuve.

## Après le rapport

Après une acceptation, supprime `docs/plans/<topic>/`, car `S5` a déjà fait entrer dans la documentation du projet les décisions qui doivent durer. Après un arrêt, laisse le dossier en place pour la reprise.

Laisse la branche telle quelle : merge, push et pull request reviennent à l'utilisateurice. Le rapport au backlog revient à toi seule.
