---
name: supplog-handoff
description: Prepara a entrega do projeto ao TI conforme os padrões Supplog: confere pendências, gera zip sem segredos e o GUIA-HANDOFF.md. Use ao entregar, submeter ou empacotar o projeto para produção.
---

# /supplog-handoff — Entrega do projeto ao TI (Padrão Supplog)

Você prepara a entrega de um projeto vibe-coded para o **crivo do TI** — a etapa
em que o dev solicita a avaliação para subir para produção. O usuário é um **vibe
coder não técnico**: ele precisa sair desta skill sabendo exatamente **o que
enviar, para onde enviar e o que vai acontecer depois**, em português simples.

> **Escopo desta versão (v1 — básico):** o entregável final é o **projeto zipado**
> mais um **guia prático de solicitação** (`GUIA-HANDOFF.md`). Evoluções futuras
> poderão automatizar o envio e integrar com o processo do TI.

A **fonte de verdade** é a referência embutida no final deste arquivo (Padrões de
Desenvolvimento Vibe Coding v1.2, derivada do documento canônico
`skills/supplog-iniciar/SKILL.md` do repositório de skills da
Supplog).

---

## Regras de conduta (invioláveis)

1. **Não altera o código do projeto.** Esta skill escreve apenas dois artefatos: o
   arquivo `.zip` do pacote e o `GUIA-HANDOFF.md`.
2. **Segredo nunca entra no pacote.** `.env` e qualquer valor real de
   configuração ficam **fora do zip** — isso é inegociável (seção 4.2). Isso
   inclui o `SSO_CLIENT_SECRET` do Login Único. Os valores reais são repassados
   ao TI separadamente, quando ele for aplicar a configuração (repasse previsto
   na seção 5.3; a recomendação de usar canal seguro — nunca e-mail aberto ou
   chat público — é desta skill).
3. **Sinaliza, não bloqueia.** Pendências (check não rodado, itens "Não conforme" no
   relatório, README incompleto) são mostradas com clareza e a recomendação é
   resolver antes de enviar — mas a decisão de enviar mesmo assim é do usuário.
   Aprovar/reprovar é do TI.
4. **Linguagem simples.** Nada de jargão sem explicação de uma frase.

---

## Processo

### Passo 1 — Pré-checagem (não bloqueante)

Verifique e **relate em linguagem simples**:

- **`RELATORIO-CHECK.md` existe?** Se não: recomende rodar a `/supplog-check`
  antes do handoff (é ela que aponta o que o TI vai olhar). Se existe: resuma o
  placar (Conforme / Não conforme / Parcial) e destaque os itens críticos ainda
  abertos.
- **`README.md` completo?** (seções obrigatórias da 2.7 — lista na referência
  embutida; sem TODOs pendentes; status coerente — para solicitar produção o
  esperado é **"Em teste"** (app em homologação); "Em desenvolvimento" entra como
  pendência a sinalizar).
- **Segredos:** `.env` está no `.gitignore` e fora do projeto versionado?
  `.env.example` existe com todas as chaves (inclusive `SSO_ISSUER`,
  `SSO_CLIENT_ID`, `SSO_CLIENT_SECRET`, `SSO_APP_URL` quando há login)?
- **Login Único (quando há login, seção 4.4/5.4):** o README traz a seção
  "Autenticação (Login Único)" com modo, quem pode entrar, `client_id` e
  Redirect URIs (sem secret)? Não há tela/tabela de senha própria? **Pergunte ao
  usuário** (não dá para ver no código): a aplicação está **Aprovada** em
  supplog.com → Minhas aplicações? As **Redirect URIs de produção**
  (`https://<endereço-de-produção>/entrar/callback` e `https://<endereço>/`)
  já estão cadastradas? Se o endereço de produção ainda não foi definido pelo
  TI, registre no guia que o cadastro das URIs de produção (que reenvia a
  aplicação para aprovação do administrador do SSO) precisa acontecer **antes**
  da subida, assim que o TI informar o endereço.
- **Seed e banco:** `database/scripts_criacao.sql` e seed presentes (o TI recria
  o banco com eles na avaliação).

Se houver pendência, faça **uma única pergunta**: resolver primeiro (recomendado)
ou prosseguir mesmo assim? Se prosseguir, as pendências entram registradas no
`GUIA-HANDOFF.md`.

### Passo 2 — Dados da solicitação

Puxe do `README.md` (e `PLANEJAMENTO.md`, se existir): nome da aplicação,
descrição em uma frase, responsável (área, contato), stack/porte, fontes do DW
consumidas e, quando há login, os dados do Login Único (`client_id`, modo, quem
pode entrar, Redirect URIs cadastradas — **nunca** o secret). **Pergunte apenas
o que faltar**, um item por vez.

**Canal de envio ao TI:** _a definir pela Supplog — quando o canal oficial
existir (e-mail, Teams, sistema de chamados), edite esta linha para fixá-lo._
Enquanto esta linha não for preenchida, **pergunte ao usuário** por qual canal a
área dele aciona o TI hoje e use esse canal no guia.

### Passo 3 — Gerar o ZIP

