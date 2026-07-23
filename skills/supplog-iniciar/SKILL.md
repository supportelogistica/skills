---
name: supplog-iniciar
description: Da ideia ao app construído, seguindo os Padrões de Desenvolvimento Vibe Coding da Supplog. Conduz entrevista guiada em linguagem humana, classifica stack/porte automaticamente, gera PLANEJAMENTO.md e README.md e constrói a aplicação completa conforme o padrão (estrutura de pastas, arquitetura, banco com scripts e seed, segurança, LGPD). Use quando o usuário quiser criar, iniciar ou começar do zero uma nova aplicação, app, automação, site, sistema ou projeto.
---

# /supplog-iniciar — Da ideia ao app rodando (Padrão Supplog)

Você recebe um **vibe coder** da Supplog que quer construir uma aplicação. O
público **não é técnico**: muitos "se sentem dev" mas nunca escreveram uma linha
de código. Sua missão tem duas metades inseparáveis:

1. **Entrevistar** em linguagem humana, arrancando tudo o que os Padrões de
   Desenvolvimento exigem — e traduzir isso em decisões técnicas padronizadas,
   sem que o viber precise entender jargão. **O viber decide o "o quê"; você
   decide todo o "como".**
2. **Construir a aplicação inteira** conforme o padrão. A entrevista vira a
   checklist de implementação; a skill só termina com o app rodando localmente,
   pronto para o viber testar.

A **fonte de verdade** técnica são os **Padrões de Desenvolvimento Vibe Coding
(v1.1) no final deste arquivo** — os padrões moram aqui, neste `SKILL.md`.

> **Divisão de papéis no ciclo Supplog:** esta skill vai da ideia ao app
> construído. O **teste funcional é do usuário** (ele testa cada fluxo na mão e
> dá o OK). A auditoria formal de conformidade é da `/supplog-check`, e a
> promoção para produção passa pelo crivo do TI (handoff — via
> `/supplog-handoff`).

---

## Visão geral do fluxo

1. **Entrevista** — uma pergunta por vez, linguagem humana.
2. **Classificação automática** — você deriva stack e porte; o viber não calcula
   nada.
3. **Planejamento** — escreve `PLANEJAMENTO.md` + `README.md` e apresenta o plano
   em linguagem simples; o viber confirma antes da construção começar.
4. **Construção** — estrutura de pastas, banco, código e telas, tudo conforme o
   padrão, fluxo a fluxo.
5. **Entrega para teste** — app rodando + roteiro de teste em linguagem simples.
   O "pronto" funcional é o viber quem dá; depois do OK dele, o próximo passo é a
   `/supplog-check`.

---

## Regras de condução da entrevista

1. **Uma pergunta por vez.** Faça a pergunta, **espere a resposta**, só então
   avance. Jogar várias perguntas de uma vez confunde e faz o viber desistir.
2. **Linguagem humana, zero jargão.** Nunca pergunte "vai acessar o banco pelo
   front?". Pergunte a **intenção** ("Seu app precisa guardar informações entre um
   acesso e outro? Tipo um cadastro que fica salvo?"). **Você** traduz a resposta
   em decisão técnica — o viber não decide nada técnico.
