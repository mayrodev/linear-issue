# Modelo do Linear + chamada MCP

> As ferramentas podem aparecer com o prefixo `mcp__linear-server__` (servidor
> local) ou `mcp__claude_ai_Linear__` (conector do claude.ai). Os nomes abaixo
> omitem o prefixo.

## Prioridade (valor numérico obrigatório na API)
| Valor | Nome |
|---|---|
| 0 | No priority |
| 1 | Urgent |
| 2 | High |
| 3 | **Medium** ← default quando o usuário não souber definir ("Normal" não existe no Linear) |
| 4 | Low |

`create_issue` recebe o **número**, não o nome. "Alta" → `2`, "média/normal" → `3`.

> **Nota:** A prioridade é um campo não-crítico. Se o usuário não souber definir, use
> `3` (Medium) como default resolvido e não o registre como pendência apenas por ter
> usado o padrão. Só crie uma pendência se o usuário indicar concretamente que a
> prioridade precisa ser revista. O importante é ter o contexto de produto e os
> critérios de conclusão claros — a prioridade pode ser ajustada depois.

## Resolução de alvos por ID (nunca chute IDs)
Labels, teams, usuários e projetos são específicos do workspace. Sempre resolva por ID a partir de dados reais:

1. **Team (obrigatório):** `list_teams` → apresente as opções → usuário escolhe → guarde o `teamId` (ou nome exato).
2. **Label da categoria:** `list_issue_labels` (filtre pelo team) → mapeie a categoria escolhida na entrevista (ver `references/taxonomy.md`) para a label existente mais próxima → guarde o ID.
   - Se **não houver** label correspondente: informe e ofereça (a) usar a mais próxima, (b) criar uma nova label, ou (c) seguir sem label. Não invente um ID.
   - Lembre: no Linear só **uma** label por *label group* pode ser aplicada por vez.
3. **Etapa (default = Codificação):** se `list_issue_labels` mostrar um *label group*
   `Etapa` (valores comuns: `Codificação`, `Design / UI`, `Research / UX Writer` —
   confirme os reais), pergunte a etapa e adicione a label correspondente. Se o
   usuário não souber, use `Codificação`. Se o team não tiver esse grupo, ignore o
   campo — não crie labels nem invente valores.
4. **Assignee (default = usuário atual):** por padrão, atribua ao usuário que pediu
   a criação — resolva com `get_user({ query: "me" })` e use o `id` retornado (o
   servidor também aceita `"me"` direto em `assignee`). Só troque se o usuário pedir
   explicitamente outra pessoa; nesse caso, resolva com `list_users`.
5. **Projeto (opcional):** `list_projects` → `projectId`.
6. **Parent issue (opcional):** se for sub-issue de outra issue já existente, guarde
   o identifier/ID (ex.: `ENG-123`) para usar como `parentId` na criação.
7. **Status e cycle (só se pedido):** por padrão a issue entra no status default do
   team. Se o usuário pedir um status ou cycle específico, resolva com
   `list_issue_statuses` / `list_cycles` antes de preencher `state` / `cycle`.

## Board, views e cycle
Toda issue criada num team aparece automaticamente no board
e nas views daquele team, na coluna do seu status. Não há associação manual a board.
Cycles são opcionais: só atribua quando o usuário pedir e com o valor real resolvido.

## Assinatura de criação
```
create_issue({
  team,                 // obrigatório (ID ou nome exato)
  title,                // título orientado a ação
  description,          // corpo em markdown (ver templates/issue.md)
  priority,             // 0-4 (número); default = 3
  labels,               // array de IDs/nomes: categoria + Etapa (se houver)
  assignee,             // ID do usuário; default = usuário atual ("me")
  project,              // opcional
  parentId,             // opcional, issue pai
  state,                // opcional, só se pedido
  cycle,                // opcional, só se pedido
})
```
Depois de criar, confirme com `get_issue` e devolva ao usuário a **URL** e o **identifier** (ex.: `ENG-123`).

> Os nomes/parâmetros exatos das ferramentas podem variar conforme a versão do
> servidor Linear MCP (ex.: versões antigas usam `teamId`, `labelIds`, `assigneeId`,
> `projectId`). Se uma ferramenta não existir ou um parâmetro for rejeitado, liste
> as ferramentas do Linear disponíveis e ajuste os nomes dos campos ao que o
> servidor expõe.

## Fallback (MCP ausente)
Se nenhuma ferramenta do Linear MCP existir: gere o draft em markdown, avise que o MCP não está conectado e não crie nada. Se só existir `mcp__claude_ai_Linear__authenticate`, peça para o usuário autenticar o conector. Setup do servidor local:
```
claude mcp add --transport http linear-server https://mcp.linear.app/mcp
```
