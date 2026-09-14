# Instruções para agentes

Biblioteca de skills da Supplog para Claude Code.

## Propósito do repositório

Repositório da Supplog para skills reutilizáveis e autocontidas. Uma skill = um
diretório + `SKILL.md`. Não são prompts soltos nem scripts genéricos — são capacidades
invocáveis pelo modelo, com condições de gatilho explícitas.

## Distribuição

- **Instalação padrão (zip):** zips por skill na release mais recente
  (`releases/latest`), gerados automaticamente pela action
  `.github/workflows/release-skills.yml`, para upload em
  Configurações → Habilidades.
- **Modos avançados (terminal):** `skills` CLI
  (`npx skills add supportelogistica/skills`, atualiza com `npx skills update`);
  plugin marketplace (`.claude-plugin/marketplace.json` + `plugin.json`,
  instalável com `claude plugin marketplace add supportelogistica/skills`);
  ou cópia manual para `~/.claude/skills/`.

## Estrutura

- `skills/<nome>/SKILL.md` — plano, um nível de profundidade.
- `skills/<nome>/` pode conter arquivos de apoio (scripts, referências) ao lado do
  `SKILL.md`, caso a skill precise.
- `.claude-plugin/` — manifests do marketplace do Claude Code.
- `.github/workflows/release-skills.yml` — gera os zips e publica uma release
  versionada (`vAAAA.MM.DD-rN`) a cada mudança em `skills/`; a mais recente fica em
  `releases/latest`.

## Filosofia

**Um diretório por skill.** Só divida em `skills/<categoria>/<nome>/` quando a coleção
crescer a ponto de uma lista plana ficar difícil de escanear — não de forma preventiva.

**O `SKILL.md` é a skill inteira.** Frontmatter (`name`, `description`,
`user-invocable`, `disable-model-invocation`, `argument-hint`) mais um corpo em
markdown. Sem arquivos de configuração separados.

**`description` = gatilho, não vitrine.** É ela que decide se o Claude invoca a skill
automaticamente — escreva como condições de uso ("use quando X, Y, Z"), não como texto
de marketing. **Máximo de 200 caracteres** (limite da tela de Habilidades do Claude).

**Skills são para Claude Code.** Escreva assumindo acesso a arquivos e ao diretório de
trabalho do projeto (Read/Write/Edit, terminal). Não inclua lógica de detecção de
ambiente nem variações para o chat do claude.ai.

**Nome = o que as pessoas procuram.** Kebab-case, rico em palavras-chave, alinhado ao
que alguém digitaria ao buscar ou invocar a skill no time — não um nome "de marca". O
nome do diretório deve ser idêntico ao `name` do frontmatter.

**Contexto Supplog.** As skills devem respeitar e reforçar os Padrões de
Desenvolvimento Vibe Coding da Supplog (stack por porte, dados no DW, LGPD,
nomenclatura em português, SQL puro parametrizado, login pelo Login Único/SSO
etc.). O documento canônico dos padrões mora em `skills/supplog-iniciar/SKILL.md`;
`supplog-check` e `supplog-handoff` carregam referências embutidas com a mesma
numeração — ao mudar o padrão, atualize as três. Comentários e documentação em
português.

## Como adicionar uma skill

1. `mkdir skills/<nome>` — nome em kebab-case, rico em palavras-chave.
2. Escreva o `SKILL.md`: frontmatter (`name`, `description` ≤ 200 caracteres com
   condições de gatilho) + corpo (workflow, regras, exemplos).
3. **Adicione a linha na tabela "Skills disponíveis" do `README.md`** com o link para o
   `SKILL.md`.
4. Teste localmente em `~/.claude/skills/<nome>` antes de mergear.
5. Commit: `feat: add <nome> skill`. O push na `main` publica automaticamente uma
   release versionada nova com os zips.

## Convenções

- Campos de frontmatter: `name`, `description`, `user-invocable`,
  `disable-model-invocation`, `argument-hint` (quando a skill recebe argumentos).
- Sem build, sem testes, sem CI além da action de release. Apenas edições em markdown
  e nos manifests.
- Documentação e comentários em português.
