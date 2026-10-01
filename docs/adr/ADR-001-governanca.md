# ADR-001: Governança de produto e engenharia (workflow ADR → formulários → plano → produção)

**Status:** Proposed
**Date:** 2026-10-01
**Deciders:** Responsável pelo ecossistema EXECUTAR (dono do produto)

## Context

O ecossistema EXECUTAR reúne vários produtos de lançamento sequencial (blog, workbook,
app, consultoria, emprego/vagas, infoprodutos e comunidade). Hoje o trabalho com o Claude
Code corre o risco de começar a produzir sem decisão registrada, sem escopo formalizado e
sem dependências mapeadas, o que gera retrabalho e erro de estruturação.

Forças em jogo:

- Vários produtos com dependências entre si (o blog depende de domínio, plataformas,
  CAPEX/OPEX, editorial, rotas, SMS, analytics e SEO).
- Agentes (Claude Code, plugins e skills) precisam de um workflow único e obrigatório
  para que o resultado seja reprodutível.
- Toda decisão e todo preenchimento precisam ficar registrados e rastreáveis no
  repositório, e não só no chat.
- A governança documental existe em dois modelos: o anterior (D01–D16, 30 documentos, dos
  quais 16 macros documentados e 14 especializados de classe E, Recomendada) e o novo
  (D01–D23, um documento macro por área, incluindo D17 PEP, D18 PCE, D19 PAC, D20 PPR,
  D21 PWB, D22 DRL e D23 PBL). Hierarquia vigente: Ecossistema → D01–D23 → Documento Macro
  → Documentos especializados → Workflows/Tarefas/Assets/Registros. Falta reconciliar os 30
  documentos antigos com as 23 áreas atuais; o Master Index novo ainda não tem as colunas
  Subáreas, Documentos especializados, Repositório/Drive, Owner, Status e Dependências.

## Decision

### 1. Ordem obrigatória do workflow

Para todo produto ou mudança de engenharia, sem exceção:

1. **ADR numerado** registra a decisão (`docs/adr/ADR-NNN-<slug>.md`).
2. **Formulário de Produto** preenchido pelos agentes (YAML, ver seção 3).
3. **Formulário de Produto para Engenharia** preenchido pelos agentes (YAML, ver seção 3).
4. **README** do produto gerado/atualizado a partir dos formulários.
5. **Plano de execução** (ex.: backend design), um por produto, formalizado em
   **issues do GitHub**.
6. **Produção** só começa depois dos passos 1–5.

Quem preenche os formulários são os agentes; o dono do produto revisa e aprova.

### 2. Bootstrap da raiz (Estratégia 07, adaptada)

Antes de produzir qualquer coisa, a **raiz do repositório** recebe os agentes e plugins
que sustentam o workflow, para o Claude Code ter a dependência e a estrutura carregadas:

| Item | Papel | Origem | Estado |
|---|---|---|---|
| Agente **handoff** (0.4.2) | Opera o ciclo `/setup-handoff` → `/plan` → `/execute` → `/verify` via `.handoff/` | `plugins/agent-handoff/` | Incluído |
| Plugin **engineering** (1.2.0) | `/architecture` (ADRs), `/review`, `/debug`, `/deploy-checklist`, `/incident`, `/standup` | `plugins/engineering/` | Incluído |
| **claude-md-optimizer** (2.2.0) | Mantém `CLAUDE.md`/`AGENTS.md` curtos por progressive disclosure | `plugins/claude-md-optimizer/` | Incluído |
| Plugin **product-management** (1.2.0) | Especificação, roadmap e síntese de pesquisa | `plugins/product-management/` | Incluído |
| Plugin **marketing** (1.2.0) | Campaign plan, conteúdo, SEO, e-mail, relatório de performance | `plugins/marketing/` | Incluído |
| Agentes do workflow (**maestro** e demais) | Orquestração e execução por área | Repositório EXECUTAR | Pendente |

A adaptação da Estratégia 07 consiste em carregar esses itens na raiz **antes** do passo 2
do workflow, e não sob demanda durante a produção.

A **Estratégia 07-Execução** (`docs/strategies/estrategia-07.md`) rege a execução: um único
arquivo de estado (`07-execucao/ESTADO.md`), progresso derivado de evidência e nunca por
declaração, WIP = 1, dado ausente registrado como `A DEFINIR`, no máximo 3 perguntas por
rodada quando algo bloqueia, e aprovação explícita antes de qualquer ação externa
(publicar, enviar, gastar, deploy). O estado deste ADR está em `07-execucao/ESTADO.md`.

### 3. Formulários em YAML

Os dois formulários obrigatórios existem como esquemas YAML versionados, convertidos das
imagens enviadas pelo dono do produto:

| Formulário | Esquema | Origem |
|---|---|---|
| **Produto** | `docs/forms/produto.yaml` | "Ficha de Caracterização da Iniciativa" (charter integrado PMBOK 8, 22 campos) |
| **Produto para Engenharia** | `docs/forms/produto-engenharia.yaml` | "Da Ideia ao Impacto" (Development Ready Package, ordem de desenvolvimento, papéis e gate) |
| Referência de ciclo | `docs/forms/ciclo-produto.yaml` | "Product Management, Ciclo de Desenvolvimento de Produto" (13 fases com perguntas de controle). Apoia os formulários; não os substitui |

Regras:

- Um arquivo preenchido por produto, em `docs/products/<produto>/produto.yaml` e
  `docs/products/<produto>/produto-engenharia.yaml`.
- Campo desconhecido fica com `valor: null` e `status: GAP`, nunca inventado; todo valor
  preenchido cita a `fonte`.