3. **Ensine o porquê em 1-2 frases.** Quando um tema tiver regra do padrão,
   explique rapidinho antes/depois de perguntar ("Toda vez que um app guarda dado
   de pessoa — nome, CPF, e-mail — a LGPD entra. Por isso preciso saber...").
4. **Recomende uma resposta** em cada pergunta quando fizer sentido, mas a decisão
   é do viber.
5. **Postura consultiva, não fiscal.** Documente e **sinalize** conflitos com o
   padrão ("olha, isso aqui não vai ser aceito na hora de subir pra produção
   porque..."), mas **não reprove nem bloqueie** o projeto. Aprovar/reprovar é do
   TI (etapa de crivo, fora desta skill).
6. **Nunca peça o viber calcular stack ou porte.** Isso é **derivado
   automaticamente** por você a partir das respostas (ver "Classificação
   automática").

---

## Fase 1 — Roteiro da entrevista

Cubra os blocos abaixo, sempre uma pergunta por vez, em português. Adapte a ordem
se a conversa pedir, mas **não pule** nenhum bloco.

### Bloco 1 — Problema e escopo

- Qual problema essa aplicação resolve? (o "porquê" antes do "como")
- Quem vai usar? (área/setor, perfil de usuário)
- **Já existe algo parecido na empresa?** (só para o viber pensar — o TI pode
  recusar por redundância em qualquer etapa; não é você quem decide isso.)

### Bloco 2 — Fluxos e funcionalidades

- Quais são os principais fluxos? (o passo a passo do que o usuário faz, do início
  ao fim)
- **Detalhe o suficiente para construir:** para cada fluxo, entenda o que o
  usuário vê, o que preenche, o que acontece depois. Esses fluxos viram a
  checklist de implementação da Fase 4 e o roteiro de teste da Fase 5.

### Bloco 3 — Dados, DW e LGPD

- Quais dados a aplicação precisa **consultar ou guardar**? (vai puxar algo do DW?
  vai salvar cadastro? vai gerar relatório?)
- **Toca em dado de pessoa** (nome, CPF, e-mail, telefone, endereço)? Se sim,
  quais? → Se sim, registre como **dado pessoal/LGPD** e explique que exige
  back-end, armazenamento cuidadoso e nada de expor no front (regras de higiene
  LGPD, seção 4.4 do padrão).
- **De onde vêm os dados?** Lembre a regra: a **única fonte permitida** de dados
  da empresa é o **DW**. Registre quais fontes do DW serão consumidas.

### Bloco 4 — Integrações

- Precisa conversar com outro sistema? (mesmo que a resposta seja "não sei",
  registre.)

### Bloco 5 — Escala

- Quantas pessoas usariam ao mesmo tempo, mais ou menos? (mesmo aproximado — isso
  ajuda a classificar o porte.)

### Bloco 6 — Segurança (em linguagem de intenção)

- Qualquer pessoa pode ver/usar, ou **só gente autorizada**? → Se exige login,
  explique que **em homologação o login é livre**, mas **em produção vira
  obrigatório** (SSO; enquanto não há SSO, login próprio com as regras básicas —
  senha com hash, mín. 14 caracteres, bloqueio após 5 tentativas, expiração de
  sessão — política completa na seção 4.3 do padrão).
- Vai **receber informação digitada** pelo usuário (formulários, uploads)? →
  Registre que toda entrada será **validada no back-end** e que upload valida tipo
  e tamanho.

### Bloco 7 — Identidade

- Qual o nome da aplicação? Quem é o **responsável** (área, contato)?

---

## Fase 2 — Classificação automática (você calcula, o viber não)

A partir das respostas, **derive a stack e o porte** e informe ao viber em
linguagem simples.

**Regra de stack (seção 2.2 do padrão):**

- **Automação** (roda sozinha/agendada, sem tela, sem banco, sem API) → **Python**.
- **Aplicação estática** (só consulta/leitura, sem back-end, sem banco, sem login)
  → **HTML, CSS e JS puro**.
- **Back-end pequeno** → **Python + Flask** (front com Tailwind).
- **Back-end médio/grande** → **Node + Nitro** (front em React).
- Toda stack em **versão LTS**; o README declara a **versão exata** (ex.:
  `Python 3.12.4`).

**Critério de porte — Flask x Node/Nitro (seção 2.3, só quando há back-end):**
Se atingir **2 dos 3** na faixa médio/grande, use **Node/Nitro**.

| Critério             | Pequeno (Flask) | Médio/Grande (Node/Nitro) |
| -------------------- | --------------- | ------------------------- |
| Endpoints            | até 10          | acima de 10               |
| Tabelas              | até 5           | acima de 5                |
| Usuários simultâneos | até 20          | acima de 20               |

Quando faltar informação para classificar com segurança, **pergunte mais** (número
aproximado de telas, cadastros, relatórios) até conseguir enquadrar.

---

## Fase 3 — Planejamento (escrever, apresentar, confirmar)

Depois de cobrir todos os blocos e classificar, escreva **dois arquivos na raiz do
projeto**:

### 1. `PLANEJAMENTO.md`

```markdown
# Planejamento — <Nome da Aplicação>

> Gerado pela entrevista /supplog-iniciar. Base: Padrões de Desenvolvimento Vibe
> Coding (v1.1).

## Problema

<o que resolve>

## Público / usuários

<área, perfil>

## Fluxos principais

<passo a passo em alto nível — esta lista é a checklist de implementação e o
roteiro de teste>

## Dados

- Fontes do DW consumidas: <...>
- Dados pessoais/LGPD envolvidos: <lista ou "nenhum">
- O que guarda / consulta: <...>

## Integrações

<sistemas externos ou "nenhuma / a definir">

## Escala estimada

<usuários simultâneos aproximados>

## Segurança

- Acesso: <público / restrito por login>
- Entradas de usuário: <formulários, uploads — ou "nenhuma">

## Classificação automática

- **Tipo:** <Automação | Estático | Back-end pequeno | Back-end médio/grande>
- **Stack:** <Python | HTML/CSS/JS | Python+Flask+Tailwind | Node+Nitro+React> (versão LTS)
- **Justificativa do porte:** <critérios que levaram à escolha>

## Regras do padrão que se aplicam a este projeto

<liste apenas as seções relevantes ao caso — ex.: se tem banco, cite 3.1–3.7
(scripts_criacao.sql, seed, SQL puro parametrizado, CriadoEm/AtualizadoEm) e 5.1
(SQLite em homologação); se tem login, cite 4.3; se tem dado pessoal, cite 4.4;
sempre cite 2.4 (nomenclatura), 2.5 (pastas) e 2.6 (arquitetura) da stack
escolhida>

## Pontos de atenção / conflitos com o padrão

<qualquer coisa que o viber quer fazer mas colide com o padrão — sinalizado, não
bloqueado>

## Adendos da construção

<decisões técnicas tomadas durante a Fase 4 que não estavam previstas — ex.: uma
tabela extra que se mostrou necessária. Registrar aqui, nunca inventar fluxo novo>
```

### 2. `README.md` (esqueleto obrigatório — seção 2.7)

Preencha o que já se sabe; marque o resto com
`<!-- TODO: preencher durante o desenvolvimento -->`. **Esses TODOs são dívida da
Fase 4** — ao final da construção, nenhum TODO pode sobrar (exceto o histórico,
que ganha a entrada inicial).

```markdown
# <Nome da Aplicação>

## Descrição

<preenchido>

## Contexto / Motivação

<preenchido>

## Responsável

- Área: <preenchido>
- Contato: <preenchido>
- Data de criação: <preenchido>

## Stack técnica

- <stack> — versão exata: <!-- TODO: declarar versão LTS exata, ex: Python 3.12.4 -->
- Classificação de porte: <preenchido>

## Como rodar localmente

<!-- TODO: preencher durante o desenvolvimento (incluir comando de execução do seed) -->

## Fluxos principais e endpoints

<fluxos em alto nível preenchidos>

<!-- TODO: método e rota, tabelas usadas (leitura/escrita), request, response e erros -->

## Estrutura de dados

<!-- TODO: tabelas principais e finalidade -->

## Fontes de dados do DW consumidas

<preenchido, ou "a definir">

## Dependências externas

<preenchido, ou "nenhuma conhecida">

## Status

Em desenvolvimento

## Histórico de alterações

<!-- TODO: preencher conforme o projeto evolui -->
```

### Confirmação antes de construir

Apresente ao viber, **em linguagem simples**, o resumo do plano: o que vai ser
construído, em qual stack (e por quê, em uma frase), quais regras do padrão vão
valer, e quais pontos de atenção existem. Faça **uma única pergunta**: se ele
confirma o plano para a construção começar. Ajustes que ele pedir aqui são
bem-vindos — atualize os arquivos e reconfirme.

---

## Fase 4 — Construção (o app inteiro, conforme o padrão)

Com o plano confirmado, **construa a aplicação completa**. Regras da construção:

1. **Esqueleto primeiro.** Monte a estrutura de pastas da stack classificada
   exatamente como a seção 2.5 do padrão define (inclui `.env.example`,
   `.gitignore` com `.env`, `README.md`).
2. **Banco antes do código** (quando houver banco): modele as tabelas conforme as
   seções 3.1–3.4 (nomes em português, `UPPER_SNAKE_CASE`/`PascalCase`, PK,
   `CriadoEm`/`AtualizadoEm`, booleanos com prefixo), escreva
   `database/scripts_criacao.sql` (única fonte de verdade do schema, UTF-8,
   seção 3.5–3.6) e o seed com dados 100% fictícios (seção 3.7). Banco local:
   **SQLite** (seção 5.1).
3. **Fluxo a fluxo.** Implemente **todos** os fluxos do `PLANEJAMENTO.md`, um por
   vez. A cada fluxo concluído, conte ao viber em linguagem simples o que ficou
   pronto ("o cadastro de ocorrências já salva e lista — faltam o relatório e a
   tela de busca"). Nada de jargão nos updates.
4. **Arquitetura obrigatória** (seção 2.6): rotas finas; regra de negócio em
   services; acesso a banco isolado e **sempre em SQL puro parametrizado** (sem
   ORM, sem query builder); em React, componentes funcionais, custom hooks e
   chamadas de API em `services/`; middleware para autenticação/validação quando
   houver API.
5. **Nomenclatura** (seção 2.4) em tudo; comentários de código em português.
6. **Segurança desde o início** (seções 4.1–4.2): validação de toda entrada no
   back-end; escape de output; erros tratados sem stack trace para o usuário;
   nenhum segredo hardcoded; `.env.example` versionado.
7. **Login** (seção 4.3): em homologação o acesso é livre — só implemente login se
   o projeto exigir acesso restrito desde já; se implementar, siga a política
   completa (hash bcrypt/argon2, senha mín. 14 caracteres, 3 de 4 categorias,
   bloqueio após 5 tentativas, expiração de sessão, logout real).
8. **Higiene LGPD** (seção 4.4) quando houver dado pessoal: nada de dado pessoal
   em app estático, em URL, em log ou no seed; colete só o que os fluxos
   justificam; fonte de dado da empresa é só o DW.
9. **Escopo fechado.** Não invente fluxos que não foram levantados. Se a
   construção exigir uma decisão técnica nova (ex.: tabela extra), **decida você**
   conforme o padrão e registre em "Adendos da construção" no `PLANEJAMENTO.md`.
10. **README de verdade.** Ao final, resolva **todos** os TODOs do README:
    versão exata da stack, como rodar (incluindo o comando do seed), endpoints
    com método/rota/tabelas/request/response/erros, estrutura de dados. Histórico
    ganha a entrada inicial. Status permanece **Em desenvolvimento**.

### Critério de conclusão (DoD técnico)

A construção só está concluída quando **tudo** abaixo for verdade:

- [ ] Todos os fluxos do `PLANEJAMENTO.md` implementados.
- [ ] Estrutura de pastas e arquitetura conforme seções 2.5 e 2.6 (faça uma
      auto-revisão rápida antes de entregar).
- [ ] Banco criado **pelos scripts** (`scripts_criacao.sql`) e seed executando sem
      erro (quando houver banco).
- [ ] App sobe localmente seguindo **exatamente** as instruções do README (teste
      você mesmo as instruções antes de entregar).
- [ ] Nenhum TODO pendente no README.

---

## Fase 5 — Entrega para o teste do usuário

O "pronto" funcional **é do viber, não seu**. Ao entregar:

1. **Explique o que foi construído** em linguagem simples, fluxo a fluxo.
2. **Ensine a rodar**: passo a passo literal (que comando digitar, que endereço
   abrir no navegador), assumindo zero conhecimento técnico.
3. **Entregue um roteiro de teste**: para cada fluxo do `PLANEJAMENTO.md`, o que
   testar e o que deve acontecer ("cadastre uma ocorrência de teste; ela deve
   aparecer na lista; tente salvar sem preencher o campo X — deve dar aviso").
4. **Diga o caminho à frente**: quando todos os fluxos estiverem OK para ele, o
   próximo passo é rodar a `/supplog-check` (auditoria de conformidade); depois do
   relatório limpo, o handoff ao TI com a `/supplog-handoff`.
5. **Relembre os pontos de atenção** registrados no `PLANEJAMENTO.md` (conflitos
   com o padrão sinalizados na entrevista) — eles vão pesar no crivo do TI.

Se o viber encontrar problema no teste, corrija dentro desta mesma missão: o
ciclo construir → testar → ajustar continua até ele dar o OK funcional.

---

## Padrões de Desenvolvimento & Vibe Coding (v1.1)

> **Documento canônico** — fonte única de verdade dos Padrões de Desenvolvimento da
> Supporte Logística (Supplog) para aplicações criadas com apoio de IA.
> Mora neste `SKILL.md` da `supplog-iniciar`. Ao alterar os padrões aqui, atualize
> as referências embutidas em `supplog-check` e `supplog-handoff`.

### Controle de versão

| Versão | Data       | Autor              | Revisor                                        |
| ------ | ---------- | ------------------ | ---------------------------------------------- |
| 1.0    | 22/07/2026 | Edu Ferreira       | Henrique Fernandes                             |
| 1.1    | 23/07/2026 | Jhonatan Magalhães | _pendente — Edu Ferreira / Henrique Fernandes_ |

**Mudanças da v1.1 sobre o PDF v1.0** _(pendentes de ratificação pelos autores do PDF)_:

1. Conversão do PDF para markdown versionado (este arquivo).
2. **Seção 4.3 (senha):** mínimo elevado de 8 para **14 caracteres**, com política
   corporativa completa (categorias, histórico, rotação). O PDF v1.0 está
   desatualizado neste ponto.
3. **Nova seção 2.6 — Padrões de Arquitetura de Código** (a seção "README
   Obrigatório" foi renumerada de 2.6 para 2.7).
4. **Nova seção 4.4 — Dados Pessoais (Higiene LGPD).**

---

### 1. Objetivo

Este documento define os padrões obrigatórios de desenvolvimento e banco de dados
para aplicações criadas com apoio de IA, visando previsibilidade, legibilidade e
agilidade de avaliação técnica antes da promoção para produção. O documento está
organizado em quatro grandes blocos: **Desenvolvimento**, **Dados**, **Segurança**
e **Ambiente**.

---

### 2. Desenvolvimento

#### 2.1 Planejamento da Aplicação

Antes de iniciar o desenvolvimento, o solicitante deve estruturar bem a ideia
respondendo às perguntas abaixo. A classificação técnica da aplicação (porte e
stack a ser utilizada, conforme seção 2.3) é **derivada automaticamente** a partir
dessas respostas — não é necessário que o solicitante calcule isso manualmente.

> Essas perguntas devem ser **discutidas diretamente com o Claude**, antes do
> início do desenvolvimento, e não apenas respondidas isoladamente pelo
> solicitante.

1. Qual problema essa aplicação resolve? (o "porquê" antes do "como")
2. Quem vai usar essa aplicação? (área/setor, perfil de usuário)
3. Quais são os principais fluxos/funcionalidades? (o passo a passo do que o
   usuário faz, do início ao fim)
4. Quais dados a aplicação precisa consultar ou guardar? (ex.: vai puxar algo do
   DW? vai salvar cadastro? vai gerar relatório?)
5. Existe alguma integração necessária com outro sistema? (mesmo que a resposta
   seja "não sei", vale registrar)
6. Estimativa de quantas pessoas usariam a aplicação ao mesmo tempo (mesmo que
   aproximada).

#### 2.2 Regras Gerais de Stack

- **Automações:** Python.
- **Aplicação estática** (ex.: landing page): HTML, CSS e JS puro.
- **Aplicação com back-end pequeno:** Python + Flask (front com Tailwind).
- **Aplicação com back-end médio/grande:** Node + Nitro (front em React).
- Toda stack deve estar em **versão LTS**.
- O README deve declarar a **versão exata** da stack principal utilizada
  (ex.: `Python 3.12.4`, `Node 20.15.0`).

**Exemplos de enquadramento:**

- **Automação (Python):** script executado via agendador (cron), sem interface,
  que lê um conjunto de arquivos de entrada, aplica uma transformação/regra de
  negócio e gera uma saída (arquivo ou notificação). Não possui banco de dados nem
  exposição de API.
- **Aplicação estática (HTML/CSS/JS puro):** página de consulta somente leitura,
  sem back-end e sem banco de dados, com conteúdo carregado de um arquivo estático
  (ex.: `.json` local). Sem autenticação, sem escrita de dados.
- **Aplicação pequena (Flask):** aplicação com poucas telas, até 3 tabelas no
  banco, até 10 endpoints (operações básicas de criar/listar/detalhar/atualizar um
  cadastro simples), uso concorrente estimado abaixo de 20 usuários.
- **Aplicação média/grande (Node/Nitro):** aplicação com múltiplos módulos, mais
  de 5 tabelas no banco, mais de 10 endpoints (podendo incluir dashboards,
  relatórios e regras de negócio mais complexas), uso concorrente estimado acima
  de 20 usuários.

#### 2.3 Critério de Classificação de Porte (Flask x Node/Nitro)

Aplica-se apenas quando houver back-end. Se a aplicação atingir **2 dos 3
critérios** abaixo na faixa "médio/grande", deve ser desenvolvida em Node/Nitro.

| Critério             | Pequeno (Flask) | Médio/Grande (Node/Nitro) |
| -------------------- | --------------- | ------------------------- |
| Endpoints            | até 10          | acima de 10               |
| Tabelas              | até 5           | acima de 5                |
| Usuários simultâneos | até 20          | acima de 20               |

#### 2.4 Nomenclatura de Código

**Flask / Python:**

- Arquivos e módulos: `snake_case` (ex.: `cliente_routes.py`)
- Classes: `PascalCase` (ex.: `ClienteModel`)
- Funções e variáveis: `snake_case` (ex.: `buscar_cliente_por_id`)
- Constantes: `UPPER_SNAKE_CASE`

**Node / Nitro / React:**

- Rotas Nitro: `nome-recurso.get.ts`, `nome-recurso.post.ts`
- Componentes React: `PascalCase.tsx` (ex.: `ClienteCard.tsx`)
- Hooks: `camelCase` prefixado com `use` (ex.: `useClientes.ts`)
- Funções e variáveis: `camelCase`
- Constantes: `UPPER_SNAKE_CASE`

**Geral:** comentários de código devem ser escritos em **português**.

#### 2.5 Estrutura de Pastas Padrão

##### 2.5.1 Flask (fullstack + Tailwind)

```
nome-da-aplicacao/
├── app/
│   ├── __init__.py
│   ├── routes/
│   ├── models/
│   ├── services/
│   ├── templates/
│   └── static/
│       ├── css/
│       ├── js/
│       └── img/
├── database/
│   ├── app.db
│   ├── scripts_criacao.sql
│   └── seed.sql
├── tests/
├── migrations/
├── .env.example
├── requirements.txt
├── tailwind.config.js
├── package.json
├── README.md
└── .gitignore
```

##### 2.5.2 Node / Nitro (fullstack, front em React)

```
nome-da-aplicacao/
├── server/                  # Nitro (backend)
│   ├── api/
│   ├── routes/
│   ├── middleware/
│   ├── plugins/
│   ├── utils/
│   └── database/
│       ├── app.db
│       ├── scripts_criacao.sql
│       └── seed.ts
├── client/                  # React (frontend)
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/
│   │   └── App.tsx
│   ├── public/
│   ├── index.html
│   ├── vite.config.ts
│   └── package.json
├── nitro.config.ts
├── package.json
├── tsconfig.json
├── .env.example
├── README.md
└── .gitignore
```

##### 2.5.3 Estático (HTML / CSS / JS puro)

```
nome-da-aplicacao/
├── index.html
├── css/
│   └── style.css
├── js/
│   └── script.js
├── img/
└── README.md
```

#### 2.6 Padrões de Arquitetura de Código _(novo na v1.1)_

A estrutura de pastas (2.5) define **onde** o código vive; as regras abaixo definem
**o que** cada camada pode fazer. Cada camada tem responsabilidade única.

**Flask:**

- **Rotas** (`app/routes/`) são finas: recebem a requisição, validam/delegam,
  chamam o service e devolvem a resposta. **Proibido regra de negócio em rota.**
- **Regra de negócio** vive em `app/services/`.
- **Acesso a banco** isolado em `app/models/`, sempre em SQL puro parametrizado
  (seção 3.5). Rota não acessa banco diretamente.
- **Templates** (Jinja2) apenas exibem dados; proibido regra de negócio em
  template.

**Node / Nitro (back-end):**

- **Rotas** (`server/api/`, `server/routes/`) são finas: validam/delegam e
  devolvem a resposta. **Proibido regra de negócio em rota.**
- **Middleware** (`server/middleware/`) concentra preocupações transversais:
  autenticação, validação de entrada, logging.
- **Regra de negócio** vive no servidor (`server/utils/` ou camada de serviço
  equivalente) — **nunca no cliente**.
- **Acesso a banco** isolado da camada de rota, sempre em SQL puro parametrizado.

**React (front-end):**

- **Componentes funcionais** obrigatórios — proibido class components.
- **Lógica de estado reutilizável** em custom hooks (`client/src/hooks/`,
  prefixo `use`).
- **Chamadas à API** isoladas em `client/src/services/` — componente não faz
  `fetch` direto.
- **Páginas** (`client/src/pages/`) compõem **componentes reutilizáveis**
  (`client/src/components/`).
- O front exibe e coleta dados; regra de negócio fica no back-end.

**Estático:**

- JS puro, sem framework e sem etapa de build; separação de HTML, CSS e JS
  conforme 2.5.3.

#### 2.7 README Obrigatório

Todo projeto deve conter um `README.md` completo, mesmo que extenso, contendo
obrigatoriamente:

- Descrição da aplicação
- Contexto / Motivação
- Responsável (área, contato, data de criação)
- Stack técnica, incluindo versão exata (ex.: `Python 3.12.4`) e classificação de
  porte
- Como rodar localmente (incluindo comando de execução do seed)
- Fluxos principais e endpoints, incluindo: método e rota, tabelas utilizadas
  (leitura/escrita), request, response e possíveis erros
- Estrutura de dados (tabelas principais e finalidade)
- Fontes de dados do DW consumidas
- Dependências externas
- Status (Em desenvolvimento / Em teste / Aguardando aprovação / Em produção)
- Histórico de alterações

---

### 3. Dados

As convenções abaixo definem o padrão obrigatório de nomenclatura e modelagem
para bancos de dados de todas as aplicações.

#### 3.1 Convenções Gerais

- Nomes de tabelas e colunas **em português**.
- Tabelas em `UPPER_SNAKE_CASE` (ex.: `OCORRENCIA_TIPO`).
- Colunas em `PascalCase` (ex.: `ClienteNome`).
- Siglas seguem `PascalCase` (ex.: `ChaveNfe`, `CodigoIbge`, `XmlNfe`).
- Nomenclatura deve favorecer agrupamento lógico e ordenação alfabética.
- Tabelas relacionadas começam pelo mesmo domínio (ex.: `CLIENTE`,
  `CLIENTE_ENDERECO`).
- Colunas usam o contexto antes do atributo (ex.: `EnderecoUf`, `ClienteEmail`).

#### 3.2 Chave Primária

- Toda tabela deve ter chave primária. Exceções exigem justificativa técnica
  documentada.
- Chave natural estável pode ser usada como PK quando existir.
- Na ausência de chave natural, usar `Id int identity(1,1)` como PK artificial.
- Para tabelas de grande volume, usar `Id bigint identity(1,1)`.
- **Não usar `uuid`** como padrão de PK.

#### 3.3 Colunas Obrigatórias

- `CriadoEm datetime not null` — preenchido automaticamente, em horário de
  Brasília.
- `AtualizadoEm datetime null` — quando fizer sentido rastrear alteração, também
  em horário de Brasília.

#### 3.4 Colunas Booleanas

- Prefixos preferenciais: `Esta`, `Possui`, `Permite`, `Deve` (ex.: `EstaAtivo`,
  `PossuiComprovante`).
- Quando nenhum prefixo fizer sentido e o nome dependeria de Is/Eh, omitir o
  prefixo (ex.: `Vip`, `Paletizado`).

#### 3.5 Scripts de Criação (sem uso de ORM)

> O uso de **ORM não está aprovado**. Todo `CREATE TABLE` deve estar descrito
> corretamente em arquivo `.sql` versionado, servindo como **única fonte de
> verdade do schema** — essencial para viabilizar o reteste completo na migração
> de SQLite para o PostgreSQL de produção.

- Arquivo: `database/scripts_criacao.sql`
- Todo acesso a banco deve usar **SQL puro** (sem query builder).
- Uso obrigatório de **queries parametrizadas/prepared statements**. Proibida a
  concatenação direta de valores de input na string SQL (prevenção de SQL
  Injection).

#### 3.6 Encoding

Todo banco, arquivo e comunicação HTTP deve usar **UTF-8**.

```sql
PRAGMA encoding = 'UTF-8'; -- no script de criação do SQLite
```

#### 3.7 Dados de Teste (Seed)

Todo projeto com banco de dados deve conter script de seed com dados fictícios,
permitindo teste imediato sem depender de dado real.

- Arquivo: `database/seed.sql` (ou `seed.py` / `seed.ts` quando necessária lógica
  extra).
- Comando de execução deve constar na seção "Como Rodar Localmente" do README.

---

### 4. Segurança

Os itens abaixo cobrem cuidados básicos de segurança na camada de aplicação. Não
abrangem infraestrutura ou rede, que são de responsabilidade de outra área.

#### 4.1 Segurança em Desenvolvimento

- **Validação e sanitização de entrada:** toda entrada do usuário (formulário,
  query string, upload) deve ser validada no back-end, nunca somente no front-end.
- **Prevenção de SQL Injection:** uso obrigatório de queries
  parametrizadas/prepared statements, sem concatenação direta de valores de input
  na string SQL.
- **Prevenção de XSS:** todo output renderizado em templates/HTML deve ser
  escapado corretamente (padrão do Jinja2 e do React já ajuda, mas não deve ser
  desativado).
- **Tratamento de erros e logs:** nunca expor stack trace ou mensagem interna de
  erro de banco para o usuário final; logs não devem registrar dado sensível em
  texto puro.
- **Upload de arquivos** (quando aplicável): validar o tipo real do arquivo (não
  somente a extensão do nome) e limitar o tamanho aceito.

#### 4.2 Variáveis de Ambiente e Segredos

- Nunca commitar o arquivo `.env` (deve constar no `.gitignore`).
- `.env.example` obrigatório e versionado, com todas as chaves necessárias
  (valores fictícios/vazios).
- Nenhum segredo hardcoded no código.
- Carregamento via biblioteca padrão do ecossistema (`python-dotenv` no Flask;
  `process.env` no Node).
- Nenhum segredo real em README, comentário de código ou mensagem de commit.

#### 4.3 Autenticação — Login Básico _(política de senha atualizada na v1.1)_

Enquanto o SSO (ver seção 5.4) não está disponível, toda aplicação promovida para
produção deve implementar seu próprio sistema de login, seguindo as regras básicas
abaixo.

- Senha nunca armazenada em texto puro — obrigatório uso de hash (**bcrypt** ou
  **argon2**).
- Limite de tentativas de login, com **bloqueio temporário após 5 tentativas**
  erradas.
- Sessão com **expiração por inatividade**.
- **Logout deve invalidar a sessão de fato**, não apenas ocultar a tela no
  front-end.

**Formato de senha (padrão corporativo):**

- Ter ao menos **14 caracteres**.
- Não conter o nome da conta nem mais de 4 caracteres consecutivos de partes do
  nome completo do usuário.
- Conter caracteres de **três destas quatro categorias**: maiúsculos (A-Z),
  minúsculos (a-z), dígitos de base 10 (0-9), não alfabéticos (ex.: `!`, `$`,
  `#`, `%`).
- Não estar no histórico de senhas (**6** senhas memorizadas).
- Rotação a cada **90 dias** (expiração).

#### 4.4 Dados Pessoais — Higiene LGPD _(novo na v1.1)_

Regras de **higiene técnica** no trato de dados pessoais (nome, CPF, e-mail,
telefone, endereço etc.). Elas **não substituem avaliação jurídica** de
conformidade com a LGPD — o que se verifica aqui é a disciplina técnica mínima
esperada de qualquer aplicação que toque dado de pessoa.

1. **Inventário:** todo dado pessoal tratado deve estar listado no
   `PLANEJAMENTO.md` e/ou `README.md` (qual dado, de onde vem, para quê).
2. **Exige back-end:** proibido tratar dado pessoal em aplicação estática; o
   front-end recebe apenas o necessário para exibição.
3. **Acesso restrito:** aplicação que trata dado pessoal exige login —
   obrigatório em produção, recomendado já em homologação.
4. **Seed 100% fictício:** proibido dado pessoal real em scripts de seed.
5. **Nunca em URL:** dado pessoal não trafega em query string/parâmetros de URL.
6. **Logs:** não registrar dado pessoal em texto puro (reforço da seção 4.1).
7. **Minimização:** coletar/armazenar apenas os dados que os fluxos documentados
   justificam.
8. **Fonte:** dado pessoal da empresa é consumido exclusivamente via DW (única
   fonte permitida).

---

### 5. Ambiente (Homologação x Produção)

Esta seção descreve o que é livre durante o desenvolvimento em homologação e quais
regras passam a valer quando a aplicação é promovida para produção.

#### 5.1 Banco de Dados

- **Homologação:** SQLite.
- **Produção:** PostgreSQL. O banco é criado somente após aprovação do projeto.

#### 5.2 Dados de Teste (Seed)

- **Homologação:** script de seed deve existir e ser executado para testes.
- **Produção:** o script **não pode continuar existindo** no projeto.

#### 5.3 Variáveis de Ambiente e Configuração

- **Homologação:** valores fictícios/de teste, sob responsabilidade do dev.
- **Produção:** o dev repassa os valores reais para o TI aplicar.

#### 5.4 Autenticação

- **Homologação:** livre, sem exigência de login/SSO.
- **Produção:** obrigatório uso de **SSO** (autenticação centralizada).

> O SSO ainda não está implementado. Enquanto isso, cada aplicação promovida para
> produção deve seguir as regras básicas de login descritas na seção 4.3. Quando o
> SSO estiver disponível, as aplicações já em produção passarão por uma modificação
> futura para integração.

#### 5.5 Responsabilidade de Manutenção

- **Homologação:** responsabilidade do dev/área que criou a aplicação.
- **Produção:** responsabilidade passa a ser do TI.

#### 5.6 Processo de Deploy/Subida

- **Homologação:** solicitação simples, com permanência de até 10 dias úteis.
- **Produção:** sujeito ao SLA de avaliação descrito a seguir.

#### 5.7 SLA de Avaliação e Subida para Produção

1. A aplicação permanece em homologação por até **10 dias úteis**.
2. Dentro desse prazo (ou ao final dele), o dev pode solicitar a subida para
   produção.
3. O TI tem SLA de **10 dias úteis** para avaliar desenvolvimento e segurança, e
   realizar os ajustes necessários.

- **Se aprovado:** a aplicação sobe para produção.
- **Se reprovado:** a aplicação sai da homologação; o dev corrige localmente e
  solicita nova homologação.
- **Se os 10 dias de homologação terminarem sem solicitação de produção:** o TI
  contata o dev para decidir entre avaliar ou remover. Se em até **2 dias úteis**
  não houver resposta do dev ou de seu gestor, a aplicação é **removida
  automaticamente**.

#### 5.8 Restrição de Ambiente de Produção

> É **proibido** publicar, mesmo que aprovada, qualquer aplicação que consome
> dados do DW da empresa em ambiente de produção que não seja o ambiente de
> produção interno da própria empresa. Infraestrutura externa ou de terceiros não
> é permitida para esse fim.

#### 5.9 Critério de Recusa por Redundância

O TI pode recusar uma proposta de aplicação, **em qualquer etapa** (homologação ou
produção), caso já exista solução equivalente entre as aplicações já consolidadas
da empresa. A decisão é registrada com justificativa e é final.

---

### 6. Glossário

- **DW (Data Warehouse):** base de dados centralizada da empresa, **única fonte
  permitida** de consumo de dados pelas aplicações.
- **LTS (Long Term Support):** versão de uma linguagem/tecnologia com suporte
  estendido e maior estabilidade, recomendada para uso em produção.
- **ORM (Object-Relational Mapping):** camada de software que traduz código
  orientado a objetos em comandos de banco de dados. **Não aprovado** neste
  padrão — o acesso ao banco deve ser feito via SQL puro.
- **SQLite:** banco de dados leve, baseado em arquivo local, utilizado no ambiente
  de testes/homologação.
- **PostgreSQL:** banco de dados relacional utilizado no ambiente de produção,
  criado somente após a aprovação do projeto.
- **Seed:** script que popula o banco de dados com dados fictícios, permitindo
  testar a aplicação sem depender de dado real.
- **Prepared Statements / Queries Parametrizadas:** forma segura de executar
  comandos SQL passando valores de entrada como parâmetros, e não concatenados na
  string da query. Principal proteção contra SQL Injection.
- **SQL Injection:** vulnerabilidade em que um invasor insere comandos SQL
  maliciosos através de campos de entrada não validados.
- **XSS (Cross-Site Scripting):** vulnerabilidade que permite a injeção de scripts
  maliciosos em páginas visualizadas por outros usuários, geralmente por falta de
  escape de conteúdo dinâmico.
- **SSO (Single Sign-On):** modelo de autenticação centralizada em que o usuário
  faz login uma única vez e passa a ter acesso às aplicações permitidas, sem
  precisar autenticar novamente em cada uma.
- **Endpoint:** uma rota/URL específica de uma API que executa uma ação (ex.:
  criar, listar, atualizar um registro).
- **Hash (bcrypt / argon2):** algoritmo que transforma uma senha em um valor
  irreversível, usado para armazenar senhas com segurança, sem guardar o texto
  original.
- **SLA (Service Level Agreement):** prazo/acordo de nível de serviço, usado neste
  documento para definir o tempo de resposta do TI na avaliação de aplicações.
- **LGPD (Lei Geral de Proteção de Dados):** lei brasileira que disciplina o
  tratamento de dados pessoais. Neste padrão, a seção 4.4 define a **higiene
  técnica** mínima — a conformidade jurídica é avaliada fora do escopo das skills.
