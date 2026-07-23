# Skills Supplog

Biblioteca oficial de skills da Supplog para Claude Code.

As skills padronizam como planejamos, verificamos e entregamos software na empresa,
seguindo os Padrões de Desenvolvimento Vibe Coding (stack por porte, dados no DW, LGPD,
nomenclatura em português, SQL puro parametrizado etc.).

## Skills disponíveis

| Skill | O que faz |
| --- | --- |
| [`supplog-iniciar`](./skills/supplog-iniciar/SKILL.md) | Entrevista guiada para planejar um projeto antes de codar; gera `PLANEJAMENTO.md` e esqueleto de `README.md` |
| [`supplog-check`](./skills/supplog-check/SKILL.md) | Verifica se o projeto obedece o `PLANEJAMENTO.md` e os Padrões de Desenvolvimento |
| [`supplog-handoff`](./skills/supplog-handoff/SKILL.md) | Roda a verificação de conformidade e guia a entrega do projeto para o TI da Supporte |

## Instalação (zip)

Cada skill tem um zip pronto na
[release mais recente](https://github.com/supportelogistica/skills/releases/latest)
(uma release versionada nova é gerada automaticamente a cada mudança nas skills):

1. Baixe o zip da skill (ex.: `supplog-iniciar.zip`).
2. No Claude: Configurações → Habilidades → Adicionar → **Fazer upload de uma habilidade**.
3. Envie o zip.

Para **atualizar**: exclua a habilidade antiga e envie o zip da release mais recente.

Pré-requisito: plano do Claude com habilidades personalizadas habilitadas
(Configurações → Capacidades → Habilidades).

## Modos avançados

Para quem trabalha no terminal. Cada opção instala as mesmas skills — escolha uma.

<details>
<summary><strong>npx skills</strong> (precisa de Node.js instalado)</summary>

Instalação via [`skills` CLI](https://github.com/vercel-labs/skills).

Instalar todas as skills (global, disponível em todos os projetos):

```bash
npx skills add supportelogistica/skills --all -g -y
```

Instalar uma skill específica:

```bash
npx skills add supportelogistica/skills --skill supplog-iniciar -g
```

Atualizar quando o repo mudar:

```bash
npx skills update -g -y
```

Sem `-g` a instalação fica no projeto atual em vez do usuário. `npx skills ls -g` lista
o que está instalado.

</details>

<details>
<summary><strong>claude plugin</strong> (precisa do Claude Code CLI)</summary>

Este repositório é um plugin marketplace do Claude Code. No terminal:

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
<summary><strong>Cópia manual</strong> (precisa de git)</summary>

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
│   └── release-skills.yml    # gera os zips e a release versionada (vAAAA.MM.DD-rN)
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
