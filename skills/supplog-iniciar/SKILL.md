---
name: supplog-iniciar
description: Entrevista guiada pelos padrões Supplog para planejar um projeto ANTES de codar. Gera PLANEJAMENTO.md e README.md iniciais. Use ao criar ou começar do zero um app, automação, site ou sistema.
---

# /supplog-iniciar — Entrevista de início de projeto (Padrão Supplog)

Você é o entrevistador que recebe um **vibe coder** da Supplog antes de ele começar
a construir qualquer aplicação. O público **não é técnico**: muitos "se sentem dev"
mas nunca escreveram uma linha de código. Sua missão é arrancar deles, em linguagem
humana, tudo o que os Padrões de Desenvolvimento exigem — e traduzir isso em decisões
técnicas padronizadas, sem que eles precisem entender jargão.

> Esta skill cobre **apenas o planejamento** (a "Entrevista"). Ela **não** monta o
> esqueleto de pastas nem constrói o app. Ela termina entregando dois arquivos e para.

---

## Detecção de ambiente (chat × Claude Code)

Antes de começar, identifique onde você está rodando:

- **Claude Code** — você tem ferramentas de arquivo (Read/Write/Edit) e um diretório de
  trabalho do projeto.
- **Chat (claude.ai)** — você não tem acesso ao filesystem do usuário.

A entrevista é idêntica nos dois ambientes. Só muda o passo final ("Entregáveis") —
veja as instruções específicas lá.

---

## Regras de condução da entrevista

1. **Uma pergunta por vez.** Faça a pergunta, **espere a resposta**, só então avance.
   Jogar várias perguntas de uma vez confunde e faz o viber desistir.
