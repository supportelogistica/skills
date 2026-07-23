---
name: supplog-handoff
description: Roda a verificação de conformidade e guia a entrega do projeto para o TI da Supporte. Use quando o usuário quiser entregar, publicar, subir ou passar o projeto para homologação ou produção.
---

# /supplog-handoff — Entrega do projeto para o TI (Padrão Supplog)

> **Placeholder** — estrutura inicial. Canal/contato oficial do TI e detalhes do processo
> serão preenchidos.

Você guia o viber na entrega do projeto para o TI da Supporte, garantindo que ele chegue
lá sem pendências óbvias.

---

## Fluxo

1. **Verifique antes de entregar.** Invoque `/supplog-check` e execute a verificação
   completa.
   - Há pendências ✖ → liste, oriente a corrigir e **pare aqui**. Entrega só sem
     pendências.
   - Tudo ✔ → siga para o checklist de entrega.
2. **Checklist de entrega:**
   - README completo: todas as seções, versão exata da stack, comando do seed.
   - Status no README atualizado para **"Aguardando aprovação"**.
   - `.env` real **fora** do repositório — os valores reais são repassados diretamente
     ao TI, nunca commitados.
   - Seed existe e roda em homologação; em produção **não** é executado.
   - Nenhum app que consome dados do DW publicado em produção externa/de terceiros —
     só o ambiente de produção interno.
3. **Prazos e expectativas (SLA):** explique ao viber que o TI avalia em até **10 dias
   úteis** (aprovado sobe; reprovado sai e o dev corrige) e que a homologação permanece
   por até **10 dias úteis**. O TI pode recusar em qualquer etapa se já existir solução
   equivalente na empresa.
4. **Acione o TI:** oriente como formalizar a entrega.
   <!-- TODO: canal/contato oficial do TI da Supporte e formato da solicitação -->

Confira e ajuste os itens do checklist nos arquivos reais do projeto (ex.: atualizar o
status no README).
