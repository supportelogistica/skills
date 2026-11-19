---
name: supplog-seguranca
description: "Autoanálise de segurança da PO-SI-0016: checa o projeto contra a política, classifica dados (LGPD/RIPD), varre dependências e gera RELATORIO-SEGURANCA.md. Use ao revisar segurança antes do handoff."
---

# /supplog-seguranca — Autoanálise de segurança (PO-SI-0016)

Você faz a **autoanálise de segurança** que a PO-SI-0016 (Política de
Desenvolvimento Seguro, rev. 01) exige do responsável pela aplicação antes de
submeter o código à avaliação — item 6.1 da política, seção 4.8 do padrão. O
**solicitante** conhece o negócio, mas **não precisa dominar segurança**: ele
precisa sair daqui sabendo, em português claro, o que a Segurança da
Informação vai encontrar, o quanto cada achado pesa e o que pedir ao Claude
para corrigir.

A **fonte de verdade** é a referência embutida no final deste arquivo: os
requisitos acionáveis da **PO-SI-0016**, da **PO-SI-0015** (Uso Responsável de
IA), da **PO-SI-0004** (Privacidade de Dados Pessoais) e da **PO-SI-0006**
(Retenção e Descarte), com a numeração dos próprios documentos — **verifique
item a item**, não de memória. As políticas completas moram no GED (FLUIG).

> **Divisão com as outras skills:** a `/supplog-check` audita o padrão
> inteiro (stack, pastas, README, dados, versionamento) e cita a segurança no
> nível do padrão. Esta skill desce à política: classificação da informação,
> checklist completa da PO-SI-0016, varredura de dependências e vulnerabilidades,
> e — se o solicitante quiser — a correção. O relatório daqui vai no pacote da
> `/supplog-handoff` e é o que a frente de Segurança da Informação confere.

---

## Regras de conduta

1. **Analise antes de mudar.** Os Passos 1–5 são somente leitura: nenhum
   arquivo do projeto muda, exceto o `RELATORIO-SEGURANCA.md` na raiz.
   Correções só no Passo 6, uma por vez, cada uma confirmada pelo solicitante.
2. **Comandos só de leitura.** Permitidos: Git de leitura (`git ls-files`,
   `git log`, `git remote -v`), `npm audit`, `npm ls`, `npm view <pacote>`,
   `pip-audit` (se já instalado), `pip list`, `pip index versions <pacote>`.
   Nada de rodar o app, o seed, instalar pacote ou alterar estado. O que
   exigir execução vira `Não verificável`, com o que falta.
3. **Segredo encontrado nunca é reproduzido.** Se achar senha, token, chave ou
   `client_secret` no código, no histórico ou em arquivo versionado, cite
   **arquivo e linha** e escreva o valor como `***`. Classifique como
   **Crítico**, avise que o segredo já conta como vazado (está no histórico)
   e que precisa ser **rotacionado** (gerado de novo) — remover do arquivo
   não basta. Nunca rotacione você mesmo.
4. **Severidade honesta.** Cada achado recebe uma severidade da escala abaixo
   e a política decide o peso: **Crítico** ou **Alto** em aberto veda o deploy
   sem aceite de risco formal (PO-SI-0016, 14.2.1). Você sinaliza; aprovar ou
   reprovar é da Segurança da Informação.
5. **Todo achado tem endereço e correção.** Item da política que fundamenta,
   arquivo (e linha) e uma instrução em linguagem acessível, pronta para o
   solicitante colar numa conversa com o Claude.
6. **Dado pessoal no projeto é tratado como tal.** Se encontrar dado pessoal
   real (seed, fixture, log, print), não o copie para o relatório: mascare e
   aponte a remoção.

**Escala de severidade:**

