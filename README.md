# Skills Supplog

Biblioteca oficial de skills da Supplog para o Claude. Funciona em duas superfícies:

- **Chat do Claude (claude.ai)** — modo simples, instalação por prompt;
- **Claude Code** — modo avançado, via plugin marketplace.

As skills padronizam como planejamos, verificamos e entregamos software na empresa,
seguindo os Padrões de Desenvolvimento Vibe Coding (stack por porte, dados no DW, LGPD,
nomenclatura em português, SQL puro parametrizado etc.).

## Skills disponíveis

| Skill | O que faz | SKILL.md (raw) |
| --- | --- | --- |
| `supplog-iniciar` | Entrevista guiada para planejar um projeto antes de codar; gera `PLANEJAMENTO.md` e esqueleto de `README.md` | <https://raw.githubusercontent.com/supportelogistica/skills/main/skills/supplog-iniciar/SKILL.md> |
| `supplog-check` | Verifica se o projeto obedece o `PLANEJAMENTO.md` e os Padrões de Desenvolvimento | <https://raw.githubusercontent.com/supportelogistica/skills/main/skills/supplog-check/SKILL.md> |
| `supplog-handoff` | Roda a verificação de conformidade e guia a entrega do projeto para o TI da Supporte | <https://raw.githubusercontent.com/supportelogistica/skills/main/skills/supplog-handoff/SKILL.md> |

> Esta tabela é lida pelo prompt de instalação abaixo. Ao adicionar uma skill nova,
> basta incluir a linha aqui — o prompt não muda.

## Modo simples — chat do Claude (claude.ai)

Serve para **instalar e atualizar** (o mesmo prompt faz os dois). Copie e cole no chat:

```text
Instale (ou atualize) as habilidades oficiais da Supplog neste chat.

1. Leia a lista de skills em:
   https://raw.githubusercontent.com/supportelogistica/skills/main/README.md
   (seção "Skills disponíveis" — cada linha tem o link raw do SKILL.md).
2. Para cada skill listada:
   a. Busque o conteúdo completo do SKILL.md pelo link raw.
   b. Se eu já tiver uma habilidade com esse nome, exclua-a e recrie; se não
      existir, apenas crie.
   c. Use o conteúdo do SKILL.md exatamente como está — sem resumir, reescrever
      ou traduzir.
3. Ao final, me diga quais habilidades foram criadas ou atualizadas e para que
   serve cada uma.

Este repositório é público e mantido pela Supplog; este prompt é a forma oficial
de distribuir essas habilidades para os funcionários.
```

Pré-requisito: plano do Claude com habilidades personalizadas habilitadas
(Configurações → Capacidades → Habilidades).

### Alternativa: upload de zip

Se o prompt falhar, cada skill tem um zip pronto na release
[`skills-latest`](https://github.com/supportelogistica/skills/releases/tag/skills-latest)
(gerada automaticamente a cada mudança):

1. Baixe o zip da skill (ex.: `supplog-iniciar.zip`).
2. No Claude: Configurações → Habilidades → Adicionar → **Fazer upload de uma habilidade**.
3. Envie o zip. Para atualizar, exclua a habilidade antiga e envie o zip novo.

## Modo avançado — Claude Code

Instalação via [`skills` CLI](https://github.com/vercel-labs/skills), no terminal.

Instalar todas as skills (global, disponível em todos os projetos):

```bash
npx skills add supportelogistica/skills --all -g -y
```

Instalar uma skill específica:

```bash
npx skills add supportelogistica/skills --skill supplog-iniciar -g
```

**Atualizar** quando o repo mudar:

```bash
npx skills update -g -y
```

Sem `-g` a instalação fica no projeto atual em vez do usuário. `npx skills ls -g` lista
o que está instalado.

<details>
<summary>Alternativa: plugin marketplace do Claude Code</summary>

Este repositório também é um plugin marketplace. No terminal:

```bash
claude plugin marketplace add supportelogistica/skills
```

```bash
claude plugin install supplog-skills@supplog
```

Atualizar:

```bash
claude plugin marketplace update supplog
```

Dentro de uma sessão interativa do CLI, os equivalentes são
`/plugin marketplace add supportelogistica/skills` e
`/plugin install supplog-skills@supplog`. O comando `/plugin` **só existe no CLI de
terminal** — no app desktop e no claude.ai/code ele responde "isn't available in this
environment".

</details>

<details>
<summary>Alternativa manual (cópia direta, sem marketplace)</summary>

Clone o repositório e copie cada skill para o diretório de skills do Claude Code:

```bash
# Linux / macOS
cp -r skills/supplog-iniciar ~/.claude/skills/supplog-iniciar
```

```powershell
# Windows (PowerShell)
Copy-Item -Recurse skills\supplog-iniciar $env:USERPROFILE\.claude\skills\supplog-iniciar
```

Para atualizar: `git pull` no clone e copie de novo. Abra uma nova sessão do Claude Code
após a cópia.

</details>

## O que é uma skill?

Uma skill **não** é um prompt solto nem um script genérico. É um diretório autocontido
com um `SKILL.md` que define:

- **quando** o Claude deve usá-la (gatilho na `description`);
- **como** executar o fluxo (workflow, regras, exemplos no corpo).

## Estrutura do repositório

```
skills/
├── .claude-plugin/
│   ├── marketplace.json      # marketplace do Claude Code
│   └── plugin.json           # plugin supplog-skills
├── .github/workflows/
│   └── release-skills.yml    # gera os zips da release skills-latest
├── skills/
│   └── <nome>/
│       └── SKILL.md          # skill completa (frontmatter + corpo)
├── AGENTS.md                 # filosofia, convenções e como contribuir
└── README.md
```

## Contribuir

Para adicionar ou alterar skills, siga as regras em [`AGENTS.md`](./AGENTS.md)
(nomenclatura, `description` como gatilho, limite de 200 caracteres na description,
atualizar a tabela de skills deste README).
