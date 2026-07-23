# Instruções para agentes

Biblioteca de skills da Supplog para Claude (chat do claude.ai) e Claude Code.

## Propósito do repositório

Repositório da Supplog para skills reutilizáveis e autocontidas. Uma skill = um
diretório + `SKILL.md`. Não são prompts soltos nem scripts genéricos — são capacidades
invocáveis pelo modelo, com condições de gatilho explícitas.

## Distribuição (dois modos)

- **Modo simples (chat do claude.ai):** prompt único no `README.md` que lê a tabela
  "Skills disponíveis" e cria/atualiza as habilidades a partir dos links raw de cada
  `SKILL.md`. Fallback: zips por skill na release `skills-latest`, gerados
  automaticamente pela action `.github/workflows/release-skills.yml`.
- **Modo avançado (Claude Code):** o repositório é um plugin marketplace
  (`.claude-plugin/marketplace.json` + `plugin.json`). Instalação via
  `/plugin marketplace add supportelogistica/skills` e atualização via
  `/plugin marketplace update supplog`.

## Estrutura

- `skills/<nome>/SKILL.md` — plano, um nível de profundidade.
- `skills/<nome>/` pode conter arquivos de apoio (scripts, referências) ao lado do
  `SKILL.md`, caso a skill precise.
- `.claude-plugin/` — manifests do marketplace do Claude Code.
- `.github/workflows/release-skills.yml` — gera os zips da release `skills-latest`.

## Filosofia

**Um diretório por skill.** Só divida em `skills/<categoria>/<nome>/` quando a coleção
crescer a ponto de uma lista plana ficar difícil de escanear — não de forma preventiva.

**O `SKILL.md` é a skill inteira.** Frontmatter (`name`, `description`,
`user-invocable`, `disable-model-invocation`, `argument-hint`) mais um corpo em
markdown. Sem arquivos de configuração separados.

**`description` = gatilho, não vitrine.** É ela que decide se o Claude invoca a skill
automaticamente — escreva como condições de uso ("use quando X, Y, Z"), não como texto
de marketing. **Máximo de 200 caracteres** (limite do claude.ai para habilidades
personalizadas).

**Toda skill funciona nas duas superfícies.** O corpo deve incluir uma seção de
detecção de ambiente (chat × Claude Code) sempre que o comportamento depender de acesso
a arquivos: no Claude Code a skill lê/escreve arquivos do projeto; no chat ela pede os
arquivos ao usuário e entrega resultados como arquivos para download. Não dependa de
scripts empacotados, `allowed-tools` ou `context: fork` — esses recursos só existem no
Claude Code.

**Nome = o que as pessoas procuram.** Kebab-case, rico em palavras-chave, alinhado ao
que alguém digitaria ao buscar ou invocar a skill no time — não um nome "de marca". O
nome do diretório deve ser idêntico ao `name` do frontmatter.

**Contexto Supplog.** As skills devem respeitar e reforçar os Padrões de
Desenvolvimento Vibe Coding da Supplog (stack por porte, dados no DW, LGPD,
nomenclatura em português, SQL puro parametrizado etc.). Comentários e documentação em
português.

## Como adicionar uma skill

1. `mkdir skills/<nome>` — nome em kebab-case, rico em palavras-chave.
2. Escreva o `SKILL.md`: frontmatter (`name`, `description` ≤ 200 caracteres com
   condições de gatilho) + corpo (workflow, regras, exemplos, detecção de ambiente).
3. **Adicione a linha na tabela "Skills disponíveis" do `README.md`** com o link raw do
   `SKILL.md` — é essa tabela que o prompt de instalação do chat lê.
4. Teste localmente em `~/.claude/skills/<nome>` antes de mergear.
5. Commit: `feat: add <nome> skill`. O push na `main` regenera os zips da release
   `skills-latest` automaticamente.

## Convenções

- Campos de frontmatter: `name`, `description`, `user-invocable`,
  `disable-model-invocation`, `argument-hint` (quando a skill recebe argumentos).
- Sem build, sem testes, sem CI além da action de release. Apenas edições em markdown
  e nos manifests.
- Documentação e comentários em português.