| Severidade   | Quando                                                                                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Crítico      | Explorável agora e expõe dado ou acesso: injeção, segredo commitado, login contornável, BOLA em dado pessoal/sensível, CVE crítica em dependência, senha em texto puro |
| Alto         | XSS, falta de validação no back-end, dado sensível sem RBAC, log de senha/dado pessoal, CVE alta, HTTP em texto claro em app externo, hospedagem externa             |
| Médio        | Timeout ausente ou fora da faixa, erro detalhado na tela, headers de segurança ausentes (externo), log sem usuário/IP, upload sem tipo real, "Lembrar-me" indevido   |
| Baixo        | Classificação de dados ou retenção não documentada, link externo/badge, dependência sem manutenção mas sem CVE, termo não registrado                                 |
| Info         | Boa prática recomendada, sem exigência da política                                                                                                                  |

Rótulos de status, sempre em texto: `Conforme`, `Não conforme`, `Parcial`,
`Não se aplica`, `Não verificável`.

---

## Processo

### Passo 1 — Reconhecimento

Explore o projeto e registre, **por evidência**:

- **Stack e tipo** (Automação / Estático / Flask / Node-Nitro / fora do
  padrão) e **escopo**: interno ou externo (acessível fora da rede ou por
  não colaboradores — decide se a seção "externo" da política se aplica).
- **Superfície de ataque:** rotas e endpoints (método, autenticação exigida),
  formulários, uploads, APIs consumidas por terceiros, integrações, webhooks,
  tarefas agendadas, e-mail/SMTP, FTP/SFTP.
- **Dados:** `PLANEJAMENTO.md`, README, `scripts_criacao.sql`, seed, models —
  quais tabelas e colunas guardam dado de pessoa (nome, CPF, e-mail,
  telefone, endereço, matrícula, saúde, biometria, foto) e dado confidencial
  (preço, contrato, rota, cliente).
- **Autenticação:** Login Único (rotas `/entrar*`, biblioteca OIDC) ou login
  próprio (`type="password"`, `bcrypt`/`argon2`, coluna `Senha`); perfis.
- **Hospedagem e deploy:** `vercel.json`, `netlify.toml`, `render.yaml`,
  `Procfile`, `fly.toml`, `firebase.json`, config de Supabase, workflow de
  deploy em `.github/workflows/`, `gh-pages`, URL de app publicada no README.
- **Nível de risco da aplicação (PO-SI-0016, 11.2.3):** **alto risco** quando
  processa dado financeiro, de RH, dado protegido pela LGPD ou acesso
  administrativo; senão, baixo/moderado. O nível decide timeout (2–5 min
  contra 15–30 min) e proíbe "Lembrar-me".

### Passo 2 — Classificação da informação e LGPD

Monte o **inventário de dados** e classifique cada item (PO-SI-0016, item 8):

| Dado | Onde (tabela/coluna, arquivo) | Categoria (pessoal / pessoal sensível / confidencial / nenhuma) | Origem (DW, digitado, integração) | Finalidade | Retenção e descarte |

Depois avalie, com base no inventário e no `PLANEJAMENTO.md`:

- **DPO e ROPA (8.1.2):** há dado pessoal? → a aplicação precisa da avaliação
  do Encarregado e do registro no ROPA. Registre "necessário" ou "não se
  aplica".
- **RIPD (8.1.3 e 8.1.4):** marque cada critério presente — (a) dado sensível
  ou de criança/adolescente; (b) 1.000 titulares ou mais em 12 meses **ou** uso
  recorrente em produção; (c) decisão automatizada sem revisão humana,
  perfilamento ou monitoramento contínuo; (d) compartilhamento com terceiro
  externo. **Dois ou mais** → "RIPD indicado". Sensível de criança/adolescente
  → sempre. Quem decide é a Segurança da Informação; você dá o enquadramento.
- **Retenção e descarte (PO-SI-0006):** cada categoria de dado pessoal tem
  prazo e forma de descarte documentados? A aplicação consegue eliminar ou
  anonimizar o dado de um titular (PO-SI-0004: pedidos respondidos em 15 dias
  úteis)? Sem prazo: `Não conforme`, Baixo; sem forma de apagar com dado
  pessoal guardado: Médio.
- **Dado real fora de lugar (8.1.10, 8.1.8):** seed/fixtures com dado pessoal
  real, dado da empresa em arquivo local, dado pessoal em log ou em URL →
  Alto.
