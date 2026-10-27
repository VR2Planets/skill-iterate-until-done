# iterate-until-done

Skill Claude Code qui implémente une fonctionnalité en autonomie. L'orchestratrice cadre la demande avec toi et écrit les conditions de validation, puis des agentes indépendantes relisent le plan, implémentent et relisent chaque lot jusqu'à validation. Le déroulé complet est dans [`skills/iterate-until-done/SKILL.md`](skills/iterate-until-done/SKILL.md).

## Installer

Le dépôt est un marketplace de plugins Claude Code qui contient un seul plugin, `iterate-until-done`. L'installation en plugin est la méthode recommandée, car Claude Code la met à jour depuis ce dépôt.

Dans une session Claude Code (terminal, ou onglet Code de Claude Desktop) :

```
/plugin marketplace add VR2Planets/skill-iterate-until-done
/plugin install iterate-until-done@skill-iterate-until-done
```

Les mêmes commandes s'exécutent hors session, dans PowerShell comme dans bash :

```powershell
claude plugin marketplace add VR2Planets/skill-iterate-until-done
claude plugin install iterate-until-done@skill-iterate-until-done
```

Le raccourci `VR2Planets/skill-iterate-until-done` désigne le dépôt GitHub. Si le clonage échoue faute de clé SSH, ajoute le marketplace par son URL HTTPS : `https://github.com/VR2Planets/skill-iterate-until-done.git`.

### Portée

- **Globale** (par défaut, `--scope user`) : le skill sert dans tous tes projets. Claude Desktop lit les mêmes réglages que le terminal, donc ses sessions locales le voient aussi.
- **Projet** (`--scope project`) : lance les deux commandes depuis la racine du projet, chacune avec `--scope project`. Elles écrivent `extraKnownMarketplaces` et `enabledPlugins` dans `.claude/settings.json`, que tu commites pour le partager. Chaque membre de l'équipe installe ensuite le plugin de son côté avec `claude plugin install iterate-until-done@skill-iterate-until-done --scope project`.
- **Locale** (`--scope local`) : ce projet, pour toi seule, dans `.claude/settings.local.json`.

## Utiliser

```
/iterate-until-done:iterate-until-done <fonctionnalité à implémenter>
```

Un skill de plugin porte le nom de son plugin en préfixe. Le skill se lance seulement à ta demande (`disable-model-invocation: true`).

## Mettre à jour

Le plugin ne déclare aucune `version`, donc chaque commit de `main` est une nouvelle version.

```
/plugin marketplace update skill-iterate-until-done
/reload-plugins
```

Hors session :

```powershell
claude plugin marketplace update skill-iterate-until-done
claude plugin update iterate-until-done@skill-iterate-until-done
```

Pour des mises à jour automatiques, ouvre `/plugin` > **Marketplaces**, choisis `skill-iterate-until-done`, puis **Enable auto-update**.

## Alternative : clone et lien

Cette méthode donne le nom court `/iterate-until-done`, et `git pull` dans le clone sert de mise à jour. Garde une seule des deux méthodes, sinon le skill apparaît deux fois.

PowerShell (une jonction ne demande pas de droits administrateur) :

```powershell
git clone https://github.com/VR2Planets/skill-iterate-until-done.git "$HOME\src\skill-iterate-until-done"
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
New-Item -ItemType Junction -Path "$HOME\.claude\skills\iterate-until-done" -Target "$HOME\src\skill-iterate-until-done\skills\iterate-until-done"
```

bash :

```bash
git clone https://github.com/VR2Planets/skill-iterate-until-done.git ~/src/skill-iterate-until-done
mkdir -p ~/.claude/skills
ln -s ~/src/skill-iterate-until-done/skills/iterate-until-done ~/.claude/skills/iterate-until-done
```

Pour un seul projet, crée le lien dans `<projet>/.claude/skills/iterate-until-done`. Il pointe vers un chemin de ta machine : ajoute-le à `.git/info/exclude` du projet.

Mise à jour, dans PowerShell comme dans bash (adapte le chemin du clone) :

```powershell
git -C "$HOME\src\skill-iterate-until-done" pull
```

## Maintenir le skill

- `skills/iterate-until-done/SKILL.md` : les instructions de l'orchestratrice. Elle désigne les instructions des agentes par `${CLAUDE_SKILL_DIR}/prompts/`, que Claude Code remplace par le dossier du skill, en plugin comme en lien.
- `skills/iterate-until-done/prompts/` : les instructions de la relectrice de plan, de l'implémentrice et de la relectrice d'implémentation.
- `.claude-plugin/marketplace.json` et `.claude-plugin/plugin.json` : les manifestes du marketplace et du plugin. Avant de pousser, vérifie-les avec `claude plugin validate .` à la racine du dépôt.
- `.claude/rules/personal-rules.md` : les règles d'écriture de l'auteur, que Claude Code applique aux sessions ouvertes dans ce dépôt.

Pour essayer une modification sans l'installer, lance `claude --plugin-dir <chemin du clone>`.
