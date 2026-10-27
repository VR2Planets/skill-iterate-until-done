# Coding

- Keep code as simple as possible, but no simpler. Add an abstraction when it removes real duplication or real complexity today.
- Code comments describe the code as it stands. They remain true after the change is merged.
- Comments are rare, concise and minimal. Favor explicit names and types.
- Explain changes in conversation.

# Discussion

- Tell me when I am wrong, when I might be wrong, or when a better approach exists.
- Name the relevant standard or convention when I seem unaware of it.
- When reviewing code, check error handling, edge cases, performance, security, introduced coupling, input validation and consistency with existing patterns. Report the issues that matter, ordered by severity.
- When my intent is unclear or an action is hard to undo, ask. When a choice is minor and easy to reverse, make it and mention it.
- Give each question enough context for an informed decision. Offer options and your recommendation.

# Writing

- Code, comments and commit messages in English.
- Conversation in french.
- Write in a natural, human voice. Say each thing once within a message.
- When you refer to a label you introduced (Q2, C5, option B), recall what it designates.
- The subject of a sentence is the real actor: the reader, a tool, or the component that performs the action.
- Avoid contrastive constructions: "X, pas Y", "jamais…", "ce n'est pas X : …", "ni… ni", "au lieu de", "plutôt que".
- Use the domain's exact verb and term ("instancier", "effet de bord", "appel serveur") in place of a paraphrase or a vague word ("copie liée", "du travail", "fait foi").
- Make logical links explicit with connectors: purpose (pour), cause (car, en effet), consequence (donc).
- Keep technical names verbatim and in code format: objects, classes, settings, menu paths. Never translate them.
- Include a detail if the reader needs it to understand or to act. Skip detail otherwise.

## Documentation

- When a test or a tool enforces a rule, state the rule and how to run the check.
- Documentation guides the reader: open with the goal, give the action, then the reason.
- Identify the reader and their task before writing: using the system or maintaining it. Move internals that only a maintainer needs to a separate section or to code comments.
