# Estratégia 07-Execução: Prompt Adaptativo

Prompt genérico, não preso a um domínio. Cole o bloco abaixo no Claude Code (Plan Mode) ou
no Claude.ai, preenchendo `<ALVO>` com o que quer construir ou lançar (e
`<ESTÁGIOS>`/`<RESTRIÇÕES>`, se já souber). O resto se adapta ao domínio.

Origem: arquivo `Estratégia 07-Execução — Prompt Adaptativo.html`, enviado pelo dono do
produto. O bloco abaixo é o texto original, sem alteração. Uso neste repositório: ADR-001,
seção 2; estado vigente em `07-execucao/ESTADO.md`.

## Resumo operacional

1. Um único arquivo de estado (`07-execucao/ESTADO.md`), nunca duplicado.
2. Progresso derivado de evidência, nunca marcado como "pronto" por declaração.
3. Parada explícita quando falta informação; dado ausente vira `A DEFINIR`.
4. WIP = 1: um estágio em execução por vez.
5. Ação externa (publicar, enviar, gastar, deploy) exige aprovação explícita.

## Prompt

```text
## OBJECTIVE
Construir, para <ALVO>, um pipeline de execução em estágios com um único lugar de
verdade sobre o progresso, onde cada etapa só avança com evidência verificável —
nunca por declaração.

## CONTEXT
<context>
Esta estratégia já foi usada com sucesso em outros projetos porque impõe três coisas:
(1) um único arquivo de estado, nunca duplicado; (2) progresso derivado de evidência,
nunca marcado manualmente como "pronto"; (3) parada explícita quando falta informação,
em vez de inventar. Adapte a estrutura ao domínio de <ALVO> — não copie exemplos de
outro domínio.
</context>

## INPUT
<input>
- ALVO: <o que você quer construir, lançar ou resolver>
- ESTÁGIOS (opcional): <fases já conhecidas; se vazio, derive a partir do ALVO>
- RESTRIÇÕES conhecidas: <prazos, riscos, dependências externas, orçamento>
</input>

## CONSTRAINTS
- Criar UM arquivo de estado único (`07-execucao/ESTADO.md`) — nunca duplicar em outro lugar.
- Cada estágio tem: objetivo observável, entrada, critério de pronto (DoD) em uma frase,
  e evidência exigida antes de marcar concluído.
- WIP = 1: apenas um estágio "em execução" por vez.
- Nunca inventar dado ausente: registrar como `A DEFINIR`; se isso bloquear o próximo
  passo, perguntar (no máximo 3 perguntas por rodada).
- `CONCLUÍDO` só depois de saída + evidência + critério satisfeito — nunca por declaração.
- Ação externa (publicar, enviar, gastar dinheiro, deploy em produção) exige aprovação
  explícita antes de executar.
- Divergência entre fontes de informação se registra como decisão pendente; nunca se
  resolve em silêncio.
- Antes de criar qualquer ferramenta, arquivo ou agente novo, responder por escrito:
  já existe algo reutilizável? é realmente necessário? qual o custo de manter? como
  reverter se não funcionar?

## TOOLS
- Ferramentas de arquivo/código do ambiente → criar e atualizar `07-execucao/ESTADO.md`
  e os prompts de cada estágio.
- Busca (se disponível) → usar só para preencher lacunas factuais verificáveis, nunca
  para adivinhar uma decisão que é sua.

## EXECUTION
1. A partir de ALVO, listar os estágios necessários (ou usar os fornecidos em INPUT).
2. Para cada estágio, escrever um arquivo `NN-nome-do-estagio.md` com: OBJECTIVE, INPUT,
   CONSTRAINTS, EXECUTION, OUTPUT CONTRACT, VALIDATION, STOP CONDITIONS.
3. Criar `07-execucao/ESTADO.md` com uma linha por estágio: status
   (⬜ pendente · 🔄 em execução · ✅ concluído · ⛔ bloqueado), dono, evidência.
4. Executar o primeiro estágio elegível (dependências satisfeitas); atualizar o estado
   antes de seguir para o próximo.
5. Ao final de cada estágio, reportar o que foi feito e qual é o próximo nó elegível.

## OUTPUT CONTRACT
Um diretório com os prompts por estágio + `07-execucao/ESTADO.md` atualizado a cada
execução. Nenhuma etapa marcada concluída sem evidência anexada.

## VALIDATION
- Todo estágio no ESTADO.md tem status, dono e evidência (ou está `A DEFINIR` de forma explícita).
- Nenhum dado foi inventado; toda lacuna está marcada.
- Nenhuma ação externa ocorreu sem aprovação registrada.

## STOP CONDITIONS
Parar e reportar quando: faltar uma decisão que muda a arquitetura, uma ação externa
não tiver aprovação, ou o mesmo estágio falhar duas vezes seguidas.
```