- O **gate obrigatório** do formulário de engenharia vale para todos os agentes: nenhuma
  implementação começa sem PRD + SPEC + Acceptance Criteria + Implementation Plan
  disponíveis e consistentes.
- A ordem do desenvolvimento de código (18 passos, da documentação aprovada ao
  monitoramento) é a do formulário de engenharia e se encaixa no ciclo do handoff:
  planejamento (passos 1–6) em `/plan`, implementação (7–10) em `/execute`, revisão e
  entrega (11–18) em `/verify` e nos checklists de deploy.

### 4. Ordem de lançamento dos macros

| # | Produto | Escopo | Dependências abertas |
|---|---|---|---|
| 1 | **Risco Cognitivo Blog** | Blog publicado, fechado como package modelo (editorial pronto e publicado, para dar track) | Burocracia (domínio, plataformas, CAPEX/OPEX), repositório único, editorial, copy, rotas, integração SMS, analytics, SEO, store/oficina de agentes, mapa cognitivo, linha editorial de lançamento |
| 2 | **Workbook de governança** | Material impresso com índice de controle e governança, dados para análise e consulta, linha editorial, pesquisa, todos os ADRs e READMEs, produção e operação (roadmap, sprints, controle de publicação, workflow de produção) | Currículo de cada agente: área, slash commands e capacidades |
| 3 | **Executar App** | Aplicativo (Expo, Android e iOS já desenvolvidos) | Migração do código para o GitHub, registro de ADRs, reaproveitamento do código existente, organização do repositório, integrações |
| 4 | **Executar Consultoria** | Consultoria, área de estudo, ferramentas e showroom | Executar App lançado |
| 5 | **Executar Emprego e Vagas** | Portfólio aplicado a vagas | Portfólio suficiente após os macros anteriores |
| 6 | **Infoprodutos, marketplace e comunidade** | Produtos físicos e digitais, ONG, comunidade | Macros anteriores |

Um macro só inicia produção quando o anterior estiver lançado, salvo decisão registrada
em novo ADR.

### 5. Linha transversal de estudos

Corre em paralelo a todos os macros: estudo e capacitação (incluindo o workbook), provas
e testes, formatação do portfólio e população do LinkedIn. Entra no workflow como produto
transversal, com seus próprios formulários e plano.

### 6. Currículo de agente

Cada agente tem um responsável por área e um "currículo" (nome, área, slash commands,
capacidades, limites). Esses currículos alimentam o workbook de governança (produto 2).

## Options Considered

### Option A: Workflow formal com ADR, formulários e plano antes da produção (escolhida)

| Dimension | Assessment |
|-----------|------------|
| Complexity | Média |
| Cost | Tempo inicial de preenchimento por produto |
| Scalability | Alta: o mesmo roteiro serve a todos os macros |
| Team familiarity | Alta: já usado com o agente handoff no Claude Code |

**Pros:** decisões rastreáveis; menos retrabalho; agentes com dependências carregadas;
README e issues derivados de fonte única.
**Cons:** atrasa o início da produção; exige disciplina nos formulários.

### Option B: Produzir direto e documentar depois

| Dimension | Assessment |
|-----------|------------|
| Complexity | Baixa |
| Cost | Baixo no início, alto em retrabalho |
| Scalability | Baixa |
| Team familiarity | Alta |

**Pros:** início rápido.
**Cons:** dependências descobertas tarde (domínio, SMS, analytics); sem registro das
decisões; resultado difícil de reproduzir entre agentes.

## Trade-off Analysis

O custo da Opção A é pago uma vez por produto e antes da produção; o custo da Opção B é
pago repetidamente, durante a produção, e cresce com o número de macros e agentes. Como o
ecossistema tem seis macros encadeados, a previsibilidade pesa mais que a velocidade
inicial.

## Consequences

- Fica mais fácil: retomar o trabalho em qualquer sessão, auditar decisões, gerar README e
  issues a partir dos formulários, montar o workbook com ADRs e currículos.
- Fica mais difícil: começar a produzir sem os formulários; mudar a ordem de lançamento
  sem novo ADR.
- A revisitar: a política de PR (rascunho ou não) por repositório e o significado exato de
  "autorizo" no workflow (hoje coberto só pela regra de aprovação explícita da Estratégia 07).

## Action Items

1. [x] Converter as imagens dos formulários em `docs/forms/*.yaml`.
2. [x] Subir na raiz o agente handoff, os plugins engineering, product-management e
       marketing e o claude-md-optimizer (`plugins/`).
3. [ ] Subir os agentes do workflow (maestro e demais), ainda não enviados.
4. [ ] Criar a branch `main` (hoje o repositório só tem a branch de trabalho) e abrir o PR.
5. [ ] Rodar `/setup-handoff` na raiz para gerar `.handoff/config.md`.
6. [ ] Preencher os formulários do produto 1 (Risco Cognitivo Blog) em
       `docs/products/risco-cognitivo-blog/` e abrir as issues do plano de execução.
7. [ ] Levantar e registrar as dependências abertas do blog: domínio, plataformas,
       CAPEX/OPEX, repositório único.
8. [ ] Definir o template do package modelo (editorial pronto e publicado).
9. [ ] Escrever o currículo de cada agente para o workbook.
10. [ ] Reconciliar os 30 documentos antigos com as 23 áreas D01–D23 e estender o Master
        Index com as colunas Subáreas, Documentos especializados, Repositório/Drive, Owner,
        Status e Dependências (ADR próprio).
11. [x] Registrar a Estratégia 07 (`docs/strategies/estrategia-07.md`) e iniciar o
        `07-execucao/ESTADO.md`.
12. [ ] Definir o fluxo "autorizo" (quem aprova, onde fica o registro da aprovação).
