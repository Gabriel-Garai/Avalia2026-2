# Projeto #20 — Gerenciador de Horas Complementares

**Nome:** Gabriel Martins dos Santos Garai  
**RA:** 2026108062  
**Turma:** 2A  
**Chave Única Oficial:** P26-2A-9E83-CBA6-DAAD-B327  
**URL_CLOUDFLARE:** A_PREENCHER_APOS_DEPLOY  
**Repositório:** https://github.com/Gabriel-Garai/Avalia2026-2

## Objetivo
Sistema responsivo em HTML5, CSS3 e JavaScript puro com login Discord OAuth2, cadastro de eventos/cursos/projetos de extensão, certificado externo, dashboard com barras de progresso por ensino, pesquisa e extensão e persistência D1.

## Arquitetura
- `public/`: frontend estático Vanilla JS.
- `functions/api/auth/discord/`: início do OAuth2 e callback.
- `functions/api/auth/logout.js`: logout por POST.
- `functions/api/me.js`: usuário autenticado.
- `functions/api/activities/`: listagem, inclusão e exclusão restritas ao dono.
- `functions/api/health.js`: diagnóstico de disponibilidade do D1.
- `lib/`: validação de negócio e sessão assinada.
- `migrations/`: tabelas D1.
- `AGENTS.md`, `.opencode/agents/`, `skills/` e `.opencode/skills/`: configuração para o harness OpenCode.

## Premissa acadêmica explícita
O enunciado exemplifica teto de **60 horas em uma única modalidade**, sem apresentar limites oficiais nem meta curricular exata. Para uma demonstração, adotamos **60h para cada eixo e 180h como meta demonstrativa**. Os registros são somados como contabilizados automaticamente dentro do teto; não existe aprovação administrativa. Essa regra deve ser revisada se o professor fornecer valores definitivos.

## Segurança
- OAuth2 `authorization_code` Discord, scope `identify`, checagem de `state` e troca de token no servidor.
- Cookie de sessão assinado com HMAC SHA-256, HttpOnly, Secure, SameSite=Lax e expiração de 7 dias.
- Autoria derivada do Discord, consultas D1 parametrizadas por `user_id`.
- Validações feitas no servidor; INSERT condicionado ao teto por categoria.
- Segredos exclusivamente em variáveis do Cloudflare: `DISCORD_CLIENT_ID`, `DISCORD_CLIENT_SECRET`, `SESSION_SECRET` (chave longa aleatória).
- Não publicar segredos e não versionar `.dev.vars`.

## Configuração Cloudflare Pages
1. Crie um banco D1 chamado `db-horas-garai` e copie o `database_id` em `wrangler.jsonc`.
2. Dentro deste diretório, com Node.js instalado, execute `npx.cmd wrangler login` (Windows), depois `npx.cmd wrangler d1 migrations apply DB --remote`.
3. No Cloudflare, conecte **o fork** `Gabriel-Garai/Avalia2026-2` ao Pages: **Root directory** `P26-2A-9E83-CBA6-DAAD-B327`, **Build command** vazio, **Build output** `public`.
4. Confirme o binding do banco `DB` em **Settings → Functions → D1 bindings**, caso o painel não o tenha associado a partir do arquivo de configuração.
5. Crie uma aplicação em https://discord.com/developers/applications. Em OAuth2, adicione exatamente `https://<seu-dominio>/api/auth/discord/callback`.
6. Configure os três segredos como **Environment variables** no Pages. `SESSION_SECRET` deve ser uma sequência aleatória longa, exclusiva.
7. Faça redeploy. Teste login, cadastro, consulta, exclusão e endpoint `/api/health` (esperado `{"ok":true,"db":true}`).
8. Atualize `URL_CLOUDFLARE` acima; execute `node ../validar-local.mjs P26-2A-9E83-CBA6-DAAD-B327` a partir da raiz do fork (não desta subpasta), conforme README oficial do professor.
9. Abra Pull Request para o repositório original e entregue URLs no AVA.

## Validação local
Execute `node --test tests/*.test.mjs` neste diretório. Estes testes cobrem validações puras e totalização, **não substituem testes de integração no Discord ou Cloudflare**.

## Engenharia de agentes
Veja `AGENTS.md` para responsabilidades, harness, skills e instruções de execução. **Agentes configurados não são agentes executados**: é obrigatório invocá-los no OpenCode e registrar evidências reais.

## Pendências para finalizar a entrega
- Executar `iniciar.mjs` oficial e preservar a estrutura esperada; **não sobrescrever arquivos do professor sem conferir**.
- Configurar D1 e OAuth2 com credenciais próprias.
- Publicar na Cloudflare, verificar funcionamento, atualizar URL.
- Executar os três agentes via OpenCode e registrar evidências.
- Rodar validador oficial e corrigir o que for apontado.
- PR e postagem das URLs no AVA.
