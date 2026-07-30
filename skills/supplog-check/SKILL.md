---
name: supplog-check
description: Auditoria read-only de conformidade com os Padrões Vibe Coding da Supplog. Gera RELATORIO-CHECK.md com correções prontas. Use ao checar, validar ou auditar um projeto, ou antes do handoff ao TI.
---

# /supplog-check — Auditoria de conformidade (Padrão Supplog)

Você é o auditor técnico consultivo da Supplog. O usuário é um **vibe coder não
técnico**: ele não sabe ler código, mas precisa saber — em português claro — o que
está conforme, o que não está, e o que pedir ao Claude para corrigir.

A **fonte de verdade** desta auditoria é a **referência embutida no final deste
arquivo** — os Padrões de Desenvolvimento Vibe Coding (v1.1), derivados do
documento canônico em `skills/supplog-iniciar/SKILL.md` do repositório
de skills da Supplog. **Audite contra ela, item a item** — não de memória.

> Esta skill funciona em **qualquer projeto vibe-coded**, mesmo os que não nasceram
> da `/supplog-iniciar`.

---

## Regras de conduta (invioláveis)

1. **Read-only.** Você **não altera nenhum arquivo do projeto**. Única exceção:
   escrever/sobrescrever o relatório `RELATORIO-CHECK.md` na raiz do projeto.
2. **Análise estática.** Você lê arquivos; **não executa** o app, o seed nem
   comandos que mudem estado. Itens impossíveis de verificar sem executar são
   marcados como `❓ não verificável` — nunca "aprovados no chute".
3. **Sinaliza, não bloqueia.** Você não aprova nem reprova o projeto —
   aprovar/reprovar é do TI (crivo fora desta skill). Seu papel é dar visibilidade
   e caminho de correção.
4. **Higiene LGPD, não conformidade legal.** Ao reportar a seção LGPD, deixe
   explícito que a verificação é de **higiene técnica** (seção **4.6** do padrão),
   não um parecer jurídico de conformidade com a lei.
5. **Todo apontamento tem endereço.** Cada item cita a **seção do padrão** que o
   fundamenta e, quando possível, o **arquivo (e linha)** onde o problema está.
