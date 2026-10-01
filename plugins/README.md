# plugins/

Agentes e plugins carregados na raiz antes de qualquer produção (ADR-001, seção 2).
Cópias sem alteração; para atualizar, substitua a pasta inteira e registre a versão aqui.

| Pasta | Versão | Origem | Licença | Papel no workflow |
|---|---|---|---|---|
| `agent-handoff/` | 0.4.2 | github.com/WillowRyu/agent-handoff | MIT | `/setup-handoff` → `/plan` → `/execute` → `/verify` (estado em `.handoff/`) |
| `engineering/` | 1.2.0 | Anthropic, `knowledge-work-plugins` | ver upstream | `/architecture` (ADRs), `/review`, `/debug`, `/deploy-checklist`, `/incident`, `/standup` |
| `claude-md-optimizer/` | 2.2.0 | github.com/wrsmith108/claude-md-optimizer | MIT | Mantém CLAUDE.md/AGENTS.md curtos (progressive disclosure) |

Notas:

- `claude-md-optimizer/` foi copiado sem a pasta `evals/` (fixtures de teste do autor, com
  CLAUDE.md/AGENTS.md de exemplo que poluiriam o repositório).
- `agent-handoff/hooks/` contém um hook `PreToolUse` que só autoriza escrita em `.handoff/**`;
  só roda se o plugin for instalado como plugin, não por estar nesta pasta.
- Estas pastas não são carregadas automaticamente pelo Claude Code. Instale com
  `claude plugin marketplace add ./plugins/<nome>` ou use `npx skills@latest add` conforme o
  README de cada um.
- Pendentes de entrar: `product-management` e `marketing` (Anthropic) e os agentes do
  workflow (maestro e demais), cujos arquivos ainda não foram enviados.