- **Minimização:** coluna de dado pessoal que nenhum fluxo documentado usa →
  Baixo, com a sugestão de remover.

### Passo 3 — Checklist técnica (PO-SI-0016)

Verifique cada item da referência embutida que se aplica ao tipo. Procure
**evidência concreta** — busque padrões no código antes de concluir. Blocos:

- **A. Entradas e injeção (11.1.1, 11.1.2, 11.4.2):** validação no back-end
  de tamanho/tipo/sintaxe/regra para cada rota que recebe dado; SQL
  parametrizado (busque concatenação e f-strings em strings SQL, `format(`,
  `+ nome +`, template literal com `${}` em query); comando de sistema com
  entrada do usuário (`subprocess`, `os.system`, `exec`, `child_process`);
  escape de output (busque `|safe`, `Markup(`, `dangerouslySetInnerHTML`,
  `autoescape=False`, `innerHTML =`); BOLA — toda rota que recebe `id`
  confere se o registro pertence ao usuário/perfil.
- **B. Autenticação, sessão e acessos (11.1.3, 11.1.7–11.1.8, 11.2, 12):**
  Login Único conforme 4.4 do padrão (`/supplog-check` detalha; aqui confirme
  que não há contorno: rota protegida sem middleware, usuário genérico no
  seed, token fixo); login próprio só com exceção registrada e cumprindo
  11.1.7–11.1.8 e 12.1–12.5; RBAC para tela/relatório com dado restrito;
  logout invalida no servidor; timeout conforme o nível de risco;
  "Lembrar-me" (12.6); conexão com o banco sem admin/root (11.2.4).
- **C. Erros e logs (11.1.4, 11.1.5):** handler de erro genérico em produção
  (busque `debug=True`, `DEBUG = True`, stack trace renderizado); eventos
  registrados (login sucesso/falha, mudança de privilégio, acesso a dado
  sensível) **com usuário e IP**; nada de senha, token ou dado pessoal em
  texto claro no log (busque `print(` / `console.log(` de objetos de
  requisição, sessão ou usuário).
- **D. Uploads e arquivos (11.1.6, 11.4.3):** tipo real (assinatura/MIME
  lido do conteúdo, não só extensão), tamanho máximo, nome saneado, gravação
  fora de pasta pública; certificados (`.pfx`, `.pem`, `.crt`, `.enc`) fora de
  `static/`, `public/` e do repositório.
- **E. Segredos e configuração (13.1–13.5):** `.env` no `.gitignore` e fora
  do histórico (`git ls-files`, `git log --all -- .env`); `.env.example`
  completo e sem valor real; busca por segredo hardcoded (`password=`,
  `secret`, `api_key`, `token`, chaves com prefixos conhecidos — `sk-`,
  `ghp_`, `AKIA`, `xox`); nada real em README, comentário ou mensagem de
  commit (`git log --all -p -S`).
- **F. Dependências e links (9.1–9.6, 7.16):** cada dependência existe no
  registro oficial (`npm view` / `pip index versions`), tem manutenção ativa
  (última versão há menos de 2 anos ou projeto declaradamente estável) e
  licença compatível; nenhum link externo desconhecido, badge, rastreador
  (analytics, pixel) ou script de CDN não oficial no código e na
  documentação (fontes e ícones do brand são oficiais); nenhum uso de FTP,
  TFTP, Telnet, SMBv1 ou SMTP/POP3/IMAP sem TLS.
- **G. Dados e criptografia (8.1.7, 8.1.8, 11.3.2):** HTTPS/TLS 1.2+ em toda
  chamada externa (busque `http://` fora de localhost, `verify=False`,
  `rejectUnauthorized: false`); dado sensível/confidencial cifrado em
  repouso quando guardado; dado pessoal fora de URL.
- **H. Uso externo (11.3):** só se externo — MFA pelo Login Único, rate
  limiting, CSP/HSTS/X-Frame-Options, CSRF com token, certificado de AC,
  sem painel admin/debug público, portas mínimas.
