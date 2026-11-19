---
name: supplog-handoff
description: "Prepara a entrega ao TI conforme os padrões Supplog: confere pendências, gera zip sem segredos e o GUIA-HANDOFF.md com o chamado no Fluig. Use ao entregar, submeter ou empacotar para produção."
---

# /supplog-handoff — Entrega do projeto ao TI (Padrão Supplog)

Você prepara a entrega de um projeto alinhado ao padrão Vibe Coding para o
**crivo do TI** — a etapa em que o solicitante abre o **chamado de avaliação
no Fluig** e pede a promoção à produção. O **solicitante** conhece o negócio,
mas **não precisa dominar engenharia de software**: ele precisa sair desta
skill sabendo exatamente **o que anexar, como preencher o chamado e o que vai
acontecer depois**, em português claro.

> **Escopo:** o entregável final é o **projeto zipado** mais um **guia prático
> de solicitação** (`GUIA-HANDOFF.md`) com o texto do chamado pronto para
> colar. A skill prepara o pacote; a abertura do chamado é do solicitante.

A **fonte de verdade** é a referência embutida no final deste arquivo (Padrões de
Desenvolvimento Vibe Coding v1.4, derivada do documento canônico
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
3. **Sinaliza, não bloqueia.** Pendências (check ou autoanálise de segurança
   não rodados, itens "Não conforme" nos relatórios, README incompleto) são
   mostradas com clareza e a recomendação é resolver antes de enviar — mas a
   decisão de enviar mesmo assim é do solicitante. Aprovar/reprovar é do TI.
   **Exceção: o subdomínio.** Sem ele o chamado não segue para o deploy
   (seção 5.6), então o guia só é escrito com o subdomínio definido.
4. **Linguagem acessível.** Evite jargão sem explicação em uma frase.

---

## Processo

### Passo 1 — Pré-checagem (não bloqueante)

Verifique e **relate em linguagem acessível**:

- **`RELATORIO-CHECK.md` existe?** Se não: recomende rodar a `/supplog-check`
  antes do handoff (é ela que aponta o que a Sustentação vai olhar). Se
  existe: resuma o resultado (Conforme / Não conforme / Parcial) e destaque
  os itens críticos ainda abertos.
- **`RELATORIO-SEGURANCA.md` existe?** Se não: recomende rodar a
  `/supplog-seguranca` — é a autoanálise de segurança que a PO-SI-0016 exige
  do responsável antes de submeter o código (seção 4.8), e é o que a
  Segurança da Informação vai conferir. Se existe: resuma por severidade e
  destaque qualquer item **Crítico** ou **Alto** em aberto — com um desses, a
  política veda o deploy sem aceite de risco formal.
- **`README.md` completo?** (seções obrigatórias da 2.7 — lista na referência
  embutida; sem TODOs pendentes; status coerente — para solicitar produção o
  esperado é **"Em teste"** (app em homologação); "Em desenvolvimento" entra como
  pendência a sinalizar). Inclui o item **"Endereço em produção"** com o
  subdomínio.
- **Segredos:** `.env` está no `.gitignore` e fora do projeto versionado?
  `.env.example` existe com todas as chaves (inclusive `SSO_ISSUER`,
  `SSO_CLIENT_ID`, `SSO_CLIENT_SECRET`, `SSO_APP_URL` quando há login)?
- **Login Único (quando há login, seção 4.4/5.4):** o README traz a seção
  "Autenticação (Login Único)" com modo, quem pode entrar, `client_id` e
  Redirect URIs (sem secret)? Não há tela/tabela de senha própria? **Pergunte ao
  usuário** (não dá para ver no código): a aplicação está **Aprovada** em
  supplog.com → Minhas aplicações? As **Redirect URIs de produção** no
  subdomínio (`https://<subdominio>.supplog.com/entrar/callback` e
  `https://<subdominio>.supplog.com/`) já estão cadastradas? Se não, registre
  no guia que o cadastro (que reenvia a aplicação para aprovação do
  administrador do SSO) precisa acontecer **antes** da subida.
- **Seed e banco:** `database/scripts_criacao.sql` e seed presentes (o TI recria
  o banco com eles na avaliação).
- **Repositório (seção 2.9):** o código está commitado e enviado ao repositório
  do GitHub da Supporte (`git remote -v`, `git status --short`), e o README
  traz o endereço? Autores dos commits com e-mail `@supplog.com`
  (`git log --format=%ae | sort -u`)? Sem repositório ainda: pendência — o
  solicitante abre o chamado no Fluig (Sustentação → Inovação → GitHub →
  Criação, projeto de vibe coding com o Claude, repositório na organização
  Supporte Logistica - Vibe Coders, com o nome de usuário do GitHub) e o guia
  registra "chamado aberto em <data>".
- **Governança (seção 4.8) — pergunte, não dá para ver no código:** o Termo
  de Ciência e Responsabilidade (FO-SI-0025) está assinado? A aplicação rodou
  só na máquina local e nos ambientes do TI (nada em Vercel, Netlify, Render,
  Supabase, GitHub Pages ou conta pessoal)? Resposta negativa entra como
  pendência no guia.
- **Dados pessoais (seção 4.5):** se o `PLANEJAMENTO.md`/README registram dado
  pessoal, confira que a classificação (pessoal / sensível / confidencial), a
  necessidade de DPO/ROPA/RIPD e a retenção estão documentadas — o chamado
  leva essa informação, e ela pode estender o prazo de avaliação.

Se houver pendência, faça **uma única pergunta**: resolver primeiro (recomendado)
ou prosseguir mesmo assim? Se prosseguir, as pendências entram registradas no
`GUIA-HANDOFF.md`.

### Passo 2 — Dados da solicitação

Puxe do `README.md` (e `PLANEJAMENTO.md`, se existir): nome da aplicação,
descrição em uma frase, responsável (área, contato), stack/porte, fontes do DW
consumidas, classificação dos dados e, quando há login, os dados do Login
Único (`client_id`, modo, quem pode entrar, Redirect URIs cadastradas —
**nunca** o secret). **Pergunte apenas o que faltar**, um item por vez.

**Subdomínio (obrigatório — seção 5.6):** leia o item "Endereço em produção"
do README/PLANEJAMENTO. Se estiver vazio, pergunte ao solicitante qual o
subdomínio desejado, explicando em uma frase: é a parte antes de
`supplog.com` no endereço pelo qual as pessoas vão abrir a aplicação, e o
chamado sem ele não segue para o deploy. Sugira `<client_id>.supplog.com`
quando há SSO (o cadastro e o endereço publicado precisam coincidir) ou o
nome da aplicação em kebab-case. Valide o formato: minúsculas, sem acento,
sem espaço, hífen entre palavras. Automação sem tela nem API: "Não se aplica".
Se o subdomínio ainda não existe no README, oriente o solicitante a pedir ao
Claude para registrá-lo lá (esta skill não altera o projeto).

**Canal de envio ao TI:** chamado no **Fluig**, campos de classificação com os
valores da tabela abaixo, `.zip` anexado. É um chamado distinto do de criação
do repositório.

| Campo    | Valor                     |
| -------- | ------------------------- |
| Área     | SUSTENTAÇÃO               |
| Subárea  | INOVAÇÃO                  |
| Sistema  | DESENVOLVIMENTO IA        |
| Serviços | AVALIAÇÃO DESENVOLVIMENTO |

### Passo 3 — Gerar o ZIP

- **Nome do arquivo:** `<nome-da-aplicacao>-handoff-<AAAA-MM-DD>.zip`, criado na
  **pasta acima** da raiz do projeto (para não entrar em zips futuros).
- **Excluir sempre:** `.env` e variantes com valores reais (`.env.local`,
  `.env.producao`…— **exceto** `.env.example`, que vai junto), `.git/`,
  `node_modules/`, `__pycache__/`, `.venv/`, `*.pyc`, artefatos de build
  (`dist/`, `build/`), zips de handoff anteriores, certificados (`.pfx`,
  `.pem`, `.crt`, `.enc`) e `database/app.db` (o TI recria o banco com
  `scripts_criacao.sql` + seed — isso faz parte da avaliação).
- **Incluir todo o resto:** código, `README.md`, `.env.example`,
  `database/scripts_criacao.sql`, seed, `.gitignore`, testes — e
  `PLANEJAMENTO.md`, `RELATORIO-CHECK.md` e `RELATORIO-SEGURANCA.md` quando
  existirem.
- **Depois de gerar, confira:** liste o conteúdo do zip e verifique que nenhum
  `.env`/segredo entrou. Se entrou, apague o zip, corrija a exclusão e gere de
  novo.

### Passo 4 — Escrever o `GUIA-HANDOFF.md`

Escreva na **raiz do projeto**, seguindo o template (se já existir um guia
anterior, sobrescreva):

```markdown
# Guia de Handoff — <Nome da Aplicação>

> Gerado por /supplog-handoff em <data>. Base: Padrões de Desenvolvimento Vibe
> Coding (v1.4). Este guia orienta o chamado de avaliação ao TI — quem
> aprova ou reprova é o TI (SLA na seção "Depois do chamado").

## O pacote

- **Arquivo:** `<nome-do-zip>` (em `<caminho onde foi criado>`)
- **Conteúdo:** projeto completo **sem segredos** (`.env` fica de fora; o
  `.env.example` vai junto), com `README.md`, scripts de criação do banco e
  seed — mais `PLANEJAMENTO.md`, `RELATORIO-CHECK.md` e
  `RELATORIO-SEGURANCA.md`, se existirem (<liste apenas o que de fato entrou
  no zip>).

## Situação do projeto

- Relatório da /supplog-check: <Conforme n · Não conforme n · Parcial n — ou
  "não foi rodado">
- Relatório da /supplog-seguranca: <Crítico n · Alto n · Médio n · Baixo n —
  ou "não foi rodado">
- Repositório: <endereço no GitHub (organização Supporte Logistica - Vibe
  Coders), tudo enviado — ou "chamado aberto em <data>, aguardando criação">
- Subdomínio desejado: `<nome-da-aplicacao>.supplog.com` <ou "Não se aplica
  — automação sem tela">
- Termo de Ciência e Responsabilidade (FO-SI-0025): <assinado / pendente>
- Dados pessoais: <classificação; DPO/ROPA: sim/não; RIPD: indicado/não —
  ou "não trata dado pessoal">
- Pendências levadas junto (se houver): <lista ou "nenhuma">

## Login Único (SSO)

<se não há login: "Não se aplica — <motivo>". Se há:>

- **client_id:** `<client_id>` — modo <bloqueio total / botão>; quem pode entrar:
  <internos / externos / ambos>
- **Status no SSO (Minhas aplicações):** <Aprovada / Aguardando aprovação>
- **Redirect URIs de produção:** <cadastradas:
  `https://<subdominio>.supplog.com/entrar/callback` e
  `https://<subdominio>.supplog.com/` — ou "pendente: cadastrar antes da
  subida; o cadastro reenvia a aplicação para aprovação do administrador do
  SSO">
- O `SSO_CLIENT_SECRET` **não está no pacote** — segue com as demais variáveis
  reais, por canal seguro.

## Onde e como enviar

1. Abra um chamado no **Fluig** com estes campos de classificação — são eles
   que direcionam o chamado para a avaliação correta:

   | Campo    | Valor                     |
   | -------- | ------------------------- |
   | Área     | SUSTENTAÇÃO               |
   | Subárea  | INOVAÇÃO                  |
   | Sistema  | DESENVOLVIMENTO IA        |
   | Serviços | AVALIAÇÃO DESENVOLVIMENTO |

2. Anexe o arquivo `<nome-do-zip>`.
3. No texto do chamado, cole o modelo abaixo (ajuste se quiser; o
   **subdomínio** é obrigatório):

> Solicito a avaliação da aplicação **<Nome>** para homologação e subida em
> produção. Projeto de **vibe coding, construído com o Claude**, conforme os
> Padrões de Desenvolvimento Vibe Coding.
>
> - **O que faz:** <descrição em 1-2 frases>
> - **Responsável:** <nome / área / e-mail corporativo>
> - **Subdomínio desejado:** `<nome-da-aplicacao>.supplog.com`
> - **Stack e porte:** <stack> (<classificação>)
> - **Repositório:** <endereço no GitHub — ou "chamado de criação aberto em
>   <data>">
> - **Fontes do DW consumidas:** <lista ou "nenhuma">
> - **Dados tratados:** <classificação — pessoal / sensível / confidencial /
>   nenhum>; <"trata dado pessoal: solicito avaliação do DPO/ROPA" / "não
>   trata dado pessoal">; <"RIPD indicado pelos critérios X e Y" — omita se
>   não>
> - **Login Único (SSO):** client_id `<client_id>`, <aprovada / aguardando
>   aprovação>; Redirect URIs de produção <cadastradas / a cadastrar antes da
>   subida> _(omita esta linha se não há login)_
> - **Status atual:** Em teste (app em homologação), solicitando subida para
>   produção
> - **Pacote:** em anexo (projeto zipado, sem segredos; o banco se recria com
>   `database/scripts_criacao.sql` + seed — comando no README)
> - **Relatórios:** `RELATORIO-CHECK.md` e `RELATORIO-SEGURANCA.md`, na raiz do
>   pacote _(cite apenas os que estão no pacote)_
> - **Termo de Ciência e Responsabilidade (FO-SI-0025):** assinado
>
> Os valores reais das variáveis de ambiente **não estão no pacote** — repasso
> por canal seguro quando solicitado.
>
> Fico à disposição durante a avaliação.

## Variáveis de ambiente reais

Os valores reais **não vão no zip**. Deixe-os anotados em local seguro (nunca em
e-mail aberto ou chat público) para repassar ao TI quando ele for aplicar a
configuração de produção (seção 5.3 do padrão). Com Login Único, isso inclui
`SSO_CLIENT_SECRET` e o `SSO_APP_URL` de produção
(`https://<subdominio>.supplog.com`, que precisa bater com a Redirect URI
cadastrada). Se suspeitar que o secret vazou, gere um novo em "Minhas
aplicações" antes de repassar — o antigo para de funcionar na hora.

## Depois do chamado — o que esperar

- A avaliação corre em **duas frentes**, cada uma com parecer próprio:
  **Sustentação** (desenvolvimento e sustentabilidade) e depois **Segurança
  da Informação** (requisitos da PO-SI-0016 e varredura de vulnerabilidades).
  Aplicação com dado sensível pode ainda ir ao **Jurídico**/DPO. O TI tem
  **SLA de 10 dias úteis** (seção 5.7); o prazo pode se estender conforme a
  complexidade ou a análise de dados pessoais.
- **Não há um segundo chamado:** a solicitação de subida para produção e as
  respostas às pendências acontecem como **acompanhamento deste mesmo
  chamado**. Acompanhe-o — é o acompanhamento que mantém a aplicação em
  homologação.
- **Aprovado:** a aplicação sobe para produção no subdomínio informado — o
  banco definitivo (PostgreSQL) é criado somente após a aprovação (5.1), o
  seed não segue para produção (5.2), a manutenção passa a ser do TI (5.5) e o
  login em produção é pelo Login Único com a aplicação aprovada no SSO e as
  Redirect URIs de produção cadastradas (5.4).
- **Reprovado:** a aplicação sai da homologação; você corrige com o Claude e
  solicita nova homologação (5.7). Vulnerabilidade Alta ou Crítica em aberto
  impede o deploy (4.8).
- **Atenção:** a homologação dura até 10 dias úteis; sem solicitação de produção
  nesse prazo, o TI contata o responsável e, sem resposta em 2 dias úteis, a
  aplicação é removida automaticamente (5.7).
- O TI pode recusar por **redundância** — se já existir solução equivalente na
  empresa (5.9) — e produção de app que consome DW **só no ambiente interno**
  (5.8).

## Depois da produção

Publicada, toda alteração começa por chamado no Fluig com os mesmos Área,
Subárea e Sistema acima, mudando só o campo Serviços (5.10): **Manutenção**
para bug (o TI implementa a partir do comportamento esperado que você
descreve) e **Melhoria** para evolução (você desenvolve em branch e pull
request, e pelo chamado pede à Sustentação o merge e o deploy). Não cabe mais
corrigir localmente e enviar por fora.
```

### Passo 5 — Encerrar

No chat, em linguagem acessível: confirme o que foi gerado (zip + guia), diga o
passo único que falta ("abra o chamado no Fluig com os quatro campos do guia,
anexe o zip e cole o texto — confira que o subdomínio está lá") e relembre:
valores reais de env (inclusive o secret do SSO) ficam com o solicitante até o
TI pedir; se há login, as Redirect URIs de produção no subdomínio precisam
estar cadastradas e aprovadas no SSO antes da subida; a subida é pedida no
mesmo chamado; e o prazo de resposta esperado é o SLA de 10 dias úteis.

---

## Padrões de Desenvolvimento Vibe Coding (v1.4) — referência embutida

_(Trechos relevantes ao handoff. Numeração idêntica à do documento canônico
`skills/supplog-iniciar/SKILL.md`.)_

### 2.7 README obrigatório

Descrição; Contexto/Motivação; Responsável (área, contato, data de criação);
Repositório (endereço no GitHub ou "chamado aberto em <data>", 2.9); Endereço
em produção (subdomínio desejado `<nome-da-aplicacao>.supplog.com`, 5.6; ou
"Não se aplica"); Stack + versão exata + classificação de porte; Como rodar
localmente (com comando do seed); Autenticação (Login Único) — modo, quem pode
entrar, `client_id`, Redirect URIs por ambiente, variáveis `SSO_*`, **nunca o
secret** (ou "Não se aplica" com motivo); Fluxos principais e endpoints
(método, rota, tabelas lidas/escritas, request, response, erros); Estrutura de
dados (tabelas e finalidade); Fontes do DW consumidas; Dependências externas;
Status (Em desenvolvimento / Em teste / Aguardando aprovação / Em produção);
Histórico de alterações.

### 2.9 Versionamento e repositório (Git / GitHub)

Toda aplicação tem repositório próprio na organização do GitHub **Supporte
Logistica - Vibe Coders**, criado pelo TI via chamado no Fluig (Sustentação →
Inovação → GitHub → Criação; informar projeto de vibe coding construído com o
Claude, responsável com e-mail corporativo e **nome de usuário do GitHub**,
cuja conta tem o e-mail `@supplog.com` verificado). Commits com e-mail
`@supplog.com`; `.env` e `app.db` fora do histórico; código enviado ao
repositório antes do handoff; README com o endereço. O repositório oficial é
o único lugar do código.

### 4.2 Variáveis de ambiente e segredos

- `.env` nunca commitado (consta no `.gitignore`) — e **nunca entra no pacote de
  entrega**. Inclui o `SSO_CLIENT_SECRET` do Login Único.
- `.env.example` obrigatório e versionado (valores fictícios/didáticos/vazios;
  com as chaves `SSO_ISSUER`, `SSO_CLIENT_ID`, `SSO_CLIENT_SECRET`, `SSO_APP_URL`
  quando há login) — **vai no pacote**.
- Nenhum segredo hardcoded; nenhum segredo real em README, comentário ou mensagem
  de commit; remover chaves geradas incorretamente por IA.
- Dependências sugeridas por IA com existência, autoria e reputação
  confirmadas; verificadas contra CVEs; sem links externos, badges ou
  rastreadores no código e na documentação.

### 4.5 Classificação e proteção de dados

Dado tratado classificado como pessoal, pessoal sensível ou confidencial não
pessoal. Dado pessoal → avaliação do DPO e registro no ROPA (acontece no
chamado de avaliação). RIPD quando dois ou mais critérios: dado sensível ou
de criança/adolescente; 1.000 titulares ou mais em 12 meses ou uso recorrente
em produção; decisão automatizada, perfilamento ou monitoramento contínuo;
compartilhamento com terceiro externo. Retenção e descarte documentados
(4.6, item 9).

### 4.8 Governança do desenvolvimento seguro

Termo de Ciência e Responsabilidade (FO-SI-0025) assinado antes do
desenvolvimento e da submissão; ferramenta de IA homologada com conta
corporativa; aplicação rodando só na máquina local e nos ambientes do TI
(nada em Vercel, Netlify, Render, Supabase, GitHub Pages ou conta pessoal);
código só no repositório oficial; autoanálise de segurança
(`/supplog-seguranca`, `RELATORIO-SEGURANCA.md` no pacote); deploy vedado com
vulnerabilidade Alta/Crítica sem aceite de risco formal; autor sem acesso à
produção.

### 5. Ambiente (Homologação x Produção)

- **5.1 Banco:** homologação **SQLite**; produção **PostgreSQL** (criado somente
  após aprovação do projeto).
- **5.2 Seed:** existe e roda em homologação; em produção o script **não pode
  continuar existindo** no projeto.
- **5.3 Env:** homologação usa valores fictícios; na subida, o solicitante
  repassa os valores reais para o TI aplicar.
- **5.4 Autenticação:** toda aplicação com login usa o **Login Único** da
  Supporte (SSO, OpenID Connect em `https://supplog.com` — seção 4.4) desde a
  homologação; sem tela, tabela ou coluna de senha própria (login próprio só
  como exceção autorizada, 4.4.1). Para produção: aplicação **Aprovada** em
  supplog.com → Minhas aplicações, com as Redirect URIs de produção no
  subdomínio (`https://<subdominio>.supplog.com/entrar/callback` e
  `https://<subdominio>.supplog.com/`) cadastradas byte a byte — cadastrar URI
  nova reenvia a aplicação para aprovação, então faça isso antes de solicitar
  a subida. `SSO_APP_URL` e `SSO_CLIENT_SECRET` reais vão com as demais
  variáveis (5.3). App **externo:** também seção **4.7** (TLS 1.2+, rate limit,
  headers, CSRF; MFA é do SSO).
- **5.5 Manutenção:** homologação é do solicitante/área; produção passa a ser do
  TI.
- **5.6 Chamado de avaliação e subdomínio:** entrega por chamado no Fluig —
  Área SUSTENTAÇÃO, Subárea INOVAÇÃO, Sistema DESENVOLVIMENTO IA, Serviços
  AVALIAÇÃO DESENVOLVIMENTO — com o `.zip` anexado. O texto informa o
  **subdomínio desejado** (`nome-da-aplicacao.supplog.com`: minúsculas, sem
  acento, hífen; igual ao `client_id`); chamado sem subdomínio não segue para
  o deploy. Homologação: permanência de até 10 dias úteis.
- **5.7 SLA:** o solicitante pede a subida dentro dos 10 dias úteis de
  homologação, como acompanhamento do mesmo chamado; o TI tem 10 dias úteis
  para avaliar em duas frentes (Sustentação, depois Segurança da Informação;
  Jurídico/DPO quando há dado sensível). Aprovado → sobe. Reprovado → sai da
  homologação, o solicitante corrige e solicita nova homologação. Sem
  solicitação no prazo, o TI contata o solicitante; sem resposta do
  solicitante ou gestor em 2 dias úteis, a aplicação é removida
  automaticamente.
- **5.8 Restrição:** proibido publicar app que consome dados do DW em produção
  externa/de terceiros — só o ambiente de produção interno.
- **5.9 Redundância:** o TI pode recusar em qualquer etapa se já existir solução
  equivalente; decisão registrada e final.
- **5.10 Depois da produção:** alteração só por chamado no Fluig — Serviços
  **Manutenção** (bug; o TI implementa) ou **Melhoria** (evolução; o
  responsável faz branch + pull request e pede merge e deploy pelo chamado).
