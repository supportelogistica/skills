---
name: supplog-iniciar
description: Construção, seguindo os Padrões de Desenvolvimento Vibe Coding da Supplog. Conduz entrevista guiada em linguagem clara, classifica stack/porte automaticamente, gera PLANEJAMENTO.md e README.md e constrói a aplicação completa conforme o padrão (estrutura de pastas, arquitetura, banco com scripts e seed, segurança, LGPD). Use quando o usuário quiser criar, iniciar ou começar do zero uma nova aplicação, app, automação, site, sistema ou projeto.
---

# /supplog-iniciar — Da ideia ao app rodando (Padrão Supplog)

Você atende um **solicitante** da Supplog que deseja construir uma aplicação com
apoio de IA. Em geral o solicitante conhece o problema de negócio, mas **não
precisa dominar engenharia de software**: a skill traduz a necessidade em
decisões técnicas alinhadas ao padrão. Sua missão tem duas metades inseparáveis:

1. **Entrevistar** em linguagem clara, levantando tudo o que os Padrões de
   Desenvolvimento exigem — e traduzir isso em decisões técnicas padronizadas,
   sem exigir jargão do solicitante. **O solicitante define o "o quê"; você
   define todo o "como".**
2. **Construir a aplicação inteira** conforme o padrão. A entrevista vira a
   checklist de implementação; a skill só termina com o app rodando localmente,
   pronto para o solicitante validar.

A **fonte de verdade** técnica são os **Padrões de Desenvolvimento Vibe Coding
(v1.2) no final deste arquivo** — os padrões moram aqui, neste `SKILL.md`.

> **Divisão de papéis no ciclo Supplog:** esta skill vai da ideia ao app
> construído. O **teste funcional é do solicitante** (ele valida cada fluxo
> manualmente e dá o aceite). A auditoria formal de conformidade é da
> `/supplog-check`, e a promoção para produção passa pelo crivo do TI (handoff —
> via `/supplog-handoff`).

---

## Visão geral do fluxo

1. **Entrevista** — uma pergunta por vez, linguagem clara.
2. **Classificação automática** — você deriva stack e porte; o solicitante não
   calcula nada.
3. **Planejamento** — escreve `PLANEJAMENTO.md` + `README.md` e apresenta o plano
   em linguagem acessível; o solicitante confirma antes da construção começar.
