# ADR-002: Conta Cloudflare padrão do ecossistema = Hub.executar

**Status:** Accepted
**Date:** 2026-10-01
**Deciders:** Dono do produto (autorização explícita: "seguir com a conta hub.executar como default, que tem todas as permissões; configure e migre tudo para lá")

## Context

Os Workers do ecossistema estavam divididos entre duas contas Cloudflare:

| Conta | ID | Subdomínio workers.dev | O que tinha |
|---|---|---|---|
| **Hub.executar@gmail.com's Account** | `92fdc1b556435f2e3d62517f3041422e` | `hub-executar` | D1 `hub-editorial-db`, `hub-editorial-db-preview`, `hub-auth-db`; KV `hub-auth-storage`; Worker `workflows-starter-template` (fila de agentes) |
| Conta legada | `88b77e623d27e9040c9e3013aebce014` | `executar-rotina-8b7` | Workers `react-router-hono-fullstack-template`, `risco-cognitivo-blog`, `workflows-starter-template`, `blog-full-stack`; Workers Builds ligados ao GitHub |

Bindings apontavam para recursos de uma conta enquanto o Worker rodava na outra. Por isso o
preview do Hub Editorial respondia `/api/health` → `503 db unavailable`. O token de deploy
das sessões de agente (`CLOUDFLARE_API_TOKEN` via proxy) só acessa a conta Hub.executar.

## Decision

1. **Hub.executar (`92fdc1b5…`) é a conta padrão** para Workers, D1, KV, R2, Workflows e
   segredos de todos os repositórios do ecossistema.
2. URLs públicas canônicas usam `*.hub-executar.workers.dev` até existir domínio próprio:
   - Hub Editorial (app + API): `https://react-router-hono-fullstack-template.hub-executar.workers.dev`
   - Auth do Hub: `https://hub-editorial-auth.hub-executar.workers.dev`
   - Blog: `https://risco-cognitivo-blog.hub-executar.workers.dev`
   - Fila de agentes / CMS: `https://workflows-starter-template.hub-executar.workers.dev`
3. Os bindings dos repositórios apontam só para recursos dessa conta. O deploy é feito com
   `CLOUDFLARE_API_TOKEN=proxy-managed npx wrangler deploy` (permissão já concedida ao agente).
4. A conta legada não recebe recursos novos. Os recursos criados nela em 2026-10-01 (D1
   `c76c6f9f…`, `dcf8650d…`, `8d1752ee…`; KV `e7af0103…`) e os Workers antigos ficam
   inalterados até a desativação, que é uma ação destrutiva e exige aprovação explícita
   (issue própria).

## Consequences

- Um token, uma conta: o agente consegue migrar, popular D1 e publicar sem conectores extras.
- Os links antigos (`*.executar-rotina-8b7.workers.dev`) continuam no ar com a versão antiga
  até a desativação. Os QR codes e links internos passam a apontar para `hub-executar`.
- O Workers Builds (CI por push no GitHub) precisa ser reconectado na conta Hub.executar
  pelo painel, porque exige a instalação do app Cloudflare no GitHub. Até lá, o deploy é feito
  via wrangler.
- Segredos (`RESEND_API_KEY`, `ANTHROPIC_API_KEY`, `AGENT_TOKEN` etc.) são configurados
  nessa conta.

## Action Items

1. [x] Registrar a decisão (este ADR) e a regra no `CLAUDE.md` de cada repositório.
2. [ ] Hub Editorial: bindings para os D1/KV da Hub.executar, seed, deploy do auth e do app.
3. [ ] Blog: integrar a `main`, apontar a base URL para `hub-executar`, publicar.
4. [ ] Fila de agentes: `BLOG_URL` para `hub-executar`, publicar.
5. [ ] Issues: reconectar Workers Builds, segredos pendentes, desativação da conta legada.
