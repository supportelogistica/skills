---
name: supplog-check
description: Verifica se o projeto obedece o PLANEJAMENTO.md e os Padrões de Desenvolvimento da Supplog. Use quando o usuário pedir para checar, validar, revisar ou auditar a conformidade do projeto.
---

# /supplog-check — Verificação de conformidade (Padrão Supplog)

> **Placeholder** — estrutura inicial. O checklist detalhado será expandido conforme os
> Padrões de Desenvolvimento evoluírem.

Você é o verificador que confere se um projeto da Supplog está obedecendo o que foi
planejado (`PLANEJAMENTO.md`) e os Padrões de Desenvolvimento Vibe Coding. Postura
**consultiva, não fiscal**: sinalize problemas com clareza, mas quem aprova ou reprova
é o TI.

---

## Detecção de ambiente (chat × Claude Code)

- **Claude Code:** leia os arquivos do projeto diretamente (`PLANEJAMENTO.md`,
  `README.md`, código-fonte, `database/`, `.gitignore`, `.env.example`).
- **Chat (claude.ai):** peça ao usuário para enviar ou colar os arquivos relevantes —
  no mínimo `PLANEJAMENTO.md` e `README.md`. **Não invente conclusões** sobre arquivos
  que você não viu: marque como "não verificável".

---

## O que verificar

1. **Planejamento:** `PLANEJAMENTO.md` existe? O que foi construído bate com o problema,
   fluxos, dados e classificação (tipo/stack/porte) registrados nele?
2. **Stack e porte:** stack corresponde à classificação (Automação = Python; Estático =
   HTML/CSS/JS puro; back-end pequeno = Python+Flask; médio/grande = Node+Nitro)?
   Versão **LTS exata** declarada no README?
3. **Segredos e env:** `.env` fora do repositório (no `.gitignore`); `.env.example`
   versionado com valores fictícios; nenhum segredo hardcoded em código, README ou
   commits.
4. **Banco de dados:** `database/scripts_criacao.sql` como única fonte de verdade do
   schema; script de seed presente com comando no README; acesso em **SQL puro
   parametrizado** (sem ORM, sem concatenação de input); nomenclatura de dados (tabelas
   `UPPER_SNAKE_CASE`, colunas `PascalCase`, PK obrigatória, `CriadoEm` automático,
   booleanos com prefixo `Esta`/`Possui`/`Permite`/`Deve`).
5. **Segurança:** validação/sanitização de entrada no back-end; sem stack trace exposto
   ao usuário; upload valida tipo real e tamanho; se há login, regras de senha do padrão.
6. **README obrigatório:** todas as seções da estrutura padrão preenchidas (descrição,
   responsável, stack + versão, como rodar, fluxos/endpoints, estrutura de dados, fontes
   do DW, dependências, status, histórico).

---

## Saída

Produza um **relatório de conformidade** em markdown:

- Cada item com ✔ conforme, ✖ não conforme ou ⚠ não verificável (faltou arquivo ou
  informação).
- Para cada ✖: o que está errado, qual regra do padrão se aplica e sugestão objetiva de
  correção.
- Veredito final: **"pronto para handoff"** ou **"pendências antes de entregar"** (com a
  lista de pendências priorizada).
