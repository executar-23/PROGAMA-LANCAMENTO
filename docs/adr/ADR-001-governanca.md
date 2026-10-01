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

| Item | Papel | Origem |
|---|---|---|
| Agente **handoff** | Opera o ciclo `/setup-handoff` → `/plan` → `/execute` → `/verify` via `.handoff/` | bundle agent-handoff |
| Plugin **engineering** (1.2.0) | `/architecture` (ADRs), `/review`, `/debug`, `/deploy-checklist`, `/incident`, `/standup` | Anthropic, `knowledge-work-plugins` |
| Plugin **product-management** | Especificação e priorização de produto | Anthropic, `knowledge-work-plugins` |
| Plugin **marketing** (campaign plan) | Plano de campanha, editorial, copy | Anthropic, `knowledge-work-plugins` |
| Agentes do workflow (**maestro** e demais) | Orquestração e execução por área | Repositório EXECUTAR |

A adaptação da Estratégia 07 consiste em carregar esses itens na raiz **antes** do passo 2
do workflow, e não sob demanda durante a produção.

### 3. Formulários em YAML

Os dois formulários passam a existir como esquemas YAML versionados:

- `docs/forms/produto.yaml`: formulário de **Produto**.
- `docs/forms/produto-engenharia.yaml`: formulário de **Produto para Engenharia**.

Regras: um arquivo preenchido por produto (`docs/products/<produto>/produto.yaml` e
`produto-engenharia.yaml`); campos não conhecidos ficam vazios e marcados `GAP`, nunca
inventados; os agentes citam a fonte de cada valor.

> **Pendente:** a conversão das duas imagens dos formulários para YAML depende dos
> arquivos originais. Eles não chegaram à sessão (só o zip do plugin engineering foi
> recebido). Os esquemas serão gravados neste PR assim que as imagens forem reenviadas.

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
- A revisitar: o conteúdo exato dos formulários (depende das imagens), a definição formal
  da Estratégia 07 e a política de PR (rascunho ou não) por repositório.

## Action Items

1. [ ] Reenviar as duas imagens dos formulários e convertê-las em `docs/forms/*.yaml`.
2. [ ] Criar a branch `main` e subir na raiz: agente handoff, plugin engineering 1.2.0,
       product-management, marketing e agentes do workflow.
3. [ ] Preencher os formulários do produto 1 (Risco Cognitivo Blog) e abrir as issues do
       plano de execução.
4. [ ] Levantar e registrar as dependências abertas do blog: domínio, plataformas,
       CAPEX/OPEX, repositório único.
5. [ ] Definir o template do package modelo (editorial pronto e publicado).
6. [ ] Escrever o currículo de cada agente para o workbook.
7. [ ] Registrar em ADR próprio a definição da Estratégia 07 e o fluxo "autorizo".