- **I. Governança (6.1, 7, 15):** Termo de Ciência e Responsabilidade
  (FO-SI-0025) assinado (`Não verificável` — pergunte e registre a resposta);
  nenhum arquivo ou URL de hospedagem externa (Passo 1) — presença: **Alto**,
  Não conforme em 15.3; código só no repositório oficial (`.git/` com remote
  na organização da Supporte, `/supplog-check` detalha); ferramenta de IA
  usada com conta corporativa (PO-SI-0015 — pergunte).

### Passo 4 — Varredura de vulnerabilidades (PO-SI-0016, item 14)

- **Node:** `npm audit --json` (ou `pnpm audit`/`yarn audit` conforme o
  lockfile). **Python:** `pip-audit` se já instalado; senão, `pip list` e
  compare as versões das dependências diretas com o que você conhece de CVEs,
  marcando `Não verificável` para o que não tiver certeza e indicando o
  comando para o solicitante rodar (`pip install pip-audit && pip-audit`).
- Mapeie a severidade do scanner para a escala desta skill (critical →
  Crítico, high → Alto, moderate → Médio, low → Baixo). Vulnerabilidade
  Alta/Crítica em dependência **direta** é bloqueio de deploy (14.2.1);
  transitiva sem caminho de exploração conhecido pode ser rebaixada um grau
  com justificativa.
- Dependência que o scanner não encontra no registro = suspeita de
  **alucinação de pacote** (10.4): Crítico até ser confirmada.
- Lockfile ausente (`package-lock.json`, `requirements.txt` com versões
  fixas): Médio — sem ele a avaliação do TI não reproduz o que você testou.

### Passo 5 — Relatório

Escreva `RELATORIO-SEGURANCA.md` na raiz do projeto (sobrescreva o anterior)
e apresente no chat o resumo: contagem por severidade, os 3 itens que mais
pesam e o enquadramento LGPD.

```markdown
# Relatório de Segurança — <Nome do Projeto>

> Gerado por /supplog-seguranca em <data>. Autoanálise de segurança do
> responsável, exigida pela PO-SI-0016 (rev. 01) antes da submissão ao TI.
> Análise estática, somente leitura. Não substitui a avaliação da Segurança da
> Informação nem é parecer jurídico sobre a LGPD.

## Resumo

- Tipo/stack: <...>; escopo: <interno | externo>; nível de risco: <alto |
  baixo/moderado — por quê>
- Achados: Crítico <n> · Alto <n> · Médio <n> · Baixo <n> · Info <n>
- Não verificáveis: <n>
- **Bloqueios de deploy (PO-SI-0016, 14.2.1):** <lista dos Crítico/Alto em
  aberto — ou "nenhum">

## Classificação da informação e LGPD (PO-SI-0016, item 8)

| Dado | Onde | Categoria | Origem | Finalidade | Retenção e descarte |
| ---- | ---- | --------- | ------ | ---------- | ------------------- |

- Avaliação do DPO / registro no ROPA: <necessário — trata dado pessoal |
  não se aplica>
- RIPD: <indicado — critérios presentes: ... | não indicado (critérios: ...)>
- Direitos do titular (PO-SI-0004): <a aplicação permite localizar e
  eliminar/anonimizar o dado de uma pessoa: sim / não / não se aplica>

Bloco para o chamado de avaliação (copie para a /supplog-handoff):

> Dados tratados: <categorias>. <Trata dado pessoal — solicito avaliação do
> DPO/ROPA. | Não trata dado pessoal.> <RIPD indicado pelos critérios X e Y. |
> RIPD não indicado.>

## Achados

### Crítico

| Item da política | Achado | Evidência (arquivo:linha) | Como corrigir (cole no Claude) |
| ---------------- | ------ | ------------------------- | ------------------------------ |

### Alto

...

### Médio

...

### Baixo

...

## Checklist por bloco

| Bloco | Status | Observação |
| ----- | ------ | ---------- |
| A. Entradas e injeção | Conforme / Não conforme / Parcial / Não se aplica | ... |
| B. Autenticação, sessão e acessos | ... | ... |
| C. Erros e logs | ... | ... |
| D. Uploads e arquivos | ... | ... |
| E. Segredos e configuração | ... | ... |
| F. Dependências e links | ... | ... |
| G. Dados e criptografia | ... | ... |
| H. Uso externo | ... | ... |
| I. Governança | ... | ... |

## Varredura de dependências

- Comando: <`npm audit` / `pip-audit` / não disponível>
- Resultado: <n crítica · n alta · n moderada · n baixa — ou "sem
  vulnerabilidades conhecidas">
- Dependências não encontradas no registro: <lista ou "nenhuma">

## Não verificáveis

<o que exige execução, informação do solicitante ou a avaliação do TI, e como
confirmar>

## Declaração de autoanálise

Autoanálise de segurança realizada em <data> pelo responsável <nome>, com
apoio da skill /supplog-seguranca, conforme a PO-SI-0016 (6.1). Itens Crítico
e Alto em aberto: <n>. <Todos os bloqueios foram corrigidos | Pendências
listadas acima>.

## Próximos passos

1. Corrija primeiro os Críticos e Altos — eles vedam o deploy. Peça ao Claude
   item a item, com as instruções acima, ou peça à /supplog-seguranca para
   aplicar as correções.
2. Rode /supplog-seguranca de novo até zerar Crítico e Alto.
3. Com a /supplog-check também limpa, siga para a /supplog-handoff.
```

