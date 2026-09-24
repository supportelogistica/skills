---
name: supplog-sso
description: Integra o Login Único (SSO) em app já existente: diagnostica o acesso atual, orienta o cadastro no SSO, implementa OIDC e aposenta o login com senha. Use ao adicionar ou migrar login para o SSO.
---

# /supplog-sso — Login Único em aplicação existente (Padrão Supplog)

Você integra o **Login Único da Supporte** (SSO, OpenID Connect em
`https://supplog.com`) em uma aplicação **que já existe** — com login próprio
de senha, com outro mecanismo de acesso ou sem login nenhum. O **solicitante**
conhece o negócio e a aplicação, mas **não precisa dominar OAuth nem engenharia
de software**: ele define quem pode entrar e o que fica aberto; **você define
todo o "como"**.

A **fonte de verdade** técnica é a referência embutida no final deste arquivo
(seções 4.3, 4.4, 4.4.1 e 5.4 dos Padrões de Desenvolvimento Vibe Coding v1.3,
derivadas do documento canônico em `skills/supplog-iniciar/SKILL.md`). A trilha
oficial do SSO está em
[Login Único](https://docs.supplog.com/vibe-coding/login-unico/o-que-e-o-login-unico)
(páginas "Cadastrando sua aplicação" e "Integrando no seu projeto"); o guia
técnico do protocolo mora em `supportelogistica/supplog`,
`docs/sso/06-guia-integracao.md`.

> **Divisão de papéis no ciclo Supplog:** aplicação **nova** já nasce com SSO
> pela `/supplog-iniciar`. Esta skill cuida da aplicação **existente**. Ao
> terminar, o caminho é o de sempre: `/supplog-check` (auditoria) e, para
> produção, `/supplog-handoff`.

---

## Resultado esperado

A missão só termina quando **tudo** abaixo for verdade:

- Abrir a aplicação leva ao SSO (bloqueio total) ou exibe o botão oficial
  "Entrar com Supplog SSO" (modo botão); voltar do SSO abre a sessão local.
- **Nenhuma** tela, rota, tabela ou coluna de senha própria restou no projeto —
  nem "esqueci minha senha", nem cadastro de usuário, nem usuário genérico.
- Usuários já existentes **preservados** e vinculados ao SSO pelo claim `sub`;
  perfis e permissões (RBAC) continuam funcionando sem alteração de regra.
- `.env.example` com as chaves `SSO_*`; README com a seção "Autenticação
  (Login Único)" preenchida (sem secret); histórico atualizado.
- Cadastro no SSO solicitado ao solicitante com os dados prontos para copiar.
- Roteiro de teste entregue, com o aviso de que o login só funciona depois da
  aprovação da aplicação no SSO.

---

## Regras de conduta

1. **Uma pergunta por vez, linguagem clara.** Pergunte a intenção ("qualquer
   pessoa que o SSO deixar entrar pode usar o sistema, ou só quem já está
   cadastrado?"), nunca o jargão. Recomende uma resposta quando fizer sentido.
2. **Cutover, não convivência.** O SSO **substitui** o login com senha; os dois
   não ficam lado a lado. Manter senha em paralelo é a exceção **4.4.1** e exige
   autorização da Segurança da Informação/TI registrada no
   `PLANEJAMENTO.md`/README. Se o solicitante insistir sem autorização,
   **sinalize** o conflito (vai pesar no crivo do TI) e siga — aprovar é do TI.
3. **Autenticação muda; autorização fica.** O SSO decide quem entra. Perfis,
   bloqueio por usuário e permissão por tela/ação/dado continuam no banco do
   app, agora chaveados por `sub`. Não reescreva regra de negócio.
4. **Biblioteca OIDC da stack, nunca protocolo à mão.** Nada de montar JWT,
   PKCE ou validação de assinatura manualmente.
5. **Segredo só no `.env`.** O `client_secret` aparece uma vez no cadastro e
   vai direto para o `.env`. Se o solicitante colar o valor no chat, avise que
   ele vazou e peça para gerar outro em "Minhas aplicações" (o anterior para
   na hora). Nunca reproduza o valor — use `SEU_SECRET_AQUI`.
6. **Destrutivo só depois de validado e confirmado.** Remover colunas/tabelas
   de senha e dependências de hash acontece em migração própria, **depois** que
   o login pelo SSO funcionou no teste e o solicitante confirmou.

---

## Processo

### Passo 1 — Diagnóstico (ler antes de mudar)

Explore o projeto e registre, **por evidência** (arquivos, dependências,
rotas), cada item abaixo. Só avance quando todos estiverem preenchidos.

**Stack e porte** (seções 2.2–2.3):

- Flask, Node/Nitro (ou Nuxt), ou **fora do padrão** (Express, Django, PHP,
  .NET…). Fora do padrão: integre mesmo assim e registre a não-conformidade de
  stack como ponto de atenção — a `/supplog-check` vai apontar.
- **Estático sem back-end:** o SSO não se aplica. Explique que proteger
  conteúdo exige servidor (sessão e segredo não podem viver no navegador) e
  encerre a skill indicando a `/supplog-iniciar` para ganhar back-end.

**Acesso hoje** — identifique qual caso (pode ser mais de um):

| Caso                        | Sinais no código                                                                                                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Sem login                   | nenhuma rota de autenticação; tudo aberto                                                                                                                                |
| Login próprio (senha)       | formulário `type="password"`; `bcrypt`/`argon2`/`werkzeug.security`/`passlib`; coluna `Senha`/`SenhaHash`/`password`; rotas `/login`, `/logout`, `/cadastro`, `/esqueci-senha`, `/redefinir`; e-mail de recuperação; tokens de reset |
| Outro provedor              | `msal`, `passport`, `google-auth`, Basic Auth, token fixo em header                                                                                                      |
| Usuário genérico            | credencial única compartilhada; `admin/admin` no seed; login sem tabela de usuários                                                                                      |
| Acesso máquina-a-máquina    | scripts, integrações ou automações autenticando com usuário/senha humano                                                                                                 |

**Como o código lê o usuário logado:** `flask_login.current_user`,
`session["usuario_id"]`, decorator `@login_required`, middleware Nitro,
`event.context.usuario`, hook React `useUsuario`… Anote **todos** os pontos de
leitura — eles definem o adaptador do Passo 4.4.

**Modelo de usuários:** tabela, chave atual (`Id`, e-mail, login), colunas de
senha, tabelas de perfil/permissão que a referenciam, seed com usuários.

**Superfície:** o que é público (landing, `/health`, webhooks, assets) e o que
é protegido; APIs consumidas por terceiros ou automações.

**Endereços:** porta local, URL de homologação, URL de produção (se já está em
produção), presença de proxy reverso (nginx).

Se existir `PLANEJAMENTO.md`/`README.md`, use-os como referência de escopo.

**Então pergunte ao solicitante**, uma por vez, apenas o que o código não
responde:

1. **Quem pode entrar:** só colaboradores da Supporte, só pessoas de fora
   (clientes, parceiros) ou ambos? → vai no cadastro do SSO e decide a
   validação de `tipo` no servidor.
2. **Tem parte aberta** antes de entrar, ou tudo exige login? → **bloqueio
   total** (recomendado para sistema 100% interno) ou **botão**.
3. **Qualquer pessoa que o SSO deixar entrar pode usar o sistema, ou só quem já
   está cadastrado?** → define o comportamento do primeiro login (Passo 4.6):
   criar o usuário automaticamente **ou** recusar quem não está na tabela.
   Recomende "só cadastrados" quando há perfis ou dado pessoal/sensível.
4. **Está em produção com usuários reais?** → define migração, comunicação aos
   usuários e coordenação com o TI (Passo 5).
5. Se há acesso máquina-a-máquina com usuário/senha: explique que isso **não
   vira login humano** — a integração passa a autenticar com `access_token` do
   SSO validado via JWKS ou chave de API rotacionável (seção 4.7). Registre
   como ponto de atenção; implemente só se o solicitante pedir nesta missão.

### Passo 2 — Plano de integração (escrever, apresentar, confirmar)

Monte o plano com: modo (bloqueio total / botão); quem pode entrar;
`client_id` sugerido (minúsculas, números e hífen, ex.: `ocorrencias-cd`);
Redirect URIs por ambiente; estratégia de vínculo dos usuários existentes;
comportamento do primeiro login; lista do que será **removido** (rotas,
templates, colunas, dependências); migrações de banco em duas etapas; pontos
de atenção (stack fora do padrão, exceção 4.4.1, M2M pendente).

- Se existir `PLANEJAMENTO.md`: atualize a seção **Segurança** (Login Único,
  quem pode entrar, cadastro no SSO, perfis) e registre o plano em **Adendos da
  construção**.
- Se não existir: apresente o plano no chat; o registro permanente será a
  seção "Autenticação (Login Único)" e o histórico do README (Passo 4.9).

Apresente em linguagem acessível e faça **uma única pergunta**: se confirma o
plano. Ajustes são bem-vindos — atualize e reconfirme.

### Passo 3 — Cadastro no SSO (feito pelo solicitante)

Só o solicitante pode cadastrar (leva dois minutos, sem chamado):
supplog.com → menu do usuário → **Minhas aplicações** → **Nova aplicação**.
Entregue um bloco pronto para copiar:

```text
Nome: <nome exibido na tela de login>
client_id: <sugerido>
Tipo de cliente: confidencial
Quem pode entrar: <internos | externos | ambos>
Redirect URIs (uma por linha, exatamente assim):
  http://localhost:<porta>/entrar/callback
  http://localhost:<porta>/
  https://<homologacao>/entrar/callback        (se já existe)
  https://<homologacao>/
  https://<producao>/entrar/callback           (se já está em produção)
  https://<producao>/
```

Cadastre **todas as URIs dos ambientes que já existem de uma vez**: alterar
Redirect URI de aplicação aprovada reenvia o cadastro para aprovação. Fora de
`localhost`, só `https`; o SSO compara byte a byte (barra final, esquema e
porta contam).

Avise: o `client_secret` aparece **uma única vez** — vai direto para o `.env`,
nunca no chat, print, README ou commit. A aplicação nasce **Aguardando
aprovação**; até a aprovação o SSO recusa o login (`invalid_client`). O
desenvolvimento continua; o teste do login depende da aprovação.

### Passo 4 — Implementar

Antes de codar, leia a página "Integrando no seu projeto" da trilha oficial e,
em Node/Nitro/Nuxt, a implementação de referência em `supportelogistica/docs`
(`server/routes/entrar/`, `server/utils/sso/`,
`server/middleware/somente-interno.ts`). Implemente na ordem abaixo.

#### 4.1 Configuração

- `.env`: `SSO_ISSUER`, `SSO_CLIENT_ID`, `SSO_CLIENT_SECRET`, `SSO_APP_URL`
  (origem pública do app, ex.: `http://localhost:<porta>`) + chave da sessão
  (`SECRET_KEY` no Flask; `SSO_COOKIE_CHAVE` no Nitro, base64 de 32 bytes).
- `.env.example`: as mesmas chaves **sem valor**. `.env` no `.gitignore`.
- **Validação no boot:** falta de qualquer `SSO_*` derruba o processo com a
  causa no log — não um 500 no `/entrar`.

#### 4.2 Biblioteca OIDC

- **Flask:** **Authlib** (`authlib.integrations.flask_client`), registrada com
  `server_metadata_url` do discovery, `code_challenge_method='S256'` e
  `token_endpoint_auth_method='client_secret_basic'`. Esqueleto:

  ```python
  # app/__init__.py (trecho) — comentários em português
  import os
  from authlib.integrations.flask_client import OAuth

  oauth = OAuth()

  def registrar_sso(app):
      # Configuração validada no boot: falta de variável derruba o processo.
      for chave in ("SSO_ISSUER", "SSO_CLIENT_ID", "SSO_CLIENT_SECRET", "SSO_APP_URL", "SECRET_KEY"):
          if not os.environ.get(chave):
              raise RuntimeError(f"Variável de ambiente obrigatória ausente: {chave}")
      oauth.init_app(app)
      oauth.register(
          name="sso",
          client_id=os.environ["SSO_CLIENT_ID"],
          client_secret=os.environ["SSO_CLIENT_SECRET"],
          # Todos os endpoints (authorize, token, jwks, logout) vêm do discovery.
          server_metadata_url=f"{os.environ['SSO_ISSUER']}/.well-known/openid-configuration",
          client_kwargs={
              "scope": "openid profile email",
              "code_challenge_method": "S256",
              "token_endpoint_auth_method": "client_secret_basic",
          },
      )
  ```

  `oauth.sso.authorize_redirect(redirect_uri)` gera `state`, `nonce` e PKCE;
  `oauth.sso.authorize_access_token()` confere o `state`, troca o `code` e
  valida o `id_token` (JWKS, `iss`, `aud`, `exp`, `nonce`), devolvendo as
  claims em `token["userinfo"]`. O `end_session_endpoint` vem de
  `oauth.sso.load_server_metadata()`.
- **Node/Nitro/Nuxt:** **`openid-client`** ou porte da implementação de
  referência, validando JWT com **`jose`**.
- **Python fora do Flask:** Authlib (tem integração para Django e Starlette).
  **Node fora do Nitro:** `openid-client`. **Outras stacks:** biblioteca OIDC
  certificada e mantida — confirme existência, autoria e reputação (4.2) antes
  de adicionar.

Prompt oficial da trilha, útil como checklist do que o código precisa cobrir:

```text
Integre este projeto ao SSO da Supporte como cliente OpenID Connect confidencial.

- Issuer https://supplog.com, discovery em /.well-known/openid-configuration.
- Authorization Code + PKCE (S256). Autenticação no token endpoint com client_secret_basic.
- Scopes openid profile email. Não existe endpoint userinfo: as claims vêm no id_token.
- Rotas: /entrar (inicia o login e guarda state, nonce e PKCE em cookie cifrado httpOnly),
  /entrar/callback (valida state, troca o code, valida o id_token via JWKS com iss, aud e nonce)
  e /entrar/sair (encerra a sessão local e redireciona ao end_session_endpoint com id_token_hint).
- Sessão em cookie httpOnly, sameSite lax, secure em produção. Access token dura 10 min:
  renove com o refresh token e trate invalid_grant mandando o usuário de volta ao /entrar.
- Use o claim sub como identificador do usuário. Nunca o e-mail.
- Bloqueie o site inteiro para quem não está logado, com um middleware no servidor.
- Configuração por variáveis de ambiente SSO_ISSUER, SSO_CLIENT_ID, SSO_CLIENT_SECRET
  e SSO_APP_URL. Nunca grave o secret no repositório.
- Use a biblioteca jose para validar o JWT. Não implemente criptografia manualmente.
```

#### 4.3 Rotas `/entrar`, `/entrar/callback`, `/entrar/sair`

- **`/entrar`** — gera `state`, `nonce` e par PKCE em cookie cifrado httpOnly
  de vida curta (≈10 min) e redireciona ao authorization endpoint. Aceita
  `?destino=` **saneado**: só caminho relativo à raiz, sem `//` nem barra
  invertida (anti open-redirect).
- **`/entrar/callback`** — confere `state`; `error` do SSO → mensagem genérica
  (sem detalhe técnico na tela); troca o `code` com `code_verifier` e
  `redirect_uri` = `<SSO_APP_URL>/entrar/callback`; valida o `id_token` via
  JWKS do discovery (`iss`, `aud` = `client_id`, `exp`, `nonce`); vincula o
  usuário (4.6); abre a sessão; redireciona ao destino saneado ou `/`.
- **`/entrar/sair`** — apaga a sessão local **e** redireciona ao
  `end_session_endpoint` com `id_token_hint` e
  `post_logout_redirect_uri=<SSO_APP_URL>/`. Logout que só apaga o cookie é
  não-conformidade.

Substitua as rotas antigas: `/login` → redireciona para `/entrar`; `/logout` →
`/entrar/sair` (assim links e favoritos antigos continuam funcionando durante
a transição). Cadastro e recuperação de senha **deixam de existir**.

#### 4.4 Sessão e o adaptador do "usuário logado"

- Cookie `httpOnly`, `SameSite=Lax`, `Secure` em produção, com conteúdo
  **cifrado ou server-side** — a sessão padrão do Flask é só assinada (legível
  pelo navegador), então guarde tokens fora dela (sessão server-side ou cookie
  cifrado). Guarde `sub`, nome, e-mail, `tipo`, `id_token` (para o logout),
  `refresh_token` e o instante de expiração do access token. Tokens **nunca**
  chegam ao front.
- **Renovação:** access token dura 10 min; renove pouco antes de vencer com o
  refresh token (**rotativo** — guarde sempre o último). `invalid_grant` →
  limpa a sessão e manda para `/entrar` **sem erro na tela**. Serialize
  renovações concorrentes; falha de rede/5xx com token ainda válido mantém a
  sessão.
- **Timeout por inatividade** da sessão local (4.1): 15–30 min; 2–5 min se o
  app trata dado sensível/confidencial.
- **Adaptador:** mantenha a interface que o resto do código já usa
  (`current_user`, `session["usuario_id"]`, `event.context.usuario`, hook…) e
  faça-a ler da nova sessão. O objetivo é que rotas, services e templates que
  só **consomem** o usuário logado não precisem mudar. Se o app usa
  `flask-login`, mantenha o `user_loader` carregando por `Id` a partir do
  `sub` da sessão; remova apenas o que dependia de senha.

#### 4.5 Proteção: bloqueio total ou botão

- **Bloqueio total** — middleware **no servidor** cobre a aplicação inteira:
  página HTML sem sessão → `/entrar?destino=<rota>`; API/asset de conteúdo sem
  sessão → `401`. Libere só `/entrar*`, assets do bundle, marca, favicon e
  rotas públicas mapeadas no Passo 1 (`/health`, webhooks — estes com a
  própria autenticação de máquina).
- **Botão** — botão oficial **"Entrar com Supplog SSO"** (texto fixo) apontando
  para `/entrar`, com o símbolo PP da marca: `https://supplog.com/brand/pp-laranja.svg`
  em botão branco; `https://supplog.com/brand/pp-branco.svg` em botão laranja
  ou chumbo escuro. Botão laranja usa texto e ícone brancos; botão branco usa
  símbolo laranja e texto chumbo. Substitui o formulário de senha na tela de
  entrada; o restante da UI segue o brand (seção 2.8).
- Só internos podem entrar → valide `tipo == INTERNO` **também no servidor**
  (`403`); ausência de `tipo` = `EXTERNO`.

#### 4.6 Vínculo dos usuários existentes (migração 1)

- **Schema:** adicione a `USUARIO` (ou equivalente) a coluna `SsoSub` única e
  nula por enquanto, e `Tipo`, `EstaAtivo`, `AtualizadoEm` se faltarem. Atualize
  `database/scripts_criacao.sql` (única fonte de verdade do schema, 3.5) e
  escreva a migração para bancos já existentes em `migrations/` (Flask) ou
  `server/database/` (Nitro), idempotente e em SQL puro.
- **Primeiro login** (no callback, com as claims validadas):
  1. Busca por `SsoSub` → achou: atualiza nome/e-mail/`tipo` e segue.
  2. Não achou: busca pelo **e-mail** — só se `email_verified` for verdadeiro,
     comparação exata sem diferenciar maiúsculas → achou: grava `SsoSub` no
     registro (vínculo único; a partir daí a chave é o `sub`) e segue.
  3. Não achou: conforme a decisão do Passo 1 — **cria** o usuário (sem perfil
     ou com o perfil mínimo) **ou** recusa com `403` e mensagem clara ("seu
     usuário ainda não foi liberado nesta aplicação; fale com <responsável>").
- **Usuários genéricos/compartilhados:** desative (`EstaAtivo = 0`) e nunca
  vincule a um `sub`. Se algum fluxo dependia dele (ex.: automação), vira o
  ponto de atenção M2M do Passo 1.
- **Higiene LGPD (4.6):** nome e e-mail são dados pessoais — não em URL, não em
  log em texto puro; o seed passa a ter usuários fictícios **sem senha**. A
  remoção dos hashes (4.7) é minimização: o app deixa de guardar o que não
  precisa mais.
- **Perfis/RBAC:** as tabelas `PERFIL`/`USUARIO_PERFIL` (ou equivalentes)
  continuam referenciando o usuário pelo `Id`; nada muda na regra de
  autorização.

#### 4.7 Aposentar o login próprio (migração 2, após o teste)

Remova, nesta ordem, **só depois** que o login pelo SSO passou no teste do
Passo 5 e o solicitante confirmou:

1. Rotas, templates, formulários e JS de login/cadastro/recuperação de senha;
   e-mails de redefinição; tokens de reset.
2. Colunas `Senha`/`SenhaHash`, tabelas de token de recuperação, colunas de
   contagem de tentativas/bloqueio — via migração 2 (`ALTER TABLE ... DROP
   COLUMN` / `DROP TABLE`) e o mesmo ajuste em `scripts_criacao.sql`.
3. Dependências que só serviam à senha (`bcrypt`, `argon2-cffi`, `passlib`,
   `flask-bcrypt`…) de `requirements.txt`/`package.json`.
4. Menções a senha em README, `.env.example` (ex.: `SENHA_ADMIN_INICIAL`),
   seed e comentários.

Confira com busca: `password`, `senha`, `bcrypt`, `argon2`, `type="password"`
não podem restar no projeto (fora de histórico/changelog).

#### 4.8 Auditoria e proxy

- Registre login, logout, `invalid_grant` e recusas (`403` por `tipo`, usuário
  não liberado) com `sub` e horário; nunca tokens, `client_secret` ou dado
  pessoal em texto puro (4.1).
- Atrás de nginx, o app precisa se enxergar como `https` em produção
  (`X-Forwarded-Proto`, `X-Forwarded-Host`); configure o framework para
  confiar nesses headers.

#### 4.9 Documentação

- README: seção **Autenticação (Login Único)** com modo, quem pode entrar,
  `client_id` (**nunca** o secret), Redirect URIs por ambiente, variáveis
  `SSO_*`, comportamento do primeiro login e o que aconteceu com o login
  antigo. Apague instruções de senha em "Como rodar localmente" (ex.: "entre
  com admin/123"). Entrada no **Histórico de alterações**.
- `PLANEJAMENTO.md` (se existir): Segurança e Adendos atualizados.
- Se a stack está fora do padrão ou houve exceção 4.4.1: registrado como
  ponto de atenção no README/PLANEJAMENTO.

### Critério de conclusão (DoD técnico)

- [ ] `SSO_*` no `.env.example` sem valor; `.env` no `.gitignore`; boot valida
      a configuração.
- [ ] Rotas `/entrar`, `/entrar/callback`, `/entrar/sair`; PKCE S256; `state` +
      `nonce`; `id_token` validado via JWKS (`iss`, `aud`, `exp`, `nonce`);
      endpoints do discovery (nenhum path hardcoded).
- [ ] Middleware de bloqueio total (ou botão oficial) — nenhuma página
      protegida acessível sem sessão, inclusive por requisição direta.
- [ ] Logout encerra a sessão **no SSO**; `invalid_grant` → `/entrar` sem erro.
- [ ] `USUARIO.SsoSub` única; vínculo por `sub` (e-mail só no primeiro login,
      verificado); genéricos desativados; RBAC intacto.
- [ ] `tipo == INTERNO` validado no servidor quando só internos entram.
- [ ] `scripts_criacao.sql` e migrações atualizados; seed sem senha; app sobe
      seguindo o README.
- [ ] Nenhuma tela, rota, tabela, coluna ou dependência de senha própria (após
      a migração 2).
- [ ] README com Autenticação (Login Único) preenchida e histórico atualizado.

### Passo 5 — Teste, cutover e produção

O aceite funcional é do solicitante. Entregue o roteiro em linguagem acessível:

1. **Login:** abrir o app leva à tela do SSO ("Entrar com a Microsoft") e volta
   já logado, na página que tentou abrir. Só funciona depois que o
   administrador do SSO **aprovar** a aplicação — até lá, `invalid_client`;
   acompanhe em "Minhas aplicações".
2. **Usuário antigo:** quem já existia entra e mantém o mesmo perfil e
   permissões de antes.
3. **Usuário novo:** entra e é criado sem perfil **ou** recebe a mensagem de
   "não liberado", conforme a decisão do Passo 1.
4. **Rota protegida direta:** abrir uma URL interna sem sessão redireciona para
   o SSO; chamada de API sem sessão devolve `401`.
5. **Sair:** encerra a sessão também no SSO — voltar ao app pede login de novo.
6. Se só internos: uma conta externa recebe `403`.

Depois do OK do solicitante, aplique a **migração 2** (4.7).

**Se a aplicação já está em produção**, o cutover exige coordenação com o TI
(seções 5.3, 5.4, 5.7):

- Redirect URIs de produção cadastradas e aplicação **Aprovada** antes da
  subida; `SSO_APP_URL` de produção precisa bater byte a byte.
- Valores reais de `SSO_*` repassados ao TI por canal seguro (nunca e-mail
  aberto ou chat público) — o TI aplica na configuração.
- **Comunicação aos usuários**, redigida por você para o solicitante enviar:
  a partir de <data>, entrar com a conta Microsoft da empresa; a senha antiga
  deixa de valer; quem não tem conta Microsoft (externos) precisa ser
  liberado no cadastro do SSO.
- A nova versão passa pelo ciclo normal: `/supplog-check` e `/supplog-handoff`.

Se o solicitante encontrar problema no teste, corrija dentro desta mesma
missão. Erros mais comuns e suas causas estão na referência embutida ("Erros
comuns").

### Passo 6 — Encerrar

No chat, em linguagem acessível: o que mudou (o app agora entra pelo SSO; o
login antigo foi aposentado; usuários preservados), o que **depende do
solicitante** (cadastro/aprovação no SSO, teste do roteiro, envio da
comunicação), e o próximo passo (`/supplog-check`; depois `/supplog-handoff`
para produção). Relembre os pontos de atenção registrados.

---

## Padrões de Desenvolvimento Vibe Coding (v1.3) — referência embutida

_(Trechos que regem a integração. Numeração idêntica à do documento canônico
`skills/supplog-iniciar/SKILL.md` — ao mudar o padrão lá, atualize aqui.)_

### 4.3 Autenticação e controle de acessos

- Contas nominais por usuário (vêm do Login Único) — proibidas contas
  genéricas/compartilhadas e qualquer usuário "coringa" no app que contorne o
  SSO.
- RBAC (grupos/perfis) quando houver dados pessoais, sensíveis ou confidenciais
  ou papéis distintos — no banco do app, chaveado pelo claim `sub` (nunca
  e-mail). O SSO decide quem entra; o app decide o que cada um faz.
- Conexão com banco sob privilégio mínimo — proibido admin/root.

### 4.4 Autenticação — Login Único (SSO)

Toda aplicação com login usa o **Login Único** da Supporte (OpenID Connect em
`https://supplog.com`), desde a homologação. O app **não tem tela de login
própria, não guarda senha e não gerencia usuários**. Automação sem tela e
estático público: não se aplica.

**Cadastro** (pelo responsável em supplog.com → Minhas aplicações → Nova
aplicação): nome; `client_id` (minúsculas, números, hífen; não muda); tipo
**confidencial** quando há back-end (público só para front sem servidor; PKCE
exigido para ambos); "Quem pode entrar" (internos/externos/ambos); Redirect
URIs byte a byte — `<SSO_APP_URL>/entrar/callback` e `<SSO_APP_URL>/` por
ambiente, inclusive `http://localhost:<porta>/...`; fora de `localhost`, só
`https`. O `client_secret` aparece uma vez → `.env`. A aplicação nasce
**Aguardando aprovação** (login recusado com `invalid_client` até lá); alterar
Redirect URI de aplicação aprovada reenvia para aprovação.

**Dados de configuração:**

| Dado                           | Valor                                                                                                     |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| Issuer                         | `https://supplog.com`                                                                                     |
| Discovery                      | `https://supplog.com/.well-known/openid-configuration` — **todos** os endpoints saem daqui; sem hardcode |
| Scopes                         | `openid profile email`                                                                                    |
| Fluxo                          | Authorization Code + **PKCE (S256)**, obrigatório mesmo com client secret                                 |
| Autenticação no token endpoint | `client_secret_basic` (confidencial) ou `none` (público)                                                  |
| Claims (no `id_token`)         | `sub`, `name`, `full_name`, `email`, `email_verified`, `tipo` (`INTERNO`/`EXTERNO`), `department`, `job_title`, `picture`. **Não existe endpoint userinfo.** |
| Vida dos tokens                | `access_token` JWT RS256, 10 min; `id_token` 1 h; `refresh_token` opaco e **rotativo**                    |

**Variáveis de ambiente** (`.env`; mesmas chaves sem valor no `.env.example`):

```bash
SSO_ISSUER=https://supplog.com
SSO_CLIENT_ID=meu-app
SSO_CLIENT_SECRET=SEU_SECRET_AQUI
# Origem pública do app. Usada na redirect_uri (/entrar/callback) e no retorno pós-logout (/).
SSO_APP_URL=http://localhost:3000
```

Em produção, `SSO_APP_URL` é o endereço real e precisa corresponder a uma
Redirect URI cadastrada. Chave da sessão (`SECRET_KEY` no Flask;
`SSO_COOKIE_CHAVE` no Nitro, base64 de 32 bytes) também fora do repositório.

**Modos:** **bloqueio total** (middleware no servidor; HTML sem sessão →
`/entrar`; API/asset sem sessão → `401`; libera só login, assets, marca,
favicon) ou **botão** oficial "Entrar com Supplog SSO" (texto fixo; símbolo PP
`pp-laranja.svg` em botão branco / `pp-branco.svg` em botão laranja ou chumbo;
laranja sobre branco reprova no WCAG AA) apontando para `/entrar`.

**Implementação obrigatória** com biblioteca OIDC da stack (Flask: Authlib com
`server_metadata_url`, `code_challenge_method='S256'`, `client_secret_basic`;
Node/Nitro: `openid-client` ou porte da referência `supportelogistica/docs` +
`jose`). Protocolo ou criptografia à mão: não conforme.

- **Rotas:** `/entrar` (gera `state`, `nonce`, PKCE em cookie cifrado httpOnly
  de vida curta; `?destino=` saneado — só caminho relativo, sem `//`),
  `/entrar/callback` (confere `state`; `error` → mensagem genérica; troca o
  `code` com `code_verifier`; valida `id_token` via JWKS: assinatura, `iss`,
  `aud` = client_id, `exp`, `nonce`; abre sessão; redireciona ao destino),
  `/entrar/sair` (limpa sessão local **e** redireciona ao
  `end_session_endpoint` com `id_token_hint` + `post_logout_redirect_uri`).
- **Sessão:** cookie `httpOnly`, `SameSite=Lax`, `Secure` em produção,
  conteúdo cifrado ou server-side; guarda `sub`, nome, e-mail, `tipo`,
  `id_token`, `refresh_token`, expiração do access token; tokens nunca no
  front.
- **Renovação:** access token de 10 min renovado com refresh **rotativo**
  (guardar sempre o último; reuso de antigo revoga a cadeia); `invalid_grant`
  → limpa sessão e volta a `/entrar` sem erro na tela; renovações concorrentes
  serializadas; falha de rede/5xx com token válido mantém a sessão.
- **Tipo de usuário:** só internos → `tipo == INTERNO` validado no servidor
  (`403`); ausência de `tipo` = `EXTERNO`.
- **Usuário no banco:** `USUARIO` com **`SsoSub`** único como chave natural
  (3.2), colunas `Nome`, `Email`, `Tipo`, `EstaAtivo`, `CriadoEm`,
  `AtualizadoEm`; nome/e-mail atualizados do `id_token` a cada login. RBAC em
  `PERFIL`/`USUARIO_PERFIL`. **Nenhuma coluna de senha.**
- **APIs próprias:** front do mesmo app → sessão (cookie). API consumida por
  terceiros com `access_token` do SSO → validar localmente via JWKS (`iss`,
  `aud` = client_id, `exp`).
- **Proxy reverso:** `X-Forwarded-Proto`/`X-Forwarded-Host`; app se enxerga
  como `https` em produção.
- **Auditoria (4.1):** login, logout e falhas com `sub`/horário; nunca tokens,
  secret ou dado pessoal em texto puro.
- **Configuração validada no boot:** falta de `SSO_*` derruba o processo com a
  causa no log.

**Checklist de conformidade** (auditado pela `/supplog-check`):

- [ ] Nenhuma página protegida acessível sem sessão, inclusive por requisição
      direta.
- [ ] `client_secret` só no `.env`; `.env` no `.gitignore`; `.env.example` com
      `SSO_*` sem valor.
- [ ] `id_token` validado com `iss`, `aud`, `exp`, `nonce`; assinatura via JWKS
      do discovery; endpoints do discovery.
- [ ] PKCE S256 e `state` em toda autorização.
- [ ] Logout encerra a sessão **no SSO**.
- [ ] `invalid_grant` no refresh → `/entrar` sem erro na tela.
- [ ] `sub` é a chave do usuário; e-mail não é chave.
- [ ] Redirect URIs de produção cadastradas exatamente iguais; HTTPS e headers
      `X-Forwarded-*` no nginx.
- [ ] Nenhuma tela, rota, tabela ou coluna de senha própria.

**Erros comuns:** `invalid_redirect_uri` → URI não cadastrada byte a byte
(barra final, `http`/`https`, porta). `invalid_client` → aplicação não
aprovada, desativada ou `client_secret` errado. **Loop infinito de login** →
`SSO_APP_URL` difere do endereço do navegador (`localhost` × `127.0.0.1`,
`http` × `https`) e o cookie não casa. **Recusa só em produção com
`redirect_uri` em `http://`** → nginx sem HTTPS ou sem `X-Forwarded-Proto` /
`X-Forwarded-Host`. **"Seu usuário não tem acesso a esta aplicação"** → a
pessoa não corresponde ao "Quem pode entrar" do cadastro.

### 4.4.1 Login próprio (exceção)

Login com senha na própria aplicação **não é permitido** em aplicação nova e
**deve ser substituído** nas existentes. Admite-se só como exceção autorizada
pela Segurança da Informação/TI, registrada no `PLANEJAMENTO.md`/README (ex.:
legado em adaptação ao SSO). Enquanto a exceção vigorar: hash bcrypt/argon2;
mínimo 14 caracteres com 4 categorias; bloqueio de dicionário/padrões/termos da
org; histórico das últimas 5; rotação 90 dias em privilégios elevados; bloqueio
≥ 15 min após 5 tentativas; mensagens genéricas; recuperação só por token de
uso único (15–30 min). Acesso máquina-a-máquina não é login próprio: segue 4.7
(client credentials, mTLS ou API keys rotacionáveis).

### 5.4 Autenticação por ambiente

- **Homologação:** integrada ao Login Único; o cadastro aceita `localhost`,
  então o mesmo código roda local e em homologação. Login recusado até a
  aprovação; o desenvolvimento segue.
- **Produção:** aplicação **Aprovada** no SSO com Redirect URIs de produção
  (`https://<app>/entrar/callback` e `https://<app>/`) cadastradas byte a byte;
  `SSO_APP_URL` e `SSO_CLIENT_SECRET` reais repassados ao TI com as demais
  variáveis (5.3), por canal seguro. Incluir URI nova em aplicação aprovada
  reenvia para aprovação — faça antes de solicitar a subida.
- Aplicações em produção com login próprio (anteriores ao Login Único) devem
  ser adaptadas ao SSO — é o trabalho desta skill; até a adaptação, seguem a
  4.4.1.