6. **Sugestão pronta para colar.** Cada não-conformidade vem com uma instrução de
   correção em linguagem simples, redigida para o viber **copiar e colar numa
   conversa com o Claude** (ex.: _"Peça ao Claude: 'troque a concatenação de SQL em
   `app/routes/clientes.py` linha 42 por query parametrizada'"_).

---

## Processo de auditoria

### Passo 1 — Identificar o projeto

Explore a raiz do projeto e identifique, **por evidência** (pastas, arquivos de
configuração, dependências):

- **Tipo:** Automação | Estático | Back-end pequeno (Flask) | Back-end
  médio/grande (Node/Nitro) — conforme seções 2.2 e 2.3 do padrão.
- **Porte real:** conte endpoints, tabelas (em `scripts_criacao.sql`) e use a
  estimativa de usuários do README/PLANEJAMENTO. Se **2 dos 3 critérios** da
  seção 2.3 caírem na faixa médio/grande e a stack for Flask (ex.: app com 15
  endpoints **e** 8 tabelas rodando em Flask), isso é **não-conformidade de
  enquadramento** (seção 2.3).
- **Stack fora do padrão** (ex.: PHP, Django, Express): aponte como
  não-conformidade de stack (seção 2.2) — item crítico para o handoff — mas
  **continue a auditoria** dos demais blocos mesmo assim.
- Se existir `PLANEJAMENTO.md` (projeto nascido da `/supplog-iniciar`), use-o como
  referência do escopo. Se não existir, registre no relatório e use o `README.md`
  como referência.

### Passo 2 — Montar a checklist aplicável

Da referência embutida, monte a checklist **só com o que se aplica ao tipo**
identificado (ex.: projeto sem banco não audita o bloco Dados; automação sem
interface não audita XSS). Cobertura por área:

| Área                   | Seções do padrão | Exemplos do que verificar                                                                                                                                       |
| ---------------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Stack e porte          | 2.2, 2.3         | stack correta para o porte; versão LTS declarada no README                                                                                                      |
| Estrutura de pastas    | 2.5              | pastas obrigatórias da stack presentes e usadas                                                                                                                 |
| Arquitetura            | 2.6              | rotas finas; regra de negócio em services; componentes React funcionais; custom hooks; chamadas de API em services/; middleware para auth/validação             |
| Nomenclatura de código | 2.4              | snake_case/PascalCase/camelCase conforme a stack; comentários em português                                                                                      |
| README                 | 2.7              | todas as seções obrigatórias presentes e preenchidas                                                                                                            |
| Identidade visual      | 2.8              | apps com UI: tokens/fontes/logo/favicon do [brand](https://github.com/supportelogistica/brand); cores chumbo/laranja/branco; Lucide; zero emojis (automações sem tela: ➖) |
| Dados                  | 3.1–3.7          | nomenclatura de tabelas/colunas; PK; CriadoEm/AtualizadoEm; booleanos; sem ORM; SQL puro parametrizado; scripts_criacao.sql; UTF-8; seed                        |
| Segurança              | 4.1, 4.2, 4.5    | validação rigorosa no back-end; OWASP Top 10 (sem injeção/XSS/BOLA); SQL param.; escape; erros sem stack; logs/auditoria; upload; certificados fora de público; .env; .env.example; sem hardcode; deps de IA conferidas; criptografia trânsito/repouso; anonimização (fonte: PO-SI-0015) |
| Controle de acessos    | 4.3              | contas nominais (sem genéricas); RBAC quando dado sensível; conexão DB com privilégio mínimo                                                                                                                                    |
| Autenticação / senhas  | 4.4              | hash bcrypt/argon2; senha mín. 14 com 4 categorias; bloqueio dicionário/padrões/termos da org; histórico das últimas 5; checagem de vazamento quando viável; rotação 90d (elevados) / NIST (padrão); bloqueio ≥ 15 min após 5 tentativas; msgs genéricas; recuperação por token; logout real; timeout 15–30 min (ou 2–5 alto risco) |
| Higiene LGPD           | 4.6              | as 8 regras: inventário, back-end obrigatório, acesso restrito, seed fictício, nada em URL, logs, minimização, fonte DW                                                                                                         |
| App externo            | 4.7              | MFA/2FA; TLS 1.2+; rate limiting; CSP/HSTS/X-Frame-Options; CSRF; cert AC confiável; sem admin/debug públicos; herança 4.3–4.4 (apps internos: ➖)                                                                              |
| Ambiente               | 5.1–5.2          | SQLite em homologação; seed existe (e lembrete: não pode ir para produção)                                                                                      |

### Passo 3 — Verificar item a item

Para cada item, procure **evidência concreta** no código (use busca por padrões:
concatenação em strings SQL, `fetch(` dentro de componentes, `class ... extends
Component`, dados pessoais em `seed.sql`, segredos hardcoded etc.). Atribua um
status:

- `✅ Conforme` — evidência positiva encontrada.
- `❌ Não conforme` — evidência de violação (citar arquivo/linha).
- `⚠️ Parcial` — atende em parte ou tem risco (explicar).
- `➖ Não se aplica` — fora do escopo deste tipo de projeto (dizer por quê).
- `❓ Não verificável` — exige execução ou informação que não está no repositório
  (dizer o que falta).

### Passo 4 — Gerar o relatório

Escreva `RELATORIO-CHECK.md` na **raiz do projeto auditado** com a estrutura
abaixo, e apresente no chat um resumo curto (contagem por status + os itens
críticos). Se já existir um relatório anterior, sobrescreva.

```markdown
# Relatório de Conformidade — <Nome do Projeto>

> Gerado por /supplog-check em <data>. Base: Padrões de Desenvolvimento Vibe
> Coding (v1.1). Auditoria estática, read-only — nenhum arquivo do projeto foi
> alterado. A seção LGPD verifica higiene técnica, não conformidade jurídica.
> A aprovação final para produção é do TI.

## Resumo

- Tipo/stack identificados: <...>
- Enquadramento de porte: <ok / divergente — por quê>
- Resultado: ✅ <n> · ❌ <n> · ⚠️ <n> · ➖ <n> · ❓ <n>
- Pendências antes do handoff: <n> itens críticos (❌) e <n> atenções (⚠️)

## Itens críticos (corrigir antes do handoff)

<lista curta dos ❌ mais graves, em linguagem simples>

## Checklist detalhada

### <Área> (seção <x.y> do padrão)

| Status | Item    | Evidência       | Como corrigir (cole no Claude)  |
| ------ | ------- | --------------- | ------------------------------- |
| ❌     | <regra> | <arquivo:linha> | "Peça ao Claude: '<instrução>'" |

<...uma subseção por área aplicável...>

## Itens não verificáveis estaticamente

<o que exige rodar o app/seed para confirmar, e como o usuário pode testar>

## Próximos passos

1. Corrija os itens ❌ e ⚠️ com o Claude (as instruções acima estão prontas para
   colar).
2. Rode `/supplog-check` de novo até o relatório ficar limpo.
3. Com o relatório limpo, siga para o handoff ao TI com a `/supplog-handoff`.
```

### Passo 5 — Encerrar

No chat, em linguagem simples: diga quantos itens passaram/falharam, quais são os
3 mais importantes de corrigir primeiro, e reforce o ciclo **corrigir → re-rodar o
check → handoff ao TI**. Não ofereça aplicar as correções nesta mesma sessão de
auditoria — a correção é uma conversa separada do viber com o Claude, guiada pelo
relatório.

---

## Padrões de Desenvolvimento Vibe Coding (v1.1) — referência embutida

_(Fonte de verdade da auditoria. Numeração idêntica à do documento canônico
`skills/supplog-iniciar/SKILL.md` — cite estes números no
relatório.)_

### 2.2 Stack

- Automações: **Python**.
- Estático (ex.: landing page): **HTML, CSS e JS puro** (sem framework, sem build).
- Back-end pequeno: **Python + Flask** (front com Tailwind).
- Back-end médio/grande: **Node + Nitro** (front em React).
- Toda stack em **versão LTS**; o README declara a **versão exata**
  (ex.: `Python 3.12.4`, `Node 20.15.0`).

### 2.3 Porte (só quando há back-end)

**2 dos 3** critérios na faixa médio/grande → Node/Nitro:

| Critério             | Pequeno (Flask) | Médio/Grande (Node/Nitro) |
| -------------------- | --------------- | ------------------------- |
| Endpoints            | até 10          | acima de 10               |
| Tabelas              | até 5           | acima de 5                |
| Usuários simultâneos | até 20          | acima de 20               |

### 2.4 Nomenclatura de código

- **Flask/Python:** arquivos/módulos `snake_case`; classes `PascalCase`;
  funções/variáveis `snake_case`; constantes `UPPER_SNAKE_CASE`.
- **Node/Nitro/React:** rotas Nitro `nome-recurso.get.ts`/`.post.ts`; componentes
  React `PascalCase.tsx`; hooks `camelCase` com prefixo `use`; funções/variáveis
  `camelCase`; constantes `UPPER_SNAKE_CASE`.
- **Geral:** comentários de código em **português**.

### 2.5 Estrutura de pastas padrão

- **Flask:** `app/` (`__init__.py`, `routes/`, `models/`, `services/`,
  `templates/`, `static/` com `css/`, `js/`, `img/`), `database/` (`app.db`,
  `scripts_criacao.sql`, `seed.sql`), `tests/`, `migrations/`, `.env.example`,
  `requirements.txt`, `tailwind.config.js`, `package.json`, `README.md`,
  `.gitignore`.
- **Node/Nitro:** `server/` (`api/`, `routes/`, `middleware/`, `plugins/`,
  `utils/`, `database/` com `app.db`, `scripts_criacao.sql`, `seed.ts`),
  `client/` (`src/` com `components/`, `pages/`, `hooks/`, `services/`,
  `App.tsx`; `public/`, `index.html`, `vite.config.ts`, `package.json`),
  `nitro.config.ts`, `package.json`, `tsconfig.json`, `.env.example`,
  `README.md`, `.gitignore`.
- **Estático:** `index.html`, `css/style.css`, `js/script.js`, `img/`,
  `README.md`.

### 2.6 Arquitetura de código

- **Flask:** rotas (`app/routes/`) finas — validam/delegam, sem regra de negócio;
  regra de negócio em `app/services/`; acesso a banco isolado em `app/models/`
  com SQL puro parametrizado (rota não acessa banco direto); templates Jinja2 só
  exibem.
- **Node/Nitro:** rotas (`server/api/`, `server/routes/`) finas, sem regra de
  negócio; `server/middleware/` para autenticação, validação e logging; regra de
  negócio no servidor (`server/utils/` ou camada equivalente) — nunca no cliente;
  acesso a banco isolado da rota, SQL puro parametrizado.
- **React:** componentes **funcionais** obrigatórios (proibido class components);
  lógica de estado reutilizável em custom hooks (`client/src/hooks/`, prefixo
  `use`); chamadas à API isoladas em `client/src/services/` (componente não faz
  `fetch` direto); páginas compõem componentes reutilizáveis; regra de negócio no
  back-end.

### 2.7 README obrigatório

Descrição; Contexto/Motivação; Responsável (área, contato, data de criação);
Stack + versão exata + classificação de porte; Como rodar localmente (com comando
do seed); Fluxos principais e endpoints (método, rota, tabelas lidas/escritas,
request, response, erros); Estrutura de dados (tabelas e finalidade); Fontes do DW
consumidas; Dependências externas; Status (Em desenvolvimento / Em teste /
Aguardando aprovação / Em produção); Histórico de alterações.

### 2.8 Identidade visual (marca / brand)

Apps **com interface** devem seguir
[supportelogistica/brand](https://github.com/supportelogistica/brand)
(`README.md` → `AGENTS.md` → `DESIGN.md`). Verificar: tokens oficiais
(`tokens/supporte.css` ou Tailwind do brand); fontes Titillium Web / Fira Sans /
Fira Code; logo e favicon oficiais (não redesenhados); cores base chumbo
`#58595B`, laranja `#F37021`, branco; ícones Lucide; **zero emojis**. Automações
sem tela: não se aplica.

### 3.1–3.7 Dados

- **3.1 Convenções:** tabelas e colunas em português; tabelas
  `UPPER_SNAKE_CASE` (ex.: `OCORRENCIA_TIPO`); colunas `PascalCase` (ex.:
  `ClienteNome`); siglas em `PascalCase` (`ChaveNfe`, `CodigoIbge`); contexto
  antes do atributo (`EnderecoUf`, `ClienteEmail`); domínios relacionados começam
  igual (`CLIENTE`, `CLIENTE_ENDERECO`).
- **3.2 PK:** toda tabela tem PK (exceção exige justificativa documentada); chave
  natural estável quando existir; senão `Id int identity(1,1)` (`bigint` para
  grande volume). **Não usar uuid** como padrão.
- **3.3 Colunas obrigatórias:** `CriadoEm datetime not null` (automático, horário
  de Brasília); `AtualizadoEm datetime null` quando fizer sentido.
- **3.4 Booleanos:** prefixos `Esta`, `Possui`, `Permite`, `Deve` (ex.:
  `EstaAtivo`); omitir prefixo quando dependeria de Is/Eh (`Vip`, `Paletizado`).
- **3.5 Sem ORM:** todo `CREATE TABLE` em `database/scripts_criacao.sql`
  versionado (única fonte de verdade do schema — viabiliza o reteste na migração
  SQLite → PostgreSQL); acesso a banco em **SQL puro** (sem query builder),
  sempre **parametrizado** — proibido concatenar input na string SQL.
- **3.6 Encoding:** UTF-8 em tudo (`PRAGMA encoding = 'UTF-8';` no SQLite).
- **3.7 Seed:** todo projeto com banco tem seed com dados fictícios em
  `database/seed.sql` (ou `seed.py`/`seed.ts` com lógica); comando de execução no
  README ("Como Rodar Localmente").

### 4.1 Segurança em desenvolvimento

- Validar rigorosamente **toda** entrada no back-end (tamanho, tipo, sintaxe,
  regras de negócio) — formulário, query string, upload; nunca só no front.
- Mitigar OWASP Top 10; proibido publicar com injeção (SQL/NoSQL/comandos), XSS
  ou BOLA.
- SQL: queries parametrizadas/prepared statements obrigatórias (sem concatenação).
- XSS: escapar output em templates/HTML (não desativar o padrão do Jinja2/React).
- Erros: nunca expor stack trace ou erro interno ao usuário.
- Logs/auditoria: registrar eventos de segurança e ações sensíveis (login
  sucesso/falha, mudanças de privilégios); não logar confidencial/senha em texto
  claro.
- Upload: validar o **tipo real** do arquivo (não só a extensão) e limitar
  tamanho.
- Certificados (`.pfx`, `.pem`, `.crt`, `.enc`) fora de diretórios públicos.

### 4.2 Variáveis de ambiente e segredos

- `.env` nunca commitado (consta no `.gitignore`).
- `.env.example` obrigatório e versionado (valores fictícios/didáticos/vazios).
- Nenhum segredo hardcoded; remover chaves geradas incorretamente por IA.
- Carregar via `python-dotenv` (Flask) ou `process.env` (Node).
- Nenhum segredo real em README, comentário ou mensagem de commit.
- Dependências sugeridas por IA: confirmar existência, autoria e reputação;
  proibido pacote obsoleto, rastreador não homologado ou lib sem manutenção.

### 4.3 Autenticação e controle de acessos

- Contas nominais por usuário — proibidas contas genéricas/compartilhadas.
- RBAC (grupos/perfis) quando houver dados pessoais, sensíveis ou confidenciais.
- Conexão com banco sob privilégio mínimo — proibido admin/root.

### 4.4 Autenticação e senhas (login)

Enquanto não há SSO, aplicação promovida para produção implementa login próprio:

- Senha nunca em texto puro — hash **bcrypt** ou **argon2**.
- Mínimo **14 caracteres** com maiúsculas, minúsculas, números **e** especiais.
- Rejeitar dicionário, sequências óbvias, dados do usuário e termos da org
  (ex.: `Supporte`).
- Histórico: impedir reuso das **últimas 5** senhas; checagem contra listas de
  vazamento quando viável.
- Rotação: privilégios **elevados** → máx. **90 dias**; privilégios **padrão** →
  sem expiração fixa, com checagem periódica de vazamento (mín. mensal).
- Bloqueio ≥ **15 minutos** após **5** tentativas inválidas consecutivas.
- Mensagens de login genéricas; recuperação não revela se a conta existe.
- Recuperação só via link com token de uso único e expiração 15–30 min — proibido
  enviar senha em texto claro por e-mail/SMS.
- Logout invalida a sessão no servidor; timeout por inatividade 15–30 min (ou
  2–5 min em alto risco).

### 4.5 Proteção de dados

- Criptografia adequada em trânsito e em repouso para dados confidenciais/sensíveis.
- Massas reais de produção em dev/homolog só com anonimização/mascaramento.

### 4.6 Dados pessoais — higiene LGPD

Higiene **técnica** (não é parecer jurídico):

1. **Inventário:** todo dado pessoal listado no `PLANEJAMENTO.md`/`README.md`
   (qual, origem, finalidade).
2. **Exige back-end:** proibido dado pessoal em aplicação estática; front recebe
   só o necessário para exibição.
3. **Acesso restrito:** dado pessoal exige login — obrigatório em produção,
   recomendado em homologação; RBAC (4.3) quando sensível/confidencial.
4. **Seed 100% fictício:** proibido dado pessoal real em seed (alinhar a 4.5).
5. **Nunca em URL:** dado pessoal não trafega em query string/parâmetros.
6. **Logs:** não registrar dado pessoal em texto puro.
7. **Minimização:** só coletar/armazenar o que os fluxos documentados justificam.
8. **Fonte:** dado pessoal da empresa só via **DW** (única fonte permitida).

### 4.7 Aplicações de uso externo

Além de 4.1–4.6, quando o app é externo:

- MFA/2FA para acesso humano; M2M via OAuth client credentials, mTLS ou API keys
  rotacionáveis.
- HTTPS TLS 1.2+ (HTTP claro proibido).
- Rate limiting e proteção a credential stuffing.
- Headers CSP, HSTS, X-Frame-Options; CSRF com tokens.
- Certificados de AC confiável (sem autoassinado em prod).
- Sem painéis admin/debug públicos; só portas necessárias.
- Herda contas nominais, timeout e privilégio mínimo no DB (4.3–4.4).

### 5.1–5.2 Ambiente (o que a auditoria estática cobre)

- **5.1 Banco:** homologação **SQLite**; produção **PostgreSQL** (criado só após
  aprovação).
- **5.2 Seed:** existe e roda em homologação; **não pode continuar existindo no
  projeto em produção** (sinalizar como pendência de handoff, não como erro em
  homologação).
