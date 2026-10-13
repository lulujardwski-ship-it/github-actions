# Avaliacao: GitHub Actions com IA

Responsavel: Luiz Henrique Weinert Jardwski  
RA: 2026107781

Site copiado integralmente de `lulujardwski-ship-it/avaliacao2`, commit
`529a59462aca7a8023ef06ad34a95ff1be0bd47a`. O codigo usa Cloudflare Pages
Functions, Google OpenID Connect, GitHub OAuth, PKCE S256 e sessoes D1.

## Automacao desta avaliacao

| Arquivo | Funcao |
| --- | --- |
| `.github/workflows/ci.yml` | Em todo push e PR para main, procura segredos, aplica SQL em SQLite local e valida HTML. |
| `.github/workflows/revisao-ia.yml` | Em PRs do proprio repositorio, solicita revisao ao Copilot e publica comentario. |
| `.github/workflows/migrar.yml` | Em push na main que altere migrations/, aplica SQL no D1 remoto, consulta PRAGMA e tenta gerar resumo por IA. |
| `migrations/0001_inicial.sql` | Esquema inicial: oauth_transactions, sessions e notas. |
| `wrangler.toml` | Configuracao do Pages e binding DB; UUID do novo D1 ainda pendente. |
| `AVALIACAO.md` | Evidencias reais e respostas para a entrega. |

A protecao da main e uma regra nas configuracoes do GitHub. O enunciado
descreve tres arquivos YAML, embora a introducao mencione quatro workflows.

Seguir [docs/CONFIGURACAO.md](docs/CONFIGURACAO.md) para configurar o novo
Pages, criar `github-actions-db`, preencher seu UUID e cadastrar os secrets.
Nao publicar o novo Pages com o UUID zerado de exemplo.

O site original e seu banco permanecem independentes desta copia. Os
arquivos de `public/entrega1/` sao evidencias historicas da avaliacao anterior
no dominio `avaliacao2-01z.pages.dev`; nao comprovam os testes desta avaliacao.
`schema.sql` e uma referencia historica: use `migrations/` no novo banco.

## Referencias

- https://github.com/actions/ai-inference
- https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions
- https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/troubleshooting-required-status-checks
- https://developers.cloudflare.com/d1/reference/migrations/

## Documentacao historica do projeto original (Avaliacao 2)

As orientacoes abaixo foram preservadas como referencia da avaliacao anterior.
Para esta avaliacao, prevalece docs/CONFIGURACAO.md, incluindo wrangler.toml.

Projeto para Cloudflare Pages com Google OpenID Connect, GitHub OAuth, PKCE S256 e sessões D1. Não contém credenciais. O conteúdo em `public/` é acessível por URL; somente as rotas de API consultam a sessão.

## Publicação

1. Crie um repositório GitHub com `main` e envie **o conteúdo desta pasta** para a raiz. `public/` e `functions/` devem ficar lado a lado.
2. No Cloudflare Pages, conecte o repositório via GitHub. Escolha framework `None`, build command vazio, output directory `public` e root directory vazio. Teste `/api/health` na URL de produção.
3. Crie o banco D1 e execute `schema.sql` no console. Adicione ao Pages um binding D1 chamado `DB` e faça uma nova implantação.
4. No Google Cloud, registre um cliente OAuth Web com callback `https://SEU-PROJETO.pages.dev/oauth/callback/google`. Configure a tela de consentimento em teste e inclua sua conta como usuária de teste.
5. No GitHub, registre uma OAuth App com Homepage URL `https://SEU-PROJETO.pages.dev` e callback `https://SEU-PROJETO.pages.dev/oauth/callback/github`. Deixe Device Flow desativado.
6. No Pages, defina `PUBLIC_BASE_URL` (URL de produção sem barra final), `GOOGLE_CLIENT_ID` e `GITHUB_CLIENT_ID` como variáveis; `GOOGLE_CLIENT_SECRET` e `GITHUB_CLIENT_SECRET` como **segredos criptografados**. Reimplante.
7. Teste ambos os logins e o logout na URL de produção, além dos seis casos de falha do PDF. Não inclua valores transitórios em logs ou evidências.

Na avaliacao anterior, nao eram usados package.json ou Wrangler. Nesta
avaliacao, os workflows usam Node/npm e o arquivo wrangler.toml. Nao coloque
credenciais no repositorio.

## Evidências

Depois dos testes reais, coloque **exatamente os oito arquivos pedidos** em `public/entrega1/` e confirme o acesso a cada URL publicada. São `01-pages-configuracao.pdf`, `02-google-retorno.txt`, `03-github-retorno.txt`, `04-d1-esquema.txt`, `05-inicio-login-google.pdf`, `06-inicio-login-github.pdf`, `07-testes-falha.md` e `08-aceitacao.md`.

Revise cada PDF e texto: a pasta será pública. Remova valores de cookies, códigos, tokens, `state`, `nonce`, `code_challenge`, segredos e identificadores privados da conta. Registre somente testes observados; não marque como executado um teste ainda pendente.

## Arquitetura

- `GET /oauth/login/{google|github}` cria transação temporária D1 e cookie `__Host-oauth-tx`.
- `GET /oauth/callback/{google|github}` consome a transação uma vez, valida a identidade e cria sessão opaca de oito horas.
- `GET /api/me` lê o resumo do cookie de sessão no D1 e devolve perfil mínimo.
- `POST /oauth/logout` exige `Origin` exata e exclui a sessão D1.

O fluxo Google verifica assinatura RS256 via descoberta OIDC e JWKS, além de emissor, audiência, validade e nonce. O fluxo GitHub consulta `/user` e exige revogação da autorização antes da sessão local.
