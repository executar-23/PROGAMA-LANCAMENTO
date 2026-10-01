# ESTADO: pipeline de governança (ADR-001)

Estratégia 07-Execução (`docs/strategies/estrategia-07.md`). Único arquivo de estado deste
pipeline; não duplicar. WIP = 1. `✅` só com saída + evidência + critério satisfeito.

Legenda: ⬜ pendente · 🔄 em execução · ✅ concluído · ⛔ bloqueado

**Alvo:** formalizar a governança de produto e engenharia do ecossistema EXECUTAR e
deixar a raiz pronta para a produção do produto 1 (Risco Cognitivo Blog).
**Restrições conhecidas:** `main` ainda não existe no GitHub; agentes do workflow ainda
não enviados; sem ação externa sem aprovação.

| # | Estágio | DoD (uma frase) | Status | Dono | Evidência |
|---|---|---|---|---|---|
| 01 | Redigir ADR-001 | ADR-001 existe em `docs/adr/` com contexto, decisão, opções e itens de ação | ✅ | Claude / revisão: dono do produto | commits `f9317d6`, `97bd111`; `docs/adr/ADR-001-governanca.md` |
| 02 | Converter formulários em YAML | Os 2 formulários (+ ciclo de referência) existem como YAML válido, com contagem de itens igual à das imagens | ✅ | Claude | `docs/forms/*.yaml`; 22 campos, 8+8 itens, 18 passos, 13 fases (carregam em PyYAML) |
| 03 | Registrar Estratégia 07 | Texto original da estratégia versionado e citado no ADR | ✅ | Claude | `docs/strategies/estrategia-07.md`; ADR-001 seção 2 |
| 04 | Carregar plugins na raiz | handoff, engineering, product-management, marketing e claude-md-optimizer em `plugins/`, com origem e licença | ✅ | Claude | `plugins/README.md`; pastas dos 5 plugins |
| 05 | Carregar agentes do workflow | Maestro e demais agentes versionados na raiz | ⛔ | Dono do produto | `A DEFINIR`: arquivos ainda não enviados |
| 06 | Criar `main` | `main` existe com o histórico da branch de trabalho (regra: trabalho direto na `main`, sem PR draft) | ✅ | Claude (autorizado: "execute está autorizado a seguir") | `main` criada em 2026-10-01 a partir da branch de trabalho |
| 07 | Rodar `/setup-handoff` | `.handoff/config.md` gerado na raiz | ⬜ | Claude | `A DEFINIR`; depende do 06 para o repositório ter base estável |
| 08 | Preencher formulários do produto 1 | `docs/products/risco-cognitivo-blog/produto.yaml` e `produto-engenharia.yaml` preenchidos, lacunas marcadas `GAP` | ⬜ | Agentes; revisão: dono do produto | `A DEFINIR`: depende de dados do blog (domínio, plataformas, CAPEX/OPEX) |
| 09 | Reconciliar D01–D16 (30 docs) com D01–D23 | Mapa antigo → novo aprovado e Master Index estendido | ⬜ | Dono do produto | `A DEFINIR` (ADR próprio) |

## Próximo nó elegível

Estágios 05 e 06 estão bloqueados por entrada ou aprovação. O próximo estágio executável
sem bloqueio é o **08**, mas ele precisa dos dados do blog (ver "Decisões pendentes").

## Decisões pendentes

2. Enviar os arquivos dos agentes do workflow (estágio 05).
3. Informar os dados do produto 1 que faltam: domínio, plataformas, CAPEX/OPEX e repositório
   único do blog (estágio 08).
4. Definir o fluxo "autorizo" (ADR-001, item 12).
5. Divergência registrada, não resolvida: o `CLAUDE.md` do repositório Risco-cognitivo-blog
   (ADR-01) manda abrir PR sem rascunho; as instruções desta sessão mandam rascunho. Qual
   regra vale para este repositório?