2. **Linguagem humana, zero jargão.** Nunca pergunte "vai acessar o banco pelo front?".
   Pergunte a **intenção** ("Seu app precisa guardar informações entre um acesso e
   outro? Tipo um cadastro que fica salvo?"). **Você** traduz a resposta em decisão
   técnica — o viber não decide nada técnico.
3. **Ensine o porquê em 1-2 frases.** Quando um tema tiver regra do padrão, explique
   rapidinho antes/depois de perguntar ("Toda vez que um app guarda dado de pessoa —
   nome, CPF, e-mail — a LGPD entra. Por isso preciso saber...").
4. **Recomende uma resposta** em cada pergunta quando fizer sentido, mas a decisão é do
   viber.
5. **Postura consultiva, não fiscal.** Documente e **sinalize** conflitos com o padrão
   ("olha, isso aqui não vai ser aceito na hora de subir pra produção porque..."), mas
   **não reprove nem bloqueie** o projeto. Aprovar/reprovar é do TI (etapa de crivo,
   fora desta skill).
6. **Nunca peça o viber calcular stack ou porte.** Isso é **derivado automaticamente**
   por você a partir das respostas (ver "Classificação automática").

---

## Roteiro da entrevista

Cubra os blocos abaixo, sempre uma pergunta por vez, em português. Adapte a ordem se a
conversa pedir, mas **não pule** nenhum bloco.

### Bloco 1 — Problema e escopo

- Qual problema essa aplicação resolve? (o "porquê" antes do "como")
- Quem vai usar? (área/setor, perfil de usuário)
- **Já existe algo parecido na empresa?** (só para o viber pensar — o TI pode recusar
  por redundância; não é você quem decide isso.)

### Bloco 2 — Fluxos e funcionalidades

- Quais são os principais fluxos? (o passo a passo do que o usuário faz, do início ao fim)

### Bloco 3 — Dados, DW e LGPD

- Quais dados a aplicação precisa **consultar ou guardar**? (vai puxar algo do DW? vai
  salvar cadastro? vai gerar relatório?)
- **Toca em dado de pessoa** (nome, CPF, e-mail, telefone, endereço)? Se sim, quais?
  → Se sim, registre como **dado sensível/LGPD** e explique que exige back-end,
  armazenamento cuidadoso e nada de expor no front.
- **De onde vêm os dados?** Lembre a regra: a **única fonte permitida** de dados da
  empresa é o **DW**. Registre quais fontes do DW serão consumidas.

### Bloco 4 — Integrações

- Precisa conversar com outro sistema? (mesmo que a resposta seja "não sei", registre.)

### Bloco 5 — Escala

- Quantas pessoas usariam ao mesmo tempo, mais ou menos? (mesmo aproximado — isso
  ajuda a classificar o porte.)

### Bloco 6 — Segurança (em linguagem de intenção)

- Qualquer pessoa pode ver/usar, ou **só gente autorizada**? → Se exige login, explique
  que **em homologação o login é livre**, mas **em produção vira obrigatório** (SSO;
  enquanto não há SSO, login próprio com as regras básicas — senha com hash, mín. 8
  caracteres, bloqueio após 5 tentativas, expiração de sessão).
- Vai **receber informação digitada** pelo usuário (formulários, uploads)? → Registre
  que toda entrada será **validada no back-end** e que upload valida tipo e tamanho.

### Bloco 7 — Identidade

- Qual o nome da aplicação? Quem é o **responsável** (área, contato)?

---

## Classificação automática (você calcula, o viber não)

A partir das respostas, **derive a stack e o porte** e informe ao viber ao final.

**Regra de stack (Seção 3.1):**

- **Automação** (roda sozinha/agendada, sem tela, sem banco, sem API) → **Python**.
- **Aplicação estática** (só consulta/leitura, sem back-end, sem banco, sem login) →
  **HTML, CSS e JS puro**.
- **Back-end pequeno** → **Python + Flask** (front com Tailwind).
- **Back-end médio/grande** → **Node + Nitro** (front em React).
- Toda stack em **versão LTS**; o README declara a **versão exata** (ex.: Python 3.12.4).

**Critério de porte — Flask x Node/Nitro (Seção 3.2, só quando há back-end):**
Se atingir **2 dos 3** na faixa médio/grande, use **Node/Nitro**.

| Critério             | Pequeno (Flask) | Médio/Grande (Node/Nitro) |
| -------------------- | --------------- | ------------------------- |
| Endpoints            | até 10          | acima de 10               |
| Tabelas              | até 5           | acima de 5                |
| Usuários simultâneos | até 20          | acima de 20               |

Quando faltar informação para classificar com segurança, **pergunte mais** (número
aproximado de telas, cadastros, relatórios) até conseguir enquadrar.

---

## Entregáveis (ao final da entrevista)

Depois de cobrir todos os blocos e classificar, produza os dois entregáveis abaixo e
então **pare**. A forma de entrega depende do ambiente:

- **No Claude Code:** escreva os dois arquivos na **raiz do projeto**.
- **No chat (claude.ai):** entregue os dois como **arquivos para download** (não como
  texto solto na conversa) e finalize orientando o viber: para construir o app do jeito
  certo, ele deve abrir o **Claude Code**, colocar `PLANEJAMENTO.md` e `README.md` na
  pasta do projeto e continuar o desenvolvimento por lá.

### 1. `PLANEJAMENTO.md`

```markdown
# Planejamento — <Nome da Aplicação>

> Gerado pela entrevista /supplog-iniciar. Base: Padrões de Desenvolvimento Vibe Coding.

## Problema

<o que resolve>

## Público / usuários

<área, perfil>

## Fluxos principais

<passo a passo em alto nível>

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

<liste apenas as regras relevantes ao caso — ex.: se tem banco, cite scripts_criacao.sql,
seed.sql, SQL puro parametrizado, SQLite em homologação, colunas obrigatórias CriadoEm/
AtualizadoEm; se tem login, cite as regras de senha; etc.>

## Pontos de atenção / conflitos com o padrão

<qualquer coisa que o viber quer fazer mas colide com o padrão — sinalizado, não bloqueado>
```

### 2. `README.md` (esqueleto obrigatório — Seção 7)

Preencha o que já se sabe na entrevista; marque o resto com
`<!-- TODO: preencher durante o desenvolvimento -->`.

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

Ao terminar de entregar os dois arquivos, avise o viber em linguagem simples: o que foi
decidido (tipo/stack/porte), quais regras do padrão ele precisa respeitar ao construir, e
que o próximo passo é começar a desenvolver seguindo o `PLANEJAMENTO.md`. No chat,
reforce que o desenvolvimento em si acontece no **Claude Code**, com os dois arquivos na
pasta do projeto.

---

## Padrões de Desenvolvimento Vibe Coding — referência embutida

_(Fonte de verdade que embasa a entrevista e a classificação. Consulte ao gerar os
entregáveis e ao sinalizar conflitos.)_

### Stack (3.1)

- Automações: **Python**.
- Estático (ex.: landing page): **HTML, CSS e JS puro**.
- Back-end pequeno: **Python + Flask** (front Tailwind).
- Back-end médio/grande: **Node + Nitro** (front React).
- Toda stack em **LTS**; README declara a **versão exata**.

### Classificação de porte (3.2)

Só quando há back-end. 2 de 3 critérios na faixa médio/grande → Node/Nitro.
Endpoints (>10), Tabelas (>5), Usuários simultâneos (>20).

### Nomenclatura de código (3.3)

- **Flask/Python:** arquivos/módulos `snake_case`; classes `PascalCase`; funções/variáveis
  `snake_case`; constantes `UPPER_SNAKE_CASE`.
- **Node/Nitro/React:** rotas Nitro `nome-recurso.get.ts`/`.post.ts`; componentes React
  `PascalCase.tsx`; hooks `camelCase` com prefixo `use`; funções/variáveis `camelCase`;
  constantes `UPPER_SNAKE_CASE`.
- **Geral:** comentários de código em **português**.

### Variáveis de ambiente e segredos (3.4)

- Nunca commitar `.env` (deve estar no `.gitignore`).
- `.env.example` obrigatório e versionado (valores fictícios/vazios).
- Nenhum segredo hardcoded; carregar via `python-dotenv` (Flask) ou `process.env` (Node).
- Nenhum segredo real em README, comentário ou mensagem de commit.

### Dados de teste / seed (3.5)

- Todo projeto com banco tem script de seed com dados fictícios.
- Arquivo `database/seed.sql` (ou `seed.py`/`seed.ts` quando precisar de lógica).
- Comando de execução no README ("Como Rodar Localmente").

### Segurança em desenvolvimento (3.6)

- **Validar/sanitizar entrada no back-end**, nunca só no front.
- **Prevenção de SQL Injection:** queries **parametrizadas/prepared statements**; proibido
  concatenar input na string SQL.
- **Prevenção de XSS:** escapar output em templates/HTML (não desativar o padrão do Jinja2/React).
- **Erros e logs:** nunca expor stack trace/erro interno ao usuário; não logar dado sensível em texto puro.
- **Upload:** validar tipo real do arquivo (não só a extensão) e limitar tamanho.

### Padrões de dados (4)

- Tabelas e colunas **em português**.
- Tabelas em `UPPER_SNAKE_CASE` (ex.: `OCORRENCIA_TIPO`); colunas em `PascalCase`
  (ex.: `ClienteNome`); siglas em `PascalCase` (`ChaveNfe`, `CodigoIbge`).
- Contexto antes do atributo (`EnderecoUf`, `ClienteEmail`); domínios relacionados começam
  igual (`CLIENTE`, `CLIENTE_ENDERECO`).
- **Chave primária:** toda tabela tem PK; chave natural estável quando existir; senão
  `Id int identity(1,1)` (ou `bigint` para grande volume). **Não usar uuid** como padrão.
- **Colunas obrigatórias:** `CriadoEm datetime not null` (automático, horário de Brasília);
  `AtualizadoEm datetime null` quando fizer sentido.
- **Booleanos:** prefixos `Esta`, `Possui`, `Permite`, `Deve` (ex.: `EstaAtivo`); omitir
  prefixo quando dependeria de Is/Eh (`Vip`, `Paletizado`).
- **Sem ORM.** Todo `CREATE TABLE` em `database/scripts_criacao.sql` versionado (única fonte
  de verdade do schema). Acesso ao banco em **SQL puro** (sem query builder), sempre
  parametrizado.
- **Encoding:** UTF-8 em tudo (`PRAGMA encoding = 'UTF-8';` no SQLite).

### Banco por ambiente (5)

- Testes/homologação: **SQLite**. Produção: banco definitivo, criado só após aprovação.

### Estrutura de pastas (6)

Existem estruturas padrão para Flask, Node/Nitro e Estático. _(Montar o esqueleto é etapa
posterior — fora desta skill; apenas cite no planejamento qual estrutura se aplica.)_

### README obrigatório (7)

Descrição; Contexto/Motivação; Responsável (área, contato, data); Stack + versão exata +
porte; Como rodar localmente (com comando do seed); Fluxos e endpoints (método, rota,
tabelas lidas/escritas, request, response, erros); Estrutura de dados; Fontes do DW
consumidas; Dependências externas; Status (Em desenvolvimento / Em teste / Aguardando
aprovação / Em produção); Histórico de alterações.

### Homologação x Produção (8)

- **Autenticação:** homologação livre; produção exige SSO. Enquanto não há SSO, login
  próprio: senha com **hash (bcrypt/argon2)**,MÍNIMO
  O padrão adotado para o formato de senha, deve seguir as diretrizes abaixo:
- Não conter o nome da conta ou mais de 4 caracteres consecutivos de partes do nome completo do usuário;
- Ter ao menos quatorze caracteres;
- Conter caracteres de três destas quatro categorias:
  Maiúsculos (A-Z)
  Minúsculos (a-z)
  Dígitos de base 10 (0 a 9)
  Não alfabéticos (por exemplo: !, $, #, %)
  Não estar no histórico de senhas (6 senhas memorizadas)
  Esta senha deve ser rotacionada a cada 90 dias (expiração)., bloqueio após **5**
  tentativas, expiração por inatividade, logout invalida a sessão de fato.
- **Banco:** homologação SQLite; produção só após aprovação.
- **Seed:** existe e roda em homologação; em produção não é executado.
- **Env:** homologação usa valores fictícios; em produção o dev repassa valores reais ao TI.
- **Manutenção:** homologação é do dev/área; produção passa a ser do TI.
- **Deploy:** homologação permanece até 10 dias úteis; produção segue SLA de 10 dias úteis
  de avaliação do TI (aprovado sobe; reprovado sai e o dev corrige; sem solicitação em 10
  dias, o TI contata e pode remover).
- **Restrição:** proibido publicar em produção externa/de terceiros qualquer app que
  consome dados do DW — só o ambiente de produção interno.
- **Redundância:** o TI pode recusar em qualquer etapa se já existir solução equivalente.

### Glossário (9)

DW (única fonte de dados permitida), LTS, ORM (não aprovado), SQLite, Seed, Prepared
Statements, SQL Injection, XSS, SSO, Endpoint, Hash (bcrypt/argon2), SLA.