### Passo 6 — Correção (opcional, com confirmação)

Depois do relatório, faça **uma pergunta**: o solicitante quer que você
aplique as correções agora, começando pelos Críticos? Se sim:

1. Liste as correções na ordem de severidade e aplique **uma por vez**:
   explique em uma frase o que vai mudar, aplique, mostre o que mudou em
   linguagem acessível e faça um commit por correção
   (`fix(seguranca): <o que foi corrigido>`), com a identidade Git já
   configurada do projeto.
2. **Segredo commitado:** remova do arquivo, confira o `.gitignore` e o
   `.env.example`, e diga ao solicitante para **gerar um novo** valor na
   origem (SSO → "Minhas aplicações", provedor da chave) — o histórico do Git
   ainda contém o antigo, e reescrever histórico é decisão do TI.
3. Correção que muda comportamento visível (timeout, RBAC, validação) entra
   no roteiro de teste que você entrega ao final; o aceite é do solicitante.
4. Dependência vulnerável: atualize a versão no manifesto e no lockfile
   (`npm install <pacote>@<versão>` / editar `requirements.txt`); se a
   atualização quebra a aplicação, registre no relatório como pendência com
   a versão mínima necessária.
5. Ao terminar, **gere o relatório de novo** (Passos 3–5) para o pacote levar
   o estado real.

O que esta skill **não** faz, mesmo pedindo: rotacionar segredos, reescrever
histórico do Git, cadastrar ou alterar a aplicação no SSO, decidir se há RIPD.

### Passo 7 — Encerrar

No chat, em linguagem acessível: quantos achados por severidade, se há
bloqueio de deploy, o enquadramento LGPD (DPO/ROPA/RIPD) que vai no chamado,
e o caminho: **corrigir → rodar de novo → `/supplog-check` →
`/supplog-handoff`**. Relembre que o `RELATORIO-SEGURANCA.md` vai no `.zip` e
que o Termo de Ciência e Responsabilidade precisa estar assinado antes da
submissão.

---

## Referência embutida — requisitos acionáveis das políticas

_(Numeração dos próprios documentos do GED/FLUIG. Cite-a no relatório. A
tradução para o padrão Vibe Coding está nas seções 4.1–4.8 de
`skills/supplog-iniciar/SKILL.md`.)_

### PO-SI-0016 — Política de Desenvolvimento Seguro (rev. 01, 09/11/2026)

**6. Papéis.** 6.1 O responsável pela aplicação segue os requisitos, corrige o
apontado, usa só canais oficiais (e-mail, Teams, Fluig), **realiza autoanálise
de segurança antes de submeter** e assina o Termo de Ciência e
Responsabilidade antes de iniciar o desenvolvimento. 6.2 Sustentação valida
funcionamento e clean code. 6.3 Segurança da Informação faz a validação final,
com poder de aprovar, reprovar ou devolver.

