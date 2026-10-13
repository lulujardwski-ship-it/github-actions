# Avaliacao GitHub Actions com IA

Responsavel: Luiz Henrique Weinert Jardwski  
RA: 2026107781  
Segundo integrante da dupla: ainda nao informado.  
Repositorio: https://github.com/lulujardwski-ship-it/github-actions

**Estado: em preparacao.** Este arquivo nao representa uma entrega concluida.
Somente execucoes e resultados realmente observados serao marcados completos.

## Tarefa 1 - CI

- Workflow: `.github/workflows/ci.yml`.
- Execucao verde: pendente.
- Execucao vermelha proposital: pendente.
- Linha do erro SQL: pendente.
- Correcao na mesma branch: pendente.

As migracoes sao testadas em SQLite porque o D1 usa seu mecanismo e suas
semanticas SQL; isso permite detectar erros de sintaxe antes de alterar o
banco remoto, embora nao substitua toda a validacao especifica do D1.

## Tarefa 2 - Main protegida

- Regra ativa exigindo PR e check `verificar`: pendente de acesso administrativo.
- PR bloqueado e depois liberado: pendente.
- Saida de um `git push` direto recusado: pendente.

## Tarefa 3 - Revisao por IA

- Workflow: `.github/workflows/revisao-ia.yml`.
- PR com SQL concatenado e comentario real da IA: pendente.
- Correcao com `prepare(...).bind(...)` e nova revisao: pendente.
- Run e log de erro ao retirar `pull-requests: write`: pendente.
- Restauracao da permissao: pendente.
- Afirmacao incorreta ou irrelevante da IA, com justificativa: pendente de uma
  resposta real; nao foi inventada nem simulada.

## Tarefa 4 - D1 depois do merge

- Workflow: `.github/workflows/migrar.yml`.
- PR com `0002_adiciona_coluna.sql`: pendente.
- Run do merge com migracao remota concluida: pendente.
- Saida de `PRAGMA table_info(notas)` confirmando a coluna nova: pendente.
- Merge sem alteracao em migrations/ e ausencia do workflow migrar: pendente.
- Bonus: resumo real por IA em `$GITHUB_STEP_SUMMARY`: pendente.

## Perguntas

### 1. Por que o CI roda duas vezes e qual resultado e considerado?

Um push em uma branch com PR aberto dispara os eventos push e pull_request,
pois ambos estao declarados no ci.yml. O push verifica o commit da branch,
enquanto o pull_request verifica por padrao o merge de teste com a main.
Se esse merge de teste tiver checks, seu resultado deve passar; caso
contrario, o GitHub considera os checks do ultimo commit da branch do PR.

### 2. Por que um PR de fork nao recebe COPILOT_PAT?

Codigo vindo de um fork pode ser controlado por alguem sem permissao no
repositorio de destino, entao seus workflows de pull_request nao recebem
os secrets desse repositorio. Isso impede que um atacante altere o workflow
para copiar o PAT ou outras credenciais para um servidor externo.

### 3. Por que declarar permissions?

Declarar contents: read e pull-requests: write limita o GITHUB_TOKEN ao que
a revisao precisa: ler o codigo e publicar um comentario. As permissoes
padrao podem variar conforme a conta ou organizacao; declarar o minimo
necessario torna o comportamento explicito e reduz o alcance do token.

### 4. A IA pode substituir o status check?

Nao, porque a revisao por IA pode produzir apontamentos incorretos e um
comentario nao comprova que HTML e migracoes passaram nos testes. O check
verificar aplica verificacoes reproduziveis e bloqueia o merge quando falha.
A justificativa com a afirmacao realmente observada na Tarefa 3 ainda
precisa ser acrescentada depois desse teste.

## Origem e limite das evidencias

O site foi copiado de avaliacao2, commit
`529a59462aca7a8023ef06ad34a95ff1be0bd47a`. As evidencias antigas de
public/entrega1/ pertencem ao dominio `avaliacao2-01z.pages.dev` e nao sao
usadas como prova de execucoes desta avaliacao GitHub Actions.