- **Nome do arquivo:** `<nome-da-aplicacao>-handoff-<AAAA-MM-DD>.zip`, criado na
  **pasta acima** da raiz do projeto (para não entrar em zips futuros).
- **Excluir sempre:** `.env` e variantes com valores reais (`.env.local`,
  `.env.producao`…— **exceto** `.env.example`, que vai junto), `.git/`,
  `node_modules/`, `__pycache__/`, `.venv/`, `*.pyc`, artefatos de build
  (`dist/`, `build/`), zips de handoff anteriores e `database/app.db` (o TI
  recria o banco com `scripts_criacao.sql` + seed — isso faz parte da avaliação).
- **Incluir todo o resto:** código, `README.md`, `.env.example`,
  `database/scripts_criacao.sql`, seed, `.gitignore`, testes — e
  `PLANEJAMENTO.md` e `RELATORIO-CHECK.md` quando existirem.
- **Depois de gerar, confira:** liste o conteúdo do zip e verifique que nenhum
  `.env`/segredo entrou. Se entrou, apague o zip, corrija a exclusão e gere de
  novo.

### Passo 4 — Escrever o `GUIA-HANDOFF.md`

Escreva na **raiz do projeto**, seguindo o template (se já existir um guia
anterior, sobrescreva):

```markdown
# Guia de Handoff — <Nome da Aplicação>

> Gerado por /supplog-handoff em <data>. Base: Padrões de Desenvolvimento Vibe
> Coding (v1.2). Este guia orienta a solicitação de avaliação ao TI — quem
> aprova ou reprova é o TI (SLA na seção "Depois do envio").

## O pacote

- **Arquivo:** `<nome-do-zip>` (em `<caminho onde foi criado>`)
- **Conteúdo:** projeto completo **sem segredos** (`.env` fica de fora; o
  `.env.example` vai junto), com `README.md`, scripts de criação do banco e
  seed — mais `PLANEJAMENTO.md` e `RELATORIO-CHECK.md`, se existirem
  (<liste apenas o que de fato entrou no zip>).

## Situação do projeto

- Relatório da /supplog-check: <Conforme n · Não conforme n · Parcial n — ou
  "não foi rodado">
- Pendências levadas junto (se houver): <lista ou "nenhuma">

## Login Único (SSO)

<se não há login: "Não se aplica — <motivo>". Se há:>

- **client_id:** `<client_id>` — modo <bloqueio total / botão>; quem pode entrar:
  <internos / externos / ambos>
- **Status no SSO (Minhas aplicações):** <Aprovada / Aguardando aprovação>
- **Redirect URIs de produção:** <cadastradas: `https://.../entrar/callback` e
  `https://.../` — ou "pendente: cadastrar assim que o TI informar o endereço de
  produção; o cadastro reenvia a aplicação para aprovação do administrador do
  SSO">
- O `SSO_CLIENT_SECRET` **não está no pacote** — segue com as demais variáveis
  reais, por canal seguro.

## Onde e como enviar

1. Envie o arquivo `<nome-do-zip>` para: **<canal do TI>**.
2. Use a mensagem modelo abaixo (copie, cole e ajuste se quiser):

> **Assunto:** Solicitação de avaliação para produção — <Nome da Aplicação>
>
> Olá, TI!
>
> Solicito a avaliação da aplicação **<Nome>** para subida em produção.
>
> - **O que faz:** <descrição em 1-2 frases>
> - **Responsável:** <nome/área/contato>
> - **Stack e porte:** <stack> (<classificação>)
> - **Fontes do DW consumidas:** <lista ou "nenhuma">
> - **Login Único (SSO):** client_id `<client_id>`, <aprovada / aguardando
>   aprovação>; Redirect URIs de produção <cadastradas / a cadastrar quando o
>   endereço for definido> _(omita esta linha se não há login)_
> - **Status atual:** Em teste (app em homologação), solicitando subida para
>   produção
> - **Pacote:** em anexo (projeto zipado, sem segredos; o banco se recria com
>   `database/scripts_criacao.sql` + seed — comando no README)
> - **Relatório de conformidade:** `RELATORIO-CHECK.md`, na raiz do pacote
>   _(inclua esta linha apenas se o relatório estiver no pacote)_
>
> Os valores reais das variáveis de ambiente **não estão no pacote** — repasso
> por canal seguro quando solicitado.
>
> Fico à disposição durante a avaliação.

## Variáveis de ambiente reais

Os valores reais **não vão no zip**. Deixe-os anotados em local seguro (nunca em
e-mail aberto ou chat público) para repassar ao TI quando ele for aplicar a
configuração de produção (seção 5.3 do padrão). Com Login Único, isso inclui
`SSO_CLIENT_SECRET` e o `SSO_APP_URL` de produção (que precisa bater com a
Redirect URI cadastrada). Se suspeitar que o secret vazou, gere um novo em
"Minhas aplicações" antes de repassar — o antigo para de funcionar na hora.

## Depois do envio — o que esperar

- O TI tem **SLA de 10 dias úteis** para avaliar desenvolvimento e segurança
  (seção 5.7).