4. **Construção** — estrutura de pastas, banco, código e telas, tudo conforme o
   padrão, fluxo a fluxo. Telas e UI seguem a marca oficial
   ([supportelogistica/brand](https://github.com/supportelogistica/brand)).
5. **Entrega para teste** — app rodando + roteiro de teste em linguagem
   acessível. O aceite funcional é do solicitante; depois do OK dele, o próximo
   passo é a `/supplog-check`.

---

## Regras de condução da entrevista

1. **Uma pergunta por vez.** Faça a pergunta, **espere a resposta**, só então
   avance. Evite várias perguntas no mesmo turno — isso prejudica o ritmo da
   entrevista.
2. **Linguagem clara, sem jargão.** Nunca pergunte "vai acessar o banco pelo
   front?". Pergunte a **intenção** ("Seu app precisa guardar informações entre um
   acesso e outro? Por exemplo, um cadastro que permanece salvo?"). **Você**
   traduz a resposta em decisão técnica — o solicitante não decide nada técnico.
3. **Explique o porquê em 1-2 frases.** Quando um tema tiver regra do padrão,
   contextualize antes/depois de perguntar ("Toda vez que um app guarda dado de
   pessoa — nome, CPF, e-mail — a LGPD entra. Por isso preciso saber...").
4. **Recomende uma resposta** em cada pergunta quando fizer sentido, mas a
   decisão final é do solicitante.
5. **Postura consultiva, não fiscal.** Documente e **sinalize** conflitos com o
   padrão ("este ponto tende a ser recusado na promoção para produção
   porque..."), mas **não reprove nem bloqueie** o projeto. Aprovar/reprovar é do
   TI (etapa de crivo, fora desta skill).
6. **Nunca peça ao solicitante calcular stack ou porte.** Isso é **derivado
   automaticamente** por você a partir das respostas (ver "Classificação
   automática").

---

## Fase 1 — Roteiro da entrevista

Cubra os blocos abaixo, sempre uma pergunta por vez, em português. Adapte a ordem
se a conversa pedir, mas **não pule** nenhum bloco.

### Bloco 1 — Problema e escopo

- Qual problema essa aplicação resolve? (o "porquê" antes do "como")
- Quem vai usar? (área/setor, perfil de usuário)
- **Já existe algo parecido na empresa?** (só para o solicitante refletir — o TI
  pode recusar por redundância em qualquer etapa; não é você quem decide isso.)

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
  LGPD, seção 4.6 do padrão).
- **De onde vêm os dados?** Lembre a regra: a **única fonte permitida** de dados
  da empresa é o **DW**. Registre quais fontes do DW serão consumidas.

### Bloco 4 — Integrações

- Precisa conversar com outro sistema? (mesmo que a resposta seja "não sei",
  registre.)

### Bloco 5 — Escala

- Quantas pessoas usariam ao mesmo tempo, mais ou menos? (mesmo aproximado — isso
  ajuda a classificar o porte.)

### Bloco 6 — Segurança (em linguagem de intenção)

- O app é **só interno** (rede/colaboradores) ou também **externo** (fora da rede
  ou usuários que não são colaboradores)? → Ambos seguem a **PO-SI-0016** e as
  seções 4.1–4.6. Se **externo**, registre também a seção **4.7** (MFA, TLS,
  headers, rate limit, CSRF etc.).
- Acesso aberto a qualquer pessoa, ou **somente usuários autorizados**? → Se tem
  back-end e tela, explique que o login é pelo **Login Único da Supporte** (SSO): a pessoa
  entra com a conta Microsoft da empresa uma vez e o app já a reconhece — o app
  **não tem tela de senha própria**, não guarda senha nem cadastra usuário (seção
  4.4 do padrão). Isso vale desde a homologação. Automação sem tela e página
  estática pública não têm login.
- **Quem pode entrar:** só colaboradores da Supporte, só pessoas de fora
  (clientes, parceiros) ou ambos? → Registre; esse valor vai no cadastro da
  aplicação no SSO ("Quem pode entrar") e define o modo de proteção.
- **Tem alguma parte aberta** (uma página inicial que qualquer um vê antes de
  entrar) ou **tudo exige login**? → Tudo exige → modo **bloqueio total**; tem
  parte aberta → modo **botão** "Entrar com Supplog SSO" (seção 4.4).
- **Todo mundo pode fazer tudo**, ou existem papéis diferentes (ex.: quem
  cadastra × quem só consulta × administrador)? → Se houver papéis **ou** dado
  pessoal/sensível, registre necessidade de **perfis/RBAC** (seção 4.3) — o SSO
  decide quem entra; o que cada um pode fazer é do app.
- Vai **receber informação digitada** pelo usuário (formulários, uploads)? →
  Registre que toda entrada será **validada no back-end** e que upload valida tipo
  real e tamanho (seção 4.1).

### Bloco 7 — Identidade

- Qual o nome da aplicação? Quem é o **responsável** (área, contato)?

---

## Fase 2 — Classificação automática (você calcula, o solicitante não)

A partir das respostas, **derive a stack e o porte** e informe ao solicitante em
linguagem acessível.

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
> Coding (v1.2).

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

- Escopo: <interno | externo (seção 4.7)>
- Acesso: <público / restrito por login>
- Login Único (SSO, seção 4.4): <bloqueio total | botão (tem área pública) | não
  se aplica (automação / estático sem login)>
- Quem pode entrar (cadastro no SSO): <internos | externos | ambos>
- Cadastro da aplicação no SSO: <client_id sugerido; status: a cadastrar /
  aguardando aprovação / aprovada>
- Perfis/RBAC: <sim (quais papéis) / não — obrigatório quando houver papéis
  distintos ou dado pessoal/sensível>
- Entradas de usuário: <formulários, uploads — ou "nenhuma">

## Classificação automática

- **Tipo:** <Automação | Estático | Back-end pequeno | Back-end médio/grande>
- **Stack:** <Python | HTML/CSS/JS | Python+Flask+Tailwind | Node+Nitro+React> (versão LTS)
- **Justificativa do porte:** <critérios que levaram à escolha>

## Regras do padrão que se aplicam a este projeto

<liste apenas as seções relevantes ao caso — ex.: se tem banco, cite 3.1–3.7
(scripts_criacao.sql, seed, SQL puro parametrizado, CriadoEm/AtualizadoEm) e 5.1
(SQLite em homologação); se tem login, cite 4.3–4.4 (Login Único) e 5.4; se tem
dado pessoal, cite 4.5–4.6; se for uso externo, cite 4.7; sempre cite 2.4 (nomenclatura), 2.5
(pastas) e 2.6 (arquitetura) da stack escolhida; se houver interface, cite 2.8
(marca / brand)>

## Pontos de atenção / conflitos com o padrão

<qualquer coisa que o solicitante quer fazer mas colide com o padrão — sinalizado,
não bloqueado>

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

## Autenticação (Login Único)

<!-- TODO: preencher se houver login — modo (bloqueio total / botão), quem pode
entrar, client_id (NUNCA o client_secret), Redirect URIs cadastradas por
ambiente, variáveis SSO_* esperadas no .env. Se não houver login, escrever
"Não se aplica" e o motivo. -->

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

Apresente ao solicitante, **em linguagem acessível**, o resumo do plano: o que
vai ser construído, em qual stack (e por quê, em uma frase), quais regras do
padrão vão valer, e quais pontos de atenção existem. Faça **uma única pergunta**:
se ele confirma o plano para a construção começar. Ajustes que ele pedir aqui são
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
   vez. A cada fluxo concluído, informe ao solicitante, em linguagem acessível, o
   que ficou pronto ("o cadastro de ocorrências já salva e lista — faltam o
   relatório e a tela de busca"). Evite jargão nos status.
4. **Arquitetura obrigatória** (seção 2.6): rotas finas; regra de negócio em
   services; acesso a banco isolado e **sempre em SQL puro parametrizado** (sem
   ORM, sem query builder); em React, componentes funcionais, custom hooks e
   chamadas de API em `services/`; middleware para autenticação/validação quando
   houver API.
5. **Nomenclatura** (seção 2.4) em tudo; comentários de código em português.
6. **Segurança desde o início** (seções 4.1–4.2 e 4.5; fonte PO-SI-0016):
   validação rigorosa de toda entrada no back-end; mitigação OWASP Top 10 (sem
   injeção, XSS, BOLA); SQL parametrizado; escape de output; erros sem stack
   trace; logs/auditoria de ações sensíveis; upload com tipo real e tamanho;
   certificados fora de pastas públicas; nenhum segredo hardcoded;
   `.env.example` versionado; deps sugeridas por IA conferidas; dados sensíveis
   criptografados em trânsito/repouso; massa de produção anonimizada em
   homologação. App **externo** → seção **4.7** também.
7. **Acesso e login pelo Login Único** (seções 4.3–4.4 e 5.4) — toda aplicação
   com back-end e tela nasce integrada ao SSO da Supporte, já na homologação.
   **Nunca construa tela de login com senha, tabela de senhas nem cadastro de
   usuário próprio** (login próprio só como exceção autorizada, 4.4.1).
   - **Peça ao solicitante o cadastro no SSO** (só ele pode fazer, leva 2 minutos, sem
     chamado): supplog.com → menu do usuário → **Minhas aplicações** → **Nova
     aplicação**. Entregue a ele, prontos para copiar: nome; `client_id`
     sugerido (minúsculas, números e hífen); tipo de cliente **confidencial**;
     "Quem pode entrar" conforme a entrevista; e as **Redirect URIs** exatas de
     desenvolvimento — `http://localhost:<porta>/entrar/callback` e
     `http://localhost:<porta>/`. Avise: o `client_secret` aparece **uma única
     vez** e deve ir **direto para o `.env`** — **nunca colar no chat**, em
     print ou no README. A aplicação fica **Aguardando aprovação**; o
     desenvolvimento continua e o teste do login depende da aprovação.
   - **Implemente com biblioteca OIDC da stack**, nunca protocolo/criptografia à
     mão: Flask → **Authlib** (OAuth client com `server_metadata_url` do
     discovery, `code_challenge_method='S256'`, `client_secret_basic`);
     Node/Nitro → **`openid-client`** ou porte da implementação de referência
     (`supportelogistica/docs`: `server/routes/entrar/`, `server/utils/sso/`,
     `server/middleware/somente-interno.ts`) validando JWT com **`jose`**.
   - Rotas `/entrar`, `/entrar/callback` e `/entrar/sair`; middleware de
     **bloqueio total** (ou **botão oficial** "Entrar com Supplog SSO" quando há
     área pública); sessão em cookie `httpOnly`/`SameSite=Lax`/`Secure` em
     produção; renovação com refresh token e `invalid_grant` → volta a `/entrar`;
     `sub` como chave do usuário (nunca e-mail); `tipo == INTERNO` validado no
     servidor quando só internos entram; logout que encerra a sessão **no SSO**.
     Detalhes e checklist completos na seção **4.4**.
   - Variáveis `SSO_ISSUER`, `SSO_CLIENT_ID`, `SSO_CLIENT_SECRET`, `SSO_APP_URL`
     (+ chave da sessão) no `.env`, com as mesmas chaves **sem valor** no
     `.env.example`.
   - **Autorização é do app** (4.3): tabela `USUARIO` chaveada por `SsoSub`,
     perfis/RBAC em tabelas próprias quando houver papéis ou dado sensível,
     conexão com o banco sob privilégio mínimo. Contas nominais são garantidas
     pelo SSO — proibido criar usuário genérico/compartilhado no app.
   - Preencha a seção **Autenticação (Login Único)** do README (sem o secret).
8. **Higiene LGPD** (seção 4.6) quando houver dado pessoal: nada de dado pessoal
   em app estático, em URL, em log ou no seed; colete só o que os fluxos
   justificam; fonte de dado da empresa é só o DW.
9. **Marca / interface (obrigatório quando houver tela)** — seção 2.8. **Antes**
   de construir qualquer UI, consulte o repositório oficial
   [supportelogistica/brand](https://github.com/supportelogistica/brand). Leia
   nesta ordem: `README.md` → `AGENTS.md` → `DESIGN.md`. Use somente assets e
   tokens oficiais (logo, favicon, fontes, grafismos, `tokens/`). Não redesenhe
   a marca nem invente cores/tipografia fora do que o brand define. Automações
   sem interface estão isentas.
10. **Escopo fechado.** Não invente fluxos que não foram levantados. Se a
    construção exigir uma decisão técnica nova (ex.: tabela extra), **decida você**
    conforme o padrão e registre em "Adendos da construção" no `PLANEJAMENTO.md`.
11. **README de verdade.** Ao final, resolva **todos** os TODOs do README:
    versão exata da stack, como rodar (incluindo o comando do seed), endpoints
    com método/rota/tabelas/request/response/erros, estrutura de dados. Histórico
    ganha a entrada inicial. Status permanece **Em desenvolvimento**.

### Critério de conclusão (DoD técnico)

A construção só está concluída quando **tudo** abaixo for verdade:

- [ ] Todos os fluxos do `PLANEJAMENTO.md` implementados.
- [ ] Estrutura de pastas e arquitetura conforme seções 2.5 e 2.6 (faça uma
      auto-revisão rápida antes de entregar).
- [ ] Interface (quando houver) alinhada à seção 2.8: brand consultado
      (`AGENTS.md` + `DESIGN.md`), tokens/fontes/logo/favicon oficiais, zero
      emojis, ícones Lucide.
- [ ] Se há login: **Login Único** conforme 4.4 — rotas `/entrar`,
      `/entrar/callback`, `/entrar/sair`; PKCE S256; `state`+`nonce`; `id_token`
      validado via JWKS (`iss`, `aud`, `exp`, `nonce`); middleware de bloqueio
      total ou botão oficial; `sub` como chave; logout no SSO; `SSO_*` no
      `.env.example`; **nenhuma** tela/tabela de senha própria; README com a
      seção Autenticação preenchida (sem secret).
- [ ] Se app **externo**: controles da seção 4.7 (TLS 1.2+, rate limit,
      headers, CSRF, sem painéis/debug públicos).
- [ ] Banco criado **pelos scripts** (`scripts_criacao.sql`) e seed executando sem
      erro (quando houver banco).
- [ ] App sobe localmente seguindo **exatamente** as instruções do README (teste
      você mesmo as instruções antes de entregar).
- [ ] Nenhum TODO pendente no README.

---

## Fase 5 — Entrega para o teste do usuário

O aceite funcional **é do solicitante, não seu**. Ao entregar:

1. **Explique o que foi construído** em linguagem acessível, fluxo a fluxo.
2. **Oriente a execução local**: passo a passo literal (quais comandos usar, qual
   endereço abrir no navegador), sem pressupor conhecimento técnico.
3. **Entregue um roteiro de teste**: para cada fluxo do `PLANEJAMENTO.md`, o que
   testar e o que deve acontecer ("cadastre uma ocorrência de teste; ela deve
   aparecer na lista; tente salvar sem preencher o campo X — deve dar aviso").
   Se há login, o **primeiro item** é o Login Único: abrir o app deve levar à
   tela do SSO ("Entrar com a Microsoft") e voltar já logado; "Sair" deve
   encerrar a sessão também no SSO. Lembre que esse teste **só funciona depois
   que o administrador do SSO aprovar a aplicação** — até lá, o SSO recusa o
   login (`invalid_client`); acompanhe o status em "Minhas aplicações".
4. **Indique o próximo passo**: quando todos os fluxos estiverem OK para ele, o
   próximo passo é rodar a `/supplog-check` (auditoria de conformidade); depois do
   relatório limpo, o handoff ao TI com a `/supplog-handoff`.
5. **Relembre os pontos de atenção** registrados no `PLANEJAMENTO.md` (conflitos
   com o padrão sinalizados na entrevista) — eles vão pesar no crivo do TI.

Se o solicitante encontrar problema no teste, corrija dentro desta mesma missão:
o ciclo construir → testar → ajustar continua até o aceite funcional.

---

## Padrões de Desenvolvimento & Vibe Coding (v1.2)

> **Documento canônico** — fonte única de verdade dos Padrões de Desenvolvimento da
> Supporte Logística (Supplog) para aplicações criadas com apoio de IA.
> Mora neste `SKILL.md` da `supplog-iniciar`. Ao alterar os padrões aqui, atualize
> as referências embutidas em `supplog-check` e `supplog-handoff`.

### Controle de versão

| Versão | Data       | Autor              | Revisor                                        |
| ------ | ---------- | ------------------ | ---------------------------------------------- |
| 1.0    | 22/07/2026 | Edu Ferreira       | Henrique Fernandes                             |
| 1.1    | 23/07/2026 | Jhonatan Magalhães | _pendente — Edu Ferreira / Henrique Fernandes_ |
| 1.2    | 14/09/2026 | Ludmilla Quirino   | _pendente_                                     |

**Mudanças da v1.2 sobre a v1.1:**

1. **Login Único (SSO) disponível.** A seção **4.4** deixa de descrever login
   próprio e passa a definir a integração obrigatória com o Login Único da
   Supporte (OpenID Connect em `https://supplog.com`), conforme a trilha oficial
   [Login Único](https://docs.supplog.com/vibe-coding/login-unico/o-que-e-o-login-unico).
   As regras de senha viram **4.4.1**, admitidas só como exceção autorizada.
2. **Seção 5.4 reescrita:** o SSO é integrado **desde a homologação** (o cadastro
   aceita `localhost`); em produção, aplicação aprovada no SSO com Redirect URIs
   de produção cadastradas.
3. **Seção 2.7:** README ganha a seção "Autenticação (Login Único)".
4. **Seção 4.7:** MFA do acesso humano passa a ser coberto pelo Login Único
   (conta Microsoft); a aplicação não implementa MFA próprio.
5. Glossário: termos do SSO (OIDC, PKCE, `client_id`/`client_secret`, Redirect
   URI, `id_token`, `sub`).

**Mudanças da v1.1 sobre o PDF v1.0** _(pendentes de ratificação pelos autores do PDF)_:

1. Conversão do PDF para markdown versionado (este arquivo).
2. **Seção 4.3 (senha):** mínimo elevado de 8 para **14 caracteres**, com política
   corporativa completa (categorias, histórico, rotação). O PDF v1.0 está
   desatualizado neste ponto.
3. **Nova seção 2.6 — Padrões de Arquitetura de Código** (a seção "README
   Obrigatório" foi renumerada de 2.6 para 2.7).
4. **Nova seção 4.4 — Dados Pessoais (Higiene LGPD).**
5. **Nova seção 2.8 — Identidade visual (marca Supporte / brand):** interfaces
   devem consultar e aplicar
   [supportelogistica/brand](https://github.com/supportelogistica/brand).
6. **Seção 4 reestruturada (segurança vigente, 29/07/2026):** 4.1–4.5 alinhadas
   à política atual (OWASP/BOLA, RBAC, login/senha, criptografia/anonimização);
   higiene LGPD renumerada de 4.4 para **4.6**.
7. **PO-SI-0016 mínimo útil (30/07/2026):** reframe (política vale para todos);
   senhas com bloqueio de dicionário/padrões e rotação NIST/90d; nova seção
   **4.7** com controles obrigatórios para apps externos.

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
- Visual e tokens (Tailwind): seção **2.8** (brand).

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
- Visual e tokens: seção **2.8** (brand).

**Estático:**

- JS puro, sem framework e sem etapa de build; separação de HTML, CSS e JS
  conforme 2.5.3.
- Visual e tokens: seção **2.8** (brand).

#### 2.7 README Obrigatório

Todo projeto deve conter um `README.md` completo, mesmo que extenso, contendo
obrigatoriamente:

- Descrição da aplicação
- Contexto / Motivação
- Responsável (área, contato, data de criação)
- Stack técnica, incluindo versão exata (ex.: `Python 3.12.4`) e classificação de
  porte
- Como rodar localmente (incluindo comando de execução do seed)
- Autenticação (Login Único), quando houver login: modo (bloqueio total /
  botão), quem pode entrar, `client_id`, Redirect URIs cadastradas por ambiente
  e variáveis `SSO_*` esperadas — **nunca** o `client_secret`. Sem login:
  "Não se aplica" e o motivo
- Fluxos principais e endpoints, incluindo: método e rota, tabelas utilizadas
  (leitura/escrita), request, response e possíveis erros
- Estrutura de dados (tabelas principais e finalidade)
- Fontes de dados do DW consumidas
- Dependências externas
- Status (Em desenvolvimento / Em teste / Aguardando aprovação / Em produção)
- Histórico de alterações

#### 2.8 Identidade visual (marca Supporte / brand)

Toda aplicação **com interface** (estática, Flask+Tailwind ou Node/Nitro+React)
deve seguir a identidade visual oficial da Supporte. A fonte de verdade é o
repositório:

**https://github.com/supportelogistica/brand**

**Obrigatório na construção de telas:**

1. **Antes de desenhar ou codificar UI**, leia neste repositório, nesta ordem:
   - `README.md` — mapa de assets e por onde começar
   - `AGENTS.md` — regras de uso de logo, favicon, grafismos, ícones e checklist
   - `DESIGN.md` — tokens, cores, tipografia, componentes e diretrizes de UI
2. **Importe os tokens e fontes oficiais** do brand (não invente valores):
   - CSS: `tokens/supporte.css` + `fontes/fontes.css`
   - Tailwind v4: `tokens/tailwind.css`; Tailwind v3: `tokens/tailwind.preset.js`
3. **Use assets oficiais** de `logo/`, `favicon/`, `simbolo/` e `grafismos/` —
   copie do repositório brand para o projeto. **Proibido** redesenhar, recortar,
   recolorir ou reconstruir a marca.
4. **Regras rápidas (detalhe completo no brand):**
   - Cores base: Chumbo `#58595B`, Laranja `#F37021`, Branco `#FFFFFF` (laranja
     só como destaque)
   - Tipografia: Titillium Web (títulos), Fira Sans (texto/UI), Fira Code (só
     código)
   - Ícones: família [Lucide](https://lucide.dev/); logos de terceiros via
     Iconify/Simple Icons
   - **Emojis proibidos** em qualquer interface ou documento da marca
   - Favicon: regra comum vs. kit por ambiente (produção / homolog / localhost)
     conforme `AGENTS.md`
5. **Automações sem tela** (Python puro) estão **isentas** desta seção.

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

Os requisitos das seções **4.1 a 4.6** são a tradução operacional, para o
desenvolvimento assistido por IA (Vibe Coding), da **PO-SI-0016 – Política de
Desenvolvimento Seguro** (GED/FLUIG) e são
obrigatórios para **todas** as aplicações (uso interno e externo). Aplicações de
uso **externo** devem cumprir, além disso, a seção **4.7**. Estes itens cobrem a
camada de aplicação; infraestrutura e rede são de outra área.

A PO-SI-0016 completa (governança, sanções, ferramentas homologadas, ROPA/RIPD
detalhado etc.) permanece a fonte normativa no FLUIG — aqui ficam só os
requisitos acionáveis na construção do app.

#### 4.1 Segurança em Desenvolvimento

- **Validação de entradas:** toda entrada de dados (formulário, query string,
  upload) deve ter tamanho, tipo, sintaxe e regras de negócio validados
  rigorosamente no back-end antes de ser processada ou armazenada — nunca somente
  no front-end.
- **Prevenção de vulnerabilidades (OWASP Top 10):** o desenvolvimento deve
  mitigar as vulnerabilidades do OWASP Top 10; é expressamente proibida a
  publicação de códigos com falhas de injeção (SQL, NoSQL, comandos), XSS
  (Cross-Site Scripting) e BOLA (Broken Object Level Authorization).
- **Consultas a banco de dados:** uso obrigatório de consultas
  parametrizadas/prepared statements. É estritamente proibida a construção de
  consultas com concatenação direta de variáveis na instrução SQL.
- **Prevenção de XSS:** todo output renderizado em templates/HTML deve ser
  escapado corretamente (o padrão do Jinja2 e do React já ajuda, mas não deve ser
  desativado).
- **Tratamento de erros:** garantir o tratamento adequado de erros e exceções. É
  terminantemente proibida a exibição de mensagens de erro detalhadas (stack
  traces) ao usuário final.
- **Logs e auditoria:** implementar o registro contínuo de eventos de segurança e
  trilhas de auditoria para ações sensíveis (sucesso/falha de login, mudanças de
  privilégios). Não é permitido gravar dados confidenciais ou senhas em texto
  claro nos arquivos de log.
- **Upload de arquivos:** quando aplicável, validar o tipo real do arquivo (não
  confiar apenas na extensão do nome) e limitar rigorosamente o tamanho máximo
  aceito.
- **Gestão de sessão:** a ação de logout deve invalidar a sessão de forma
  definitiva no servidor (back-end), não se limitando a redirecionar a tela no
  front-end. Com o Login Único (4.4), o logout também encerra a sessão no SSO.
- **Timeout de sessão:** aplicações internas devem possuir expiração automática
  por inatividade — entre **15 e 30 minutos** (risco baixo/moderado) ou entre
  **2 e 5 minutos** (aplicações de alto risco com dados sensíveis e
  confidenciais). Com o Login Único, aplica-se à sessão local do app (cookie);
  a pessoa volta pelo SSO sem redigitar senha se a sessão central estiver viva.
- **Certificados digitais:** arquivos de certificados (`.pfx`, `.pem`, `.crt`,
  `.enc`) não devem ser armazenados em diretórios públicos da aplicação.

#### 4.2 Variáveis de Ambiente e Segredos

- **Proteção do `.env`:** arquivos com variáveis de ambiente e credenciais são
  estritamente confidenciais, nunca devem ser commitados e devem constar
  obrigatoriamente no `.gitignore`.
- **Arquivo de exemplo:** criar e versionar um arquivo de modelo (`.env.example`)
  preenchido exclusivamente com valores fictícios, didáticos ou vazios.
- **Sem hardcode:** é terminantemente proibida a inserção de senhas, chaves de
  API ou tokens embutidos diretamente no código-fonte. O autor da aplicação deve
  remover chaves geradas incorretamente por IA.
- **Carregamento dinâmico:** credenciais e variáveis devem ser carregadas
  dinamicamente em tempo de execução (`python-dotenv` no Flask; `process.env` no
  Node).
- **Segredos fora de artefatos:** nenhum segredo real em README, comentário de
  código ou mensagem de commit.
- **Validação de IA:** dependências sugeridas por IA devem ter sua existência,
  autoria e reputação confirmadas manualmente. É proibido o uso de pacotes
  obsoletos, rastreadores não homologados ou bibliotecas sem manutenção.

#### 4.3 Autenticação e Controle de Acessos

- **Autenticação individual:** todo acesso é nominal e individual. Com o Login
  Único (4.4) isso vem da conta corporativa Microsoft de cada pessoa; é
  expressamente proibida a utilização de contas genéricas ou compartilhadas — e
  proibido criar no app qualquer usuário "coringa" que contorne o SSO.
- **Segregação (RBAC):** implementar segregação de acessos baseada em grupos ou
  perfis, garantindo que funcionalidades sensíveis sejam operáveis exclusivamente
  por pessoas autorizadas quando houver uso de dados pessoais, sensíveis e
  confidenciais — ou quando o projeto tiver papéis distintos. O SSO decide
  **quem entra**; perfis, bloqueio por usuário e permissão por tela/ação/dado
  ficam **no banco da própria aplicação**, chaveados pelo claim `sub` do SSO
  (nunca pelo e-mail, que pode mudar).
- **Privilégio mínimo (banco de dados):** as conexões do sistema com o banco de
  dados devem operar estritamente com os privilégios mínimos necessários, sendo
  proibido o uso de credenciais de administrador ou root.

#### 4.4 Autenticação — Login Único (SSO)

O **Login Único** (Single Sign-On) é o serviço de autenticação da Supporte,
disponível em `https://supplog.com` e baseado em **OpenID Connect** (o mesmo
padrão de Google e Microsoft). A pessoa entra com a conta corporativa Microsoft
na primeira aplicação; nas demais, o login é automático. Documentação oficial:
trilha [Login Único](https://docs.supplog.com/vibe-coding/login-unico/o-que-e-o-login-unico)
(páginas "O que é", "Cadastrando sua aplicação" e "Integrando no seu projeto");
guia técnico em `supportelogistica/supplog`, `docs/sso/06-guia-integracao.md`.

**Uso obrigatório** em toda aplicação nova com login — interna ou externa,
desde a homologação (5.4). A aplicação **não tem tela de login própria, não
armazena senha e não gerencia usuários**: ela redireciona a pessoa para o SSO e
recebe a identidade validada. O SSO cuida de senha, bloqueio por tentativas,
sessão central, logout e desativação (conta Microsoft desativada = perde acesso
a todas as aplicações). Automação sem tela e aplicação estática pública não têm
login e ficam fora desta seção.

**Fronteira de responsabilidade:**

- **O SSO decide quem entra.** No cadastro define-se se entram só usuários
  internos (colaboradores), só externos ou ambos; o SSO barra quem não
  corresponde antes de chegar ao app.
- **A aplicação decide o que a pessoa pode fazer** (4.3): perfis, bloqueio por
  usuário e permissão por tela/ação/dado ficam no banco do próprio app,
  chaveados pelo claim **`sub`** (identificador estável) — **nunca pelo e-mail**.

**Cadastro da aplicação** (feito pelo responsável, sem chamado, em
supplog.com → menu do usuário → **Minhas aplicações** → **Nova aplicação**):

- **Nome** exibido na tela de login e **`client_id`** (minúsculas, números e
  hífen; não muda depois).
- **Tipo de cliente:** **confidencial** quando há back-end (Flask, Node/Nitro);
  público só para front sem servidor. PKCE é exigido para ambos.
- **Quem pode entrar:** internos / externos / ambos.
- **Redirect URIs**, comparadas **byte a byte** (barra final, esquema e porta
  contam): por ambiente, `<SSO_APP_URL>/entrar/callback` (callback) e
  `<SSO_APP_URL>/` (retorno pós-logout), incluindo desenvolvimento
  (`http://localhost:<porta>/...`). Fora de `localhost`, só `https`.
- O **`client_secret` aparece uma única vez**: vai direto para o `.env`. Nunca em
  repositório, chat, print, README ou commit. Se perder ou suspeitar de
  vazamento, gere outro na página da aplicação (o anterior para na hora).
- A aplicação nasce **Aguardando aprovação** de um administrador do SSO; até lá
  o login é recusado (`invalid_client`) — o desenvolvimento continua. Alterar
  Redirect URI de aplicação aprovada reenvia o cadastro para aprovação.

**Dados de configuração:**

| Dado                           | Valor                                                                                                              |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| Issuer                         | `https://supplog.com`                                                                                              |
| Discovery                      | `https://supplog.com/.well-known/openid-configuration` — **todos** os endpoints saem daqui; proibido hardcode de path |
| Scopes                         | `openid profile email`                                                                                             |
| Fluxo                          | Authorization Code + **PKCE (S256)**, obrigatório mesmo com client secret                                          |
| Autenticação no token endpoint | `client_secret_basic` (confidencial) ou `none` (público)                                                           |
| Claims (no `id_token`)         | `sub`, `name`, `full_name`, `email`, `email_verified`, `tipo` (`INTERNO`/`EXTERNO`), `department`, `job_title`, `picture`. **Não existe endpoint userinfo.** |
| Vida dos tokens                | `access_token` JWT RS256, 10 min; `id_token` 1 h; `refresh_token` opaco e **rotativo**                             |

**Variáveis de ambiente** (no `.env`; as mesmas chaves, sem valor, no
`.env.example`; carregadas em tempo de execução — 4.2):

```bash
SSO_ISSUER=https://supplog.com
SSO_CLIENT_ID=meu-app
SSO_CLIENT_SECRET=SEU_SECRET_AQUI
# Origem pública do app. Usada na redirect_uri (/entrar/callback) e no retorno pós-logout (/).
SSO_APP_URL=http://localhost:3000
```

Em produção, `SSO_APP_URL` é o endereço real (`https://meuapp.supplog.com`) e
precisa corresponder a uma Redirect URI cadastrada. Some-se a chave da sessão
(`SECRET_KEY` no Flask; `SSO_COOKIE_CHAVE` no Nitro, base64 de 32 bytes), também
fora do repositório.

**Dois modos de chamar o login** (mesmo fluxo OIDC; muda o gatilho):

- **Bloqueio total** — padrão para sistema 100% interno, sem área pública: um
  middleware **no servidor** protege a aplicação inteira. Sem sessão → página
  HTML redireciona para `/entrar` (guardando o destino para voltar); requisição
  de API/asset sem sessão → `401`. Libere só o próprio fluxo de login, assets do
  bundle, marca e favicon.
- **Botão** — quando há conteúdo aberto, landing page ou tela de entrada
  própria: o botão oficial **"Entrar com Supplog SSO"** aponta para `/entrar`.
  Texto fixo; ícone é o símbolo PP da marca (`https://supplog.com/brand/pp-laranja.svg`
  em botão branco/fundo claro; `https://supplog.com/brand/pp-branco.svg` em botão
  laranja ou chumbo escuro). Botão laranja usa texto e ícone brancos; botão
  branco usa símbolo laranja e texto chumbo (laranja sobre branco reprova no
  WCAG AA). Cor a critério do app entre as três.

**Implementação obrigatória** — use a biblioteca OIDC da stack (Flask:
**Authlib**, OAuth client com `server_metadata_url` do discovery,
`code_challenge_method='S256'` e `client_secret_basic`; Node/Nitro:
**`openid-client`** ou porte da implementação de referência em
`supportelogistica/docs` — `server/routes/entrar/`, `server/utils/sso/`,
`server/middleware/somente-interno.ts` — validando JWT com **`jose`**).
**Proibido implementar o protocolo ou a criptografia à mão.**

- **Rotas:**
  - `/entrar` — gera `state`, `nonce` e par PKCE (`code_verifier` /
    `code_challenge`), guarda-os em cookie **cifrado, httpOnly**, de vida curta
    (≈10 min) e redireciona ao authorization endpoint com `response_type=code`,
    scopes, `state`, `nonce`, `code_challenge` e `code_challenge_method=S256`.
    Aceita `?destino=` (rota que a pessoa tentou abrir) **saneado**: só caminho
    relativo à raiz, sem `//`, sem barra invertida — anti open-redirect.
  - `/entrar/callback` — confere o `state`; trata `error` do SSO com mensagem
    genérica; troca o `code` no token endpoint com `code_verifier` e
    `redirect_uri`; valida o `id_token` via **JWKS do discovery**: assinatura,
    `iss` = issuer, `aud` = `client_id`, `exp`, `nonce`. Abre a sessão e
    redireciona ao destino saneado (ou `/`).
  - `/entrar/sair` — apaga a sessão local **e** redireciona ao
    `end_session_endpoint` com `id_token_hint` e `post_logout_redirect_uri`
    cadastrada. Logout que só apaga cookie é não-conformidade (a pessoa
    voltaria logada na próxima visita).
- **Sessão:** cookie `httpOnly`, `SameSite=Lax`, `Secure` em produção, com
  conteúdo cifrado (ou sessão server-side). Guarde `sub`, nome, e-mail, `tipo`,
  `id_token` (para o logout), `refresh_token` e o instante de expiração do
  access token. Nunca exponha tokens ao front.
- **Renovação:** o access token dura 10 min — renove com o refresh token um
  pouco antes de vencer. O refresh é **rotativo**: guarde sempre o **último**;
  o reuso de um antigo revoga a cadeia inteira. `invalid_grant` (sessão SSO
  encerrada, usuário desativado, refresh revogado) → limpe a sessão e mande a
  pessoa para `/entrar` **sem erro na tela** (será silencioso se a sessão SSO
  estiver viva). Serialize renovações concorrentes do mesmo refresh (várias
  abas/requests em paralelo). Falha de rede/5xx com token ainda válido mantém a
  sessão atual.
- **Tipo de usuário:** se a aplicação aceita só internos, valide `tipo ==
  INTERNO` também **no servidor** (`403` caso contrário). Ausência de `tipo`
  trata-se como `EXTERNO` (menor privilégio).
- **Usuário no banco:** tabela `USUARIO` com **`SsoSub`** como chave natural
  única (3.2) e colunas como `Nome`, `Email`, `Tipo`, `EstaAtivo`, `CriadoEm`,
  `AtualizadoEm`; atualize nome/e-mail a partir do `id_token` a cada login.
  Perfis e permissões (RBAC, 4.3) em tabelas próprias (ex.: `PERFIL`,
  `USUARIO_PERFIL`). **Nenhuma coluna de senha.**
- **APIs próprias:** se o front chama a API do mesmo app, a sessão (cookie)
  autentica. Se a API é consumida por terceiros com `access_token` do SSO,
  valide-o localmente via JWKS (`iss`, `aud` = seu `client_id`, `exp`).
- **Proxy reverso:** atrás de nginx, o servidor repassa `X-Forwarded-Proto` e
  `X-Forwarded-Host`; o app precisa se enxergar como `https` em produção.
- **Auditoria (4.1):** registre login, logout e falhas com `sub`/horário; nunca
  registre tokens, `client_secret` ou dado pessoal em texto puro.
- **Configuração validada no boot:** falta de qualquer `SSO_*` derruba o
  processo com a causa no log (não só um 500 no `/entrar`).

**Checklist de conformidade** (auditado pela `/supplog-check`, exigido no
handoff):

- [ ] Nenhuma página protegida é acessível sem sessão, inclusive por requisição
      direta (API/asset de conteúdo).
- [ ] `client_secret` só no `.env`; `.env` no `.gitignore`; `.env.example` com
      as chaves `SSO_*` sem valor.
- [ ] `id_token` validado com `iss`, `aud`, `exp` e `nonce`; assinatura via JWKS
      do discovery; endpoints obtidos do discovery.
- [ ] PKCE S256 e `state` em toda autorização.
- [ ] Logout encerra a sessão **no SSO** (`end_session_endpoint` +
      `id_token_hint`), não só apaga o cookie.
- [ ] `invalid_grant` no refresh redireciona a `/entrar` sem erro na tela.
- [ ] `sub` é a chave do usuário; e-mail não é chave.
- [ ] Redirect URIs de produção cadastradas **exatamente** iguais às que o app
      monta; domínio com HTTPS e headers `X-Forwarded-*` no nginx.
- [ ] Nenhuma tela, rota, tabela ou coluna de senha própria.

**Erros comuns:** `invalid_redirect_uri` → URI não cadastrada byte a byte
(barra final, `http`/`https`, porta). `invalid_client` → aplicação ainda não
aprovada, desativada ou `client_secret` errado. **Loop infinito de login** →
`SSO_APP_URL` difere do endereço do navegador (`localhost` × `127.0.0.1`,
`http` × `https`) e o cookie não casa. **Recusa só em produção com
`redirect_uri` em `http://`** → nginx sem HTTPS ou sem `X-Forwarded-Proto` /
`X-Forwarded-Host`. **"Seu usuário não tem acesso a esta aplicação"** → a pessoa
não corresponde ao "Quem pode entrar" do cadastro.

##### 4.4.1 Login próprio (exceção)

Login com usuário e senha **na própria aplicação não é permitido** para
aplicações novas. Admite-se **somente como exceção**, com autorização expressa
da Segurança da Informação/TI registrada no `PLANEJAMENTO.md` e no README (ex.:
sistema legado em adaptação ao SSO). Acesso machine-to-machine não é login
próprio: segue os mecanismos da seção 4.7. Nesses casos aplicam-se
integralmente as regras abaixo, além de 4.3:

- **Armazenamento seguro:** a senha do usuário nunca deve ser armazenada em texto
  puro — é obrigatória a aplicação de funções de hash seguras e modernas
  (**bcrypt** ou **argon2**).
- **Complexidade no back-end:** o sistema deve validar e forçar um comprimento
  mínimo de **14 caracteres**, incluindo combinação de letras maiúsculas,
  minúsculas, números **e** caracteres especiais.
- **Bloqueio de dicionário e padrões:** rejeitar senhas com palavras de
  dicionário genéricas, sequências óbvias (ex.: `123456`, `qwerty`), dados do
  próprio usuário (nome, e-mail, data de nascimento) ou termos da organização
  (ex.: `Supporte`, `Supp123`, `Logística`).
- **Histórico e senhas comprometidas:** impedir o reuso das **últimas 5** senhas.
  Quando viável, validar a nova credencial contra listas de senhas conhecidamente
  vazadas (sem expor a senha/hash completa — ex.: modelo k-anonymity).
- **Rotação (alinhada à PO-SI-0016 / NIST SP 800-63B):**
  - **Privilégios elevados** (admin, root, DBA, contas de serviço, automações,
    acesso a dados sensíveis/confidenciais): expiração obrigatória em no máximo
    **90 dias**, com aviso prévio e bloqueio até nova senha.
  - **Privilégios padrão** (usuário final sem acesso admin/sensível): dispensada
    a expiração fixa; em troca, checagem contra listas de vazamento na
    criação/alteração e de forma periódica (mínimo mensal). Indício de
    comprometimento → bloquear e forçar troca imediata.
- **Bloqueio por força bruta:** suspender temporariamente o acesso (por no mínimo
  **15 minutos**) após o limite máximo de **5** tentativas de autenticação
  inválidas consecutivas.
- **Mensagens de erro genéricas:** mensagens de login devem ser genéricas (ex.:
  "Usuário ou senha incorretos"). O processo de recuperação não deve confirmar,
  na interface, se a conta existe ou não.
- **Recuperação de credenciais:** deve ser feita exclusivamente via links com
  tokens criptográficos de uso único e tempo de expiração curto (**15 a 30
  minutos**). É expressamente proibido o envio de senhas em texto claro por
  e-mail ou SMS.

#### 4.5 Proteção de Dados

- **Criptografia:** o código-fonte deve garantir que dados confidenciais ou
  sensíveis recebam tratamento restrito, implementando criptografia adequada em
  trânsito e em repouso.
- **Anonimização:** massas de dados reais de produção não podem ser usadas em
  ambientes de desenvolvimento ou homologação sem a devida anonimização ou
  mascaramento.

#### 4.6 Dados Pessoais — Higiene LGPD

Regras de **higiene técnica** no trato de dados pessoais (nome, CPF, e-mail,
telefone, endereço etc.). Elas **não substituem avaliação jurídica** de
conformidade com a LGPD — o que se verifica aqui é a disciplina técnica mínima
esperada de qualquer aplicação que toque dado de pessoa.

1. **Inventário:** todo dado pessoal tratado deve estar listado no
   `PLANEJAMENTO.md` e/ou `README.md` (qual dado, de onde vem, para quê).
2. **Exige back-end:** proibido tratar dado pessoal em aplicação estática; o
   front-end recebe apenas o necessário para exibição.
3. **Acesso restrito:** aplicação que trata dado pessoal exige login —
   obrigatório em produção, recomendado já em homologação; aplicar RBAC (seção
   4.3) quando houver dado sensível/confidencial.
4. **Seed 100% fictício:** proibido dado pessoal real em scripts de seed
   (alinhar com anonimização da seção 4.5).
5. **Nunca em URL:** dado pessoal não trafega em query string/parâmetros de URL.
6. **Logs:** não registrar dado pessoal em texto puro (reforço da seção 4.1).
7. **Minimização:** coletar/armazenar apenas os dados que os fluxos documentados
   justificam.
8. **Fonte:** dado pessoal da empresa é consumido exclusivamente via DW (única
   fonte permitida).

#### 4.7 Aplicações de uso externo

Aplica-se quando a aplicação é acessível **fora da rede corporativa** ou por
usuários que **não** são colaboradores. Além de 4.1–4.6, é obrigatório:

- **MFA/2FA** para todo acesso humano — coberto pelo **Login Único** (4.4):
  a autenticação, inclusive o fator adicional, é do SSO/conta Microsoft; a
  aplicação **não implementa MFA próprio**. No cadastro do SSO, "Quem pode
  entrar" deve refletir o público externo (externos ou ambos). Acessos
  machine-to-machine (APIs, integrações, automações): OAuth 2.0 client
  credentials, certificados mTLS ou chaves de API **rotacionáveis**.
- **HTTPS com TLS 1.2+** em todas as rotas e APIs — HTTP em texto claro proibido.
- **Rate limiting** e proteção contra credential stuffing nos endpoints expostos.
- **Cabeçalhos de segurança HTTP:** no mínimo CSP, HSTS e X-Frame-Options.
- **CSRF:** defesas ativas com tokens criptográficos únicos e transitórios.
- **Certificados** emitidos por AC confiável — autoassinados proibidos em
  produção.
- **Superfície mínima:** só portas necessárias (ex.: 443); sem SSH, RDP ou banco
  expostos à internet; sem painéis administrativos, de infraestrutura ou de
  debug públicos.
- **Herança do interno:** contas nominais (4.3), Login Único com validação de
  `tipo` no servidor (4.4), timeout de sessão (4.1) e privilégio mínimo no banco
  (4.3) continuam obrigatórios.

> Detalhes normativos, sanções e fluxo formal de aprovação: **PO-SI-0016** e
> **PGP-SI-0015** no GED (FLUIG).

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

- **Homologação:** valores fictícios/de teste, sob responsabilidade do solicitante.
- **Produção:** o solicitante repassa os valores reais para o TI aplicar.

#### 5.4 Autenticação

- **Homologação:** a aplicação já nasce integrada ao **Login Único** (4.4) — o
  cadastro no SSO aceita Redirect URIs de `localhost`, então o login funciona
  local e em homologação com o mesmo código. Enquanto o administrador do SSO
  não aprova a aplicação, o login é recusado e o desenvolvimento segue; o teste
  do fluxo de login depende da aprovação.
- **Produção:** obrigatório **Login Único** com a aplicação **aprovada** no SSO
  e as Redirect URIs de produção (`https://<app>/entrar/callback` e
  `https://<app>/`) cadastradas byte a byte. `SSO_APP_URL` e `SSO_CLIENT_SECRET`
  reais são repassados ao TI junto com as demais variáveis (5.3), por canal
  seguro. Incluir Redirect URI nova em aplicação aprovada reenvia o cadastro
  para aprovação — faça isso antes de solicitar a subida.

> Aplicações em produção com login próprio (anteriores ao Login Único) devem ser
> adaptadas ao SSO; até a adaptação, seguem a **4.4.1**.

#### 5.5 Responsabilidade de Manutenção

- **Homologação:** responsabilidade do solicitante/área que criou a aplicação.
- **Produção:** responsabilidade passa a ser do TI.

#### 5.6 Processo de Deploy/Subida

- **Homologação:** solicitação simples, com permanência de até 10 dias úteis.
- **Produção:** sujeito ao SLA de avaliação descrito a seguir.

#### 5.7 SLA de Avaliação e Subida para Produção

1. A aplicação permanece em homologação por até **10 dias úteis**.
2. Dentro desse prazo (ou ao final dele), o solicitante pode pedir a subida para
   produção.
3. O TI tem SLA de **10 dias úteis** para avaliar desenvolvimento e segurança, e
   realizar os ajustes necessários.

- **Se aprovado:** a aplicação sobe para produção.
- **Se reprovado:** a aplicação sai da homologação; o solicitante corrige
  localmente e solicita nova homologação.
- **Se os 10 dias de homologação terminarem sem solicitação de produção:** o TI
  contata o solicitante para decidir entre avaliar ou remover. Se em até **2 dias
  úteis** não houver resposta do solicitante ou de seu gestor, a aplicação é
  **removida
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
- **BOLA (Broken Object Level Authorization):** falha em que o sistema não
  verifica se o usuário autenticado tem permissão sobre o objeto/recurso
  solicitado (ex.: acessar registro de outro usuário só mudando o id na URL).
- **RBAC (Role-Based Access Control):** controle de acesso por grupos ou perfis,
  restringindo funcionalidades sensíveis a quem está autorizado.
- **SSO (Single Sign-On) / Login Único:** serviço de autenticação centralizada
  da Supporte em `https://supplog.com` (seção 4.4). O usuário faz login uma única
  vez com a conta Microsoft e passa a ter acesso às aplicações permitidas, sem
  autenticar de novo em cada uma.
- **OIDC (OpenID Connect):** padrão aberto de autenticação sobre OAuth 2.0 usado
  pelo Login Único; a aplicação redireciona a pessoa ao provedor e recebe um
  `id_token` com a identidade validada.
- **PKCE (S256):** extensão do OAuth que protege a troca do código de
  autorização com um par `code_verifier`/`code_challenge`; obrigatório no Login
  Único, mesmo com `client_secret`.
- **`client_id` / `client_secret`:** identificador público e senha da aplicação
  cadastrada no SSO. O secret aparece uma vez no cadastro e vive só no `.env`.
- **Redirect URI:** endereço da aplicação para onde o SSO devolve a pessoa após
  o login (`/entrar/callback`) ou o logout (`/`); precisa estar cadastrado
  exatamente igual.
- **`id_token`:** JWT assinado pelo SSO com as claims da pessoa (`sub`, nome,
  e-mail, `tipo`, departamento, cargo, foto); validado via JWKS.
- **`sub`:** identificador estável do usuário no SSO — a chave do usuário no
  banco da aplicação (nunca o e-mail).
- **Endpoint:** uma rota/URL específica de uma API que executa uma ação (ex.:
  criar, listar, atualizar um registro).
- **Hash (bcrypt / argon2):** algoritmo que transforma uma senha em um valor
  irreversível, usado para armazenar senhas com segurança, sem guardar o texto
  original. Relevante apenas no login próprio de exceção (4.4.1).
- **SLA (Service Level Agreement):** prazo/acordo de nível de serviço, usado neste
  documento para definir o tempo de resposta do TI na avaliação de aplicações.
- **LGPD (Lei Geral de Proteção de Dados):** lei brasileira que disciplina o
  tratamento de dados pessoais. Neste padrão, a seção **4.6** define a **higiene
  técnica** mínima — a conformidade jurídica é avaliada fora do escopo das skills.
- **PO-SI-0016:** Política de Desenvolvimento Seguro da empresa (GED/FLUIG). As
  seções **4.1–4.7** deste padrão são a tradução operacional para o
  desenvolvimento assistido por IA (Vibe Coding);
  a política completa no FLUIG permanece a fonte normativa.
- **MFA / 2FA:** autenticação multifator — obrigatória para acesso humano em
  aplicações de uso externo (seção 4.7); com o Login Único, é provida pelo
  SSO/conta Microsoft, não pela aplicação.
