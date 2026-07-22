# Skills Supplog

Repositório **interno** da Supplog com skills para Claude Code.

Aqui vivem capacidades reutilizáveis que o modelo pode invocar automaticamente (ou sob demanda) para padronizar como planejamos e construímos software na empresa — alinhadas aos Padrões de Desenvolvimento Vibe Coding (stack por porte, dados no DW, LGPD, nomenclatura em português, SQL puro parametrizado etc.).

## O que é uma skill?

Uma skill **não** é um prompt solto nem um script genérico. É um diretório autocontido com um `SKILL.md` que define:

- **quando** o Claude deve usá-la (gatilho na `description`);
- **como** executar o fluxo (workflow, regras, exemplos no corpo).

## Estrutura

```
skills/
├── skills/
│   └── <nome>/
│       └── SKILL.md          # skill completa (frontmatter + corpo)
├── AGENTS.md                 # filosofia, convenções e como contribuir
└── README.md
```

Cada skill fica em `skills/<nome>/`, com um único nível de profundidade. Arquivos de apoio (scripts, referências) podem ficar ao lado do `SKILL.md` quando necessário.

## Como usar

1. Clone este repositório.
2. Copie (ou faça symlink de) cada skill para o diretório de skills do Claude Code, por exemplo:

```bash
# Linux / macOS
cp -r skills/supplog-iniciar ~/.claude/skills/supplog-iniciar

# Windows (PowerShell)
Copy-Item -Recurse skills\supplog-iniciar $env:USERPROFILE\.claude\skills\supplog-iniciar
```

3. Reinicie ou abra uma nova sessão do Claude Code — a skill passa a ficar disponível conforme o gatilho da `description`.

## Skills disponíveis

- [`supplog-iniciar`](./skills/supplog-iniciar/SKILL.md) — entrevista guiada pelos Padrões de Desenvolvimento Vibe Coding para planejar um novo projeto **antes** de escrever código. Classifica stack/porte e gera `PLANEJAMENTO.md` e um esqueleto de `README.md`. Use ao criar, iniciar ou começar do zero uma aplicação, app, automação, site ou sistema.

## Contribuir

Para adicionar ou alterar skills, siga as regras em [`AGENTS.md`](./AGENTS.md) (nomenclatura, `description` como gatilho, testes locais em `~/.claude/skills/` antes do merge).
