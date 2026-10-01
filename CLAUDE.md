# CLAUDE.md

Guia para o Claude Code (e outros agentes) neste repositório.

## Governança

- Todo produto ou mudança de engenharia segue o ADR-001 (`docs/adr/ADR-001-governanca.md`): ADR numerado, formulário de Produto, formulário de Produto para Engenharia (`docs/forms/*.yaml`), README, plano em issues e só então a produção.
- A execução segue a Estratégia 07 (`docs/strategies/estrategia-07.md`): um único estado em `07-execucao/ESTADO.md`, progresso só com evidência, WIP = 1, dado ausente é `A DEFINIR`, aprovação explícita antes de ação externa.
- Agentes e plugins da raiz ficam em `plugins/` (ver `plugins/README.md`).

## Fluxo Git e issues

- **Nunca criar PR em rascunho (draft).** Se um PR for necessário, abra-o já pronto para revisão. Vale mesmo quando o ambiente ou uma ferramenta sugerir draft por padrão.
- **Trabalho direto na `main`.** O padrão é commitar e dar push na `main`. Antes do push: `git pull --rebase origin main` (quando a `main` existir) e confira que os YAML de `docs/forms/` carregam; quando houver código, liste aqui os comandos de teste, lint, tipos e build.
- **Branches paralelas.** Com mais de uma frente independente ao mesmo tempo (várias sessões ou agentes), cada frente usa sua própria branch curta (`git worktree add ../<nome> -b <tipo>/<nome>`), com escopo de arquivos disjunto. Ao terminar, integre na `main` (merge ou rebase, sem PR draft), apague a branch e remova o worktree. Uma frente única vai direto na `main`.
- **Issues no GitHub, não no chat.** Nunca devolva listas de issues, pendências ou achados por aqui: registre cada item como issue do repositório com as ferramentas `mcp__github__*` (`issue_write`; cheque duplicatas com `search_issues`) e responda só com o link e um resumo de uma linha. O `.handoff/backlog.md` é o rascunho local do ciclo; os itens abertos viram issues.