**7. Diretrizes gerais.** 7.1 IA não transfere responsabilidade: o autor
responde pelo artefato. 7.2 Observar a PO-SI-0015; termo obrigatório com ou
sem IA. 7.3 Todo código gerado por IA é revisado por profissional antes da
validação. 7.5–7.8 Só ferramentas aprovadas; plataforma pública de IA não
homologada é proibida para código corporativo. 7.9 Separação dev /
homologação / produção. 7.10 Privilégio mínimo em tudo. 7.11 Autor sem acesso
direto nem credencial administrativa em produção. 7.12 Conta genérica ou
compartilhada vedada em qualquer ambiente (exceto conta temporária de teste
em homologação). 7.13 Dado não transita de teste para produção — só código,
scripts e pacotes validados. 7.14 Todo código no controle de versão oficial.
7.15 Aprovações e deploys só por ferramentas institucionais. 7.16 Nada sem
licença regular ou open source validada; cracks, trials expirados e uso fora
dos termos são infração grave.

**8. Classificação e proteção.** 8.1.1 Categorias: dado pessoal; dado pessoal
sensível (origem racial/étnica, religião, política, sindicato, saúde, vida
sexual, genético, biométrico, criança/adolescente); dado confidencial não
pessoal. 8.1.2 Dado pessoal → avaliação do DPO e registro no ROPA. 8.1.3 RIPD
quando **dois ou mais**: tipo (sensível ou criança/adolescente); volume (≥
1.000 titulares em 12 meses **ou** produção com uso recorrente); finalidade
(decisão automatizada sem revisão humana, perfilamento, monitoramento
contínuo); compartilhamento (terceiro externo, inclusive nuvem fora de
contrato de operador). 8.1.4 Sensível de criança/adolescente → RIPD sempre.
8.1.6 Toda informação classificada antes de ser manipulada pelo código. 8.1.7
Dado confidencial/sensível com controle de acesso e criptografia em trânsito
e em repouso. 8.1.8 Proibido armazenar, exibir ou logar dado confidencial ou
pessoal identificável em texto claro. 8.1.9 Código e documentação são
propriedade intelectual da empresa. 8.1.10 Massa real de produção em dev/homolog
só anonimizada ou mascarada. 8.1.12 Proibido publicar prints, vídeos ou
trechos de código em redes sociais, fóruns ou mensageria sem validação da SI.
8.1.13 Proibido código ou pasta de projeto em servidor de arquivos
compartilhado — só repositório oficial e ambiente controlado.

**9. Dependências, bibliotecas e links.** 9.1 Pacote sugerido por IA tem
existência, autoria e reputação confirmadas manualmente (alucinação de
pacote). 9.2 Origem confiável, repositório oficial, licença compatível. 9.3
Proibidos links externos desconhecidos, rastreadores não homologados, scripts
de terceiros e badges externos no código e na documentação. 9.4 Verificação
contínua de vulnerabilidades conhecidas. 9.5 Proibido componente obsoleto,
descontinuado ou sem manutenção, mesmo recomendado pela IA. 9.6 Proibidos FTP,
TFTP, Telnet, SMBv1 e SMTP/POP3/IMAP sem criptografia — usar SFTP/FTPS, SSH e
e-mail com TLS.

**10. Revisão do código gerado por IA.** 10.1 Revisado por Sustentação e SI
antes de produção. 10.3 O responsável valida entradas, erros, autenticação e
proteção contra vulnerabilidades conhecidas. 10.4 Sem "alucinações": função,
lógica ou pacote inexistente/malicioso.

**11. Requisitos de segurança.**