- **Aprovado:** a aplicação sobe para produção — o banco definitivo (PostgreSQL)
  é criado somente após a aprovação (5.1), o seed não segue para produção (5.2),
  a manutenção passa a ser do TI (5.5) e o login em produção é pelo Login Único
  com a aplicação aprovada no SSO e as Redirect URIs de produção cadastradas
  (5.4).
- **Reprovado:** a aplicação sai da homologação; você corrige com o Claude e
  solicita nova homologação (5.7).
- **Atenção:** a homologação dura até 10 dias úteis; sem solicitação de produção
  nesse prazo, o TI contata o responsável e, sem resposta em 2 dias úteis, a
  aplicação é removida automaticamente (5.7).
- O TI pode recusar por **redundância** — se já existir solução equivalente na
  empresa (5.9) — e produção de app que consome DW **só no ambiente interno**
  (5.8).
```

### Passo 5 — Encerrar

No chat, em linguagem simples: confirme o que foi gerado (zip + guia), diga o
passo único que falta ("envie o zip pelo canal X com a mensagem modelo do guia")
e relembre: valores reais de env (inclusive o secret do SSO) ficam com você até
o TI pedir; se há login, as Redirect URIs de produção precisam estar cadastradas
e aprovadas no SSO antes da subida; e o prazo de resposta esperado é o SLA de
10 dias úteis.

---

## Padrões de Desenvolvimento Vibe Coding (v1.2) — referência embutida

_(Trechos relevantes ao handoff. Numeração idêntica à do documento canônico
`skills/supplog-iniciar/SKILL.md`.)_

### 2.7 README obrigatório

Descrição; Contexto/Motivação; Responsável (área, contato, data de criação);
Stack + versão exata + classificação de porte; Como rodar localmente (com comando
do seed); Autenticação (Login Único) — modo, quem pode entrar, `client_id`,
Redirect URIs por ambiente, variáveis `SSO_*`, **nunca o secret** (ou "Não se
aplica" com motivo); Fluxos principais e endpoints (método, rota, tabelas
lidas/escritas, request, response, erros); Estrutura de dados (tabelas e
finalidade); Fontes do DW consumidas; Dependências externas; Status (Em
desenvolvimento / Em teste / Aguardando aprovação / Em produção); Histórico de
alterações.

### 4.2 Variáveis de ambiente e segredos

- `.env` nunca commitado (consta no `.gitignore`) — e **nunca entra no pacote de
  entrega**. Inclui o `SSO_CLIENT_SECRET` do Login Único.
- `.env.example` obrigatório e versionado (valores fictícios/didáticos/vazios;
  com as chaves `SSO_ISSUER`, `SSO_CLIENT_ID`, `SSO_CLIENT_SECRET`, `SSO_APP_URL`
  quando há login) — **vai no pacote**.
- Nenhum segredo hardcoded; nenhum segredo real em README, comentário ou mensagem
  de commit; remover chaves geradas incorretamente por IA.
- Dependências sugeridas por IA devem ter existência, autoria e reputação
  confirmadas.

### 5. Ambiente (Homologação x Produção)

- **5.1 Banco:** homologação **SQLite**; produção **PostgreSQL** (criado somente
  após aprovação do projeto).
- **5.2 Seed:** existe e roda em homologação; em produção o script **não pode
  continuar existindo** no projeto.
- **5.3 Env:** homologação usa valores fictícios; na subida, o dev repassa os
  valores reais para o TI aplicar.
- **5.4 Autenticação:** toda aplicação com login usa o **Login Único** da
  Supporte (SSO, OpenID Connect em `https://supplog.com` — seção 4.4) desde a
  homologação; sem tela, tabela ou coluna de senha própria (login próprio só
  como exceção autorizada, 4.4.1). Para produção: aplicação **Aprovada** em
  supplog.com → Minhas aplicações, com as Redirect URIs de produção
  (`https://<app>/entrar/callback` e `https://<app>/`) cadastradas byte a byte —
  cadastrar URI nova reenvia a aplicação para aprovação, então faça isso antes
  de solicitar a subida. `SSO_APP_URL` e `SSO_CLIENT_SECRET` reais vão com as
  demais variáveis (5.3). App **externo:** também seção **4.7** (TLS 1.2+, rate
  limit, headers, CSRF; MFA é do SSO).
- **5.5 Manutenção:** homologação é do dev/área; produção passa a ser do TI.
- **5.6 Deploy:** homologação é solicitação simples (permanência de até 10 dias
  úteis); produção segue o SLA abaixo.
- **5.7 SLA:** o dev solicita a subida dentro dos 10 dias úteis de homologação; o
  TI tem 10 dias úteis para avaliar. Aprovado → sobe. Reprovado → sai da
  homologação, o dev corrige e solicita nova homologação. Sem solicitação no
  prazo, o TI contata o dev; sem resposta do dev ou gestor em 2 dias úteis, a
  aplicação é removida automaticamente.
- **5.8 Restrição:** proibido publicar app que consome dados do DW em produção
  externa/de terceiros — só o ambiente de produção interno.
- **5.9 Redundância:** o TI pode recusar em qualquer etapa se já existir solução
  equivalente; decisão registrada e final.
