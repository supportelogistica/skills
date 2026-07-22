# Instruções para agentes

Biblioteca interna de skills de Claude Code da Supplog.

## Propósito do repositório

Repositório interno da Supplog para skills reutilizáveis e autocontidas. Uma skill = um diretório + `SKILL.md`. Não são prompts soltos nem scripts genéricos — são capacidades invocáveis pelo modelo, com condições de gatilho explícitas.

Não há publicação via `npx`, skills.sh ou CLI externo: o uso é interno (clone/cópia local para `~/.claude/skills/` ou equivalente no projeto).

## Estrutura

- `skills/<nome>/SKILL.md` — plano, um nível de profundidade.
- `skills/<nome>/` pode conter arquivos de apoio (scripts, referências) ao lado do `SKILL.md`, caso a skill precise.

## Filosofia

**Um diretório por skill.** Só divida em `skills/<categoria>/<nome>/` quando a coleção crescer a ponto de uma lista plana ficar difícil de escanear — não de forma preventiva.

**O `SKILL.md` é a skill inteira.** Frontmatter (`name`, `description`, `user-invocable`, `disable-model-invocation`, `argument-hint`) mais um corpo em markdown. Sem arquivos de configuração separados.

**`description` = gatilho, não vitrine.** É ela que decide se o Claude invoca a skill automaticamente — escreva como condições de uso ("use quando X, Y, Z"), não como texto de marketing.

**Nome = o que as pessoas procuram.** Kebab-case, rico em palavras-chave, alinhado ao que alguém digitaria ao buscar ou invocar a skill no time — não um nome "de marca".

**Contexto Supplog.** As skills devem respeitar e reforçar os Padrões de Desenvolvimento Vibe Coding da Supplog (stack por porte, dados no DW, LGPD, nomenclatura em português, SQL puro parametrizado etc.). Comentários e documentação em português.

## Como adicionar uma skill

1. `mkdir skills/<nome>` — nome em kebab-case, rico em palavras-chave.
2. Escreva o `SKILL.md`: frontmatter (`name`, `description` com condições de gatilho) + corpo (workflow, regras, exemplos).
3. Teste localmente em `~/.claude/skills/<nome>` antes de mergear.
4. Commit: `feat: add <nome> skill`.

## Convenções

- Campos de frontmatter: `name`, `description`, `user-invocable`, `disable-model-invocation`, `argument-hint` (quando a skill recebe argumentos).
- Sem build, sem testes, sem CI. Apenas edições em markdown.
- Documentação e comentários em português.