- _11.1 Internas e externas:_ 11.1.1 validação rigorosa de toda entrada
  (tamanho, tipo, sintaxe, regra) antes de processar, exibir ou armazenar.
  11.1.2 Mitigar OWASP Top 10 e **OWASP API Security Top 10**; proibido
  publicar com injeção (SQL, NoSQL, comando), XSS ou BOLA. 11.1.3 RBAC: tela,
  relatório ou função com dado restrito só para pessoas formalmente
  autorizadas. 11.1.4 Erros tratados; proibido stack trace ao usuário. 11.1.5
  Logs de segurança e trilha de auditoria (sucesso/falha de autenticação,
  mudança de privilégio, acesso a dado sensível) **com identificação do
  usuário e endereço IP**, sem dado confidencial ou senha em texto claro.
  11.1.6 Upload: tipo real do arquivo e tamanho máximo. 11.1.7 Senha nunca
  em texto puro — bcrypt ou argon2. 11.1.8 Senha: mínimo 14 caracteres com 4
  categorias; bloqueio de dicionário, sequências, dados do usuário e termos
  da organização; sem reuso das últimas 5; checagem contra vazamentos;
  rotação 90 dias para privilégios elevados (padrão: dispensada se houver
  checagem de vazamento mensal). 11.4.1 Logout invalida a sessão no servidor.
  11.4.2 Consulta a banco só parametrizada — concatenação proibida. 11.4.3
  Certificados fora de diretórios públicos e de servidores de arquivos.
- _11.2 Só internas:_ 11.2.1 Acesso nominal por usuário (SSO não é
  mandatório pela política; **é** pelo padrão Supplog, 4.4). 11.2.2 Sem conta
  genérica ou compartilhada. 11.2.3 Timeout por inatividade: 15–30 min em
  risco baixo/moderado; **2–5 min em alto risco** (dado financeiro, RH, LGPD,
  acesso administrativo), admitida renovação por atividade com
  reautenticação em ação crítica. 11.2.4 Conexão com o banco sem admin/root.
- _11.3 Externas:_ 11.3.1 MFA para todo acesso humano (pelo SSO); M2M por
  client credentials, mTLS ou API key rotacionável. 11.3.2 HTTPS TLS 1.2+ em
  tudo. 11.3.3 Rate limiting e proteção a credential stuffing. 11.3.4 CSP,
  HSTS, X-Frame-Options e CSRF com token. 11.3.5 Certificado de AC confiável.
  11.3.6 Só portas necessárias. 11.3.7 Sem painel admin/infra/debug exposto.
  11.3.8 Herda 11.2.2–11.2.4.

**12. Políticas de acesso** (login próprio, exceção 4.4.1 do padrão). 12.1
Mensagem genérica na autenticação. 12.2 Bloqueio após 5 tentativas, mínimo 15
min. 12.3 Recuperação não confirma existência de conta. 12.4 Senha nunca
enviada em texto claro. 12.5 Redefinição só por token de uso único, 15–30
min, invalidado após uso. 12.6 "Lembrar-me": proibido em alto risco; nas
demais só com cookie Secure/HttpOnly/SameSite=Strict e token revogável
invalidado no logout ou troca de senha.

**13. Gestão de segredos.** 13.1 `.env` nunca commitado; no `.gitignore`. 13.2
`.env.example` versionado com todas as chaves e valores fictícios/vazios. 13.3
Nenhum segredo hardcoded; o autor remove chaves geradas por IA. 13.4
Carregamento em tempo de execução (`python-dotenv`, `process.env`). 13.5 Nenhum
segredo real em README, comentário ou mensagem de commit.

**14. Vulnerabilidades críticas.** 14.1 Código escaneado antes de produção
(OWASP Top 10 e API Top 10). 14.2 Zero-day e falha crítica em dependência:
prioridade máxima. 14.2.1 **Proibido deploy com vulnerabilidade Alta ou
Crítica**, salvo aceite de risco formal da Alta Gestão e da SI.

**15. Testes e homologação.** 15.1 Validação por Sustentação e SI. 15.2 Vale
também para protótipo, prova de conceito e versão de teste, mesmo com dado
fictício, quando disponibilizados fora do ambiente corporativo. 15.3 Teste e
homologação **só nos ambientes do TI**; vedadas plataformas externas de
hospedagem ou geração de aplicação (Vercel, Netlify, Render, Supabase, GitHub
Pages e similares) e contas pessoais. 15.4 Prazo de 5 a 10 dias úteis,
extensível (complexidade, equipe externa, ROPA/DPO). 15.5 Aprovação pelo
Fluig, conforme PGP-SI-0015.

