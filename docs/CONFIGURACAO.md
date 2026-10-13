# Preparacao da avaliacao GitHub Actions

Este repositorio e uma copia de `lulujardwski-ship-it/avaliacao2`, commit
`529a59462aca7a8023ef06ad34a95ff1be0bd47a`. Os arquivos em `public/entrega1/`
registram a avaliacao anterior no site `https://avaliacao2-01z.pages.dev/`.
Eles nao comprovam a publicacao nem os testes deste novo repositorio.

## Cloudflare e OAuth

1. Criar um novo D1 chamado `github-actions-db`, para manter a avaliacao
   anterior independente. Copiar o UUID publico do banco para `database_id`
   em `wrangler.toml`, substituindo o valor composto por zeros.
2. Aplicar `migrations/0001_inicial.sql` ao novo D1. Nao aplicar `schema.sql`
   antes: ele e a referencia historica do projeto original.
3. Criar um Pages conectado a `lulujardwski-ship-it/github-actions`, com
   branch `main`, framework `None`, build vazio e diretorio de saida `public`.
4. Conferir o binding D1 `DB`, apontando para `github-actions-db`.
5. Configurar `PUBLIC_BASE_URL` com a nova URL HTTPS sem barra final e
   `GOOGLE_CLIENT_ID`/`GITHUB_CLIENT_ID` como variaveis. Os Client Secrets
   ficam somente em segredos criptografados do Pages.
6. Cadastrar no cliente Google a nova URL de retorno
   `https://NOVA-URL.pages.dev/oauth/callback/google`. Para GitHub, criar ou
   configurar uma OAuth App para a nova URL, preservando a app do site anterior.
7. Reimplantar e verificar `/api/health`, login, `/api/me` e logout.

O UUID zerado nao representa um banco real. O workflow remoto recusa esse
valor; a configuracao deve ser concluida antes de publicar o novo Pages.

## Secrets do GitHub Actions

Em Settings > Secrets and variables > Actions, cadastrar:

- `COPILOT_PAT`: fine-grained PAT com `Copilot Requests`, validade de 7 dias
  e conta com Copilot ativo, conforme o enunciado.
- `CLOUDFLARE_API_TOKEN`: token com D1 Edit restrito a conta usada.
- `CLOUDFLARE_ACCOUNT_ID`: ID da conta Cloudflare.

Inserir valores somente nas telas oficiais de secrets. Nao coloca-los em
arquivos, comentarios, mensagens ou logs.

## Protecao da main

Depois da primeira execucao do CI, configurar uma regra ativa para `main`
que exija pull request e o check `verificar`. Evitar bypass para a conta
usada nos testes, inclusive administradores, para comprovar o push recusado.
Nao exigir uma segunda aprovacao humana se a dupla nao tiver outro revisor
disponivel: o enunciado exige o PR e o check, nao uma quantidade de reviews.

## Sequencia de evidencias reais

1. CI aprovado; adicionar erro de sintaxe SQL em uma branch e aguardar CI
   reprovado. Registrar run e linha do erro antes de corrigir.
2. Com protecao ativa, observar o PR bloqueado; corrigir na mesma branch,
   observar liberacao e registrar uma tentativa real de push direto recusada.
3. Criar PR com Function usando SQL concatenado; registrar comentario real
   da IA; corrigir com `prepare(...).bind(...)` e registrar nova revisao.
   Retirar temporariamente `pull-requests: write`, observar erro ao comentar
   e restaurar. Nao fazer merge da versao vulneravel.
4. Adicionar `0002_adiciona_coluna.sql` por PR, passar pelo CI e pela IA,
   fazer merge e guardar o run de `migrar` e o PRAGMA do banco remoto.
5. Fazer outro merge sem alterar `migrations/`; relacionar o SHA com a lista
   de runs que nao inclui `migrar`.
6. Completar `AVALIACAO.md` com links e quatro respostas e entregar por PR.

Nao registrar como aprovado ou concluido um teste ainda nao observado.