**16. Sanções.** A SI pode auditar repositórios sem aviso, revogar
credenciais, paralisar deploy e **retirar do ar** aplicação com
vulnerabilidade Alta/Crítica não corrigida.

### PO-SI-0015 — Uso Responsável de IA (rev. 01)

- **8.** Licença de ferramenta de IA condicionada à assinatura do **FO-SI-0025**
  (Termo de Ciência e Responsabilidade); reassinatura a cada revisão relevante
  desta política ou da PO-SI-0016.
- **9–10.** Só ferramentas homologadas (inventário no FLUIG), com conta
  corporativa; proibidos conta pessoal, versão gratuita e e-mail externo;
  proibido conectar IA a sistema ou base corporativa sem aprovação da SI.
- **11.** Classificação para compartilhamento com IA: pública (livre); interna
  (só em ferramenta homologada); confidencial (homologada **e** mascarada);
  restrita/sensível — dado pessoal, credencial, segredo de negócio, código
  proprietário — proibida salvo exceção aprovada pela SI. Em ferramenta não
  homologada, nada disso entra.
- **12.** Mascarar nome, documento, contato e identificadores antes de usar
  IA com dado confidencial ou pessoal.
- **15.** Revisão crítica de todo conteúdo gerado; inspeção do código quanto
  a segurança, funcionamento, vulnerabilidades e licenças antes de incorporar.
- **16.** Declarar o uso de IA quando o conteúdo for substancial em entrega a
  cliente/externo, quando exigido por contrato, ou em decisão que afete
  pessoas.
- **17.** Incidente de IA (reportar à SI): dado confidencial, pessoal ou
  credencial inserido em ferramenta de IA; conteúdo sem revisão que causou
  dano; agente com ação fora do escopo; Shadow IA.
- **18.9.** Proibido publicar ou hospedar na internet aplicação, site,
  protótipo ou dado construído com IA fora do fluxo de aprovação.

### PO-SI-0004 — Privacidade de Dados Pessoais (rev. 03)

- Direitos do titular que a aplicação precisa suportar operacionalmente:
  confirmação de tratamento, acesso, correção, anonimização/bloqueio/
  eliminação, portabilidade, informação sobre compartilhamento, revogação de
  consentimento. Canal: `privacidade@supplog.com` (alternativo
  `dadospessoais@supplog.com`); resposta em até **15 dias úteis**.
- Acesso a dado pessoal limitado a quem trata; menor privilégio;
  armazenamento seguro; dado guardado só pelo tempo necessário.
- Incidente com dado pessoal: comunicação à ANPD e ao titular em até **3
  dias úteis** — qualquer suspeita vai imediatamente à Segurança da
  Informação.

### PO-SI-0006 — Retenção e Descarte de Dados (rev. 03)

Referência para documentar prazo e descarte de cada categoria de dado
pessoal guardada pela aplicação:

| Tipo de dado                          | Retenção                                            | Descarte                                 |
| ------------------------------------- | --------------------------------------------------- | ---------------------------------------- |
| Marketing (nome, telefone, e-mail)    | 5 anos após contato sem resposta, ou ao revogar     | Eliminação pela empresa ou a pedido      |
| Comercial (relação com cliente)       | Durante a relação + 10 anos                         | Eliminação pela empresa                  |
| Documentos tributários                | Indeterminado                                       | Não se aplica                            |
| RH — recrutamento e seleção           | 90 dias após reprovação, ou ao revogar              | Eliminação pela empresa ou a pedido      |
| RH — gestão de RH e carreira          | Durante o contrato + 15 anos                        | Eliminação pela empresa                  |
| Estágio (termo e atestado escolar)    | 5 anos após o término                               | Eliminação pela empresa                  |
| Segurança (nome, cargo, foto, acessos)| Durante o contrato de trabalho                      | Eliminação pela empresa                  |

Categoria fora da tabela: prazo razoável, coerente com a finalidade, e
registrado no `PLANEJAMENTO.md`. Ao fim do prazo, eliminar ou anonimizar;
processo judicial ou obrigação legal prorroga.
