# Modelo do Linear + chamada MCP

## Prioridade (valor numérico obrigatório na API)
| Valor | Nome |
|---|---|
| 0 | No priority |
| 1 | Urgent |
| 2 | High |
| 3 | **Medium** ← ("Normal" não existe no Linear) |
| 4 | Low |

`create_issue` recebe o **número**, não o nome. "Alta" → `2`, "média/normal" → `3`.

## Resolução de alvos por ID (nunca chute IDs)
Labels, teams, usuários e projetos são específicos do workspace. Sempre resolva por ID a partir de dados reais:

1. **Team (obrigatório):** `mcp__linear-server__list_teams` → apresente as opções → usuário escolhe → guarde `teamId`.
2. **Label:** `mcp__linear-server__list_issue_labels` (filtre pelo team) → mapeie a categoria escolhida para a label existente mais próxima → guarde `labelIds` (array).
   - Se **não houver** label correspondente: informe e ofereça (a) usar a mais próxima, (b) criar uma nova label, ou (c) seguir sem label. Não invente um ID.
   - Lembre: no Linear só **uma** label por *label group* pode ser aplicada por vez.
3. **Assignee (opcional):** `mcp__linear-server__list_users` → resolva o nome para `assigneeId`. Aceita "a definir" (deixe sem).
4. **Projeto (opcional):** `mcp__linear-server__list_projects` → `projectId`.

## Assinatura de criação
```
mcp__linear-server__create_issue({
  teamId,               // obrigatório
  title,                // título orientado a ação
  description,          // corpo em markdown (ver templates/issue.md)
  priority,             // 0-4 (número)
  labelIds,             // array de IDs (opcional)
  assigneeId,           // opcional
  projectId,            // opcional
})
```
Depois de criar, confirme com `mcp__linear-server__get_issue` e devolva ao usuário a **URL** e o **identifier** (ex.: `ENG-123`).

> Os nomes/parâmetros exatos das ferramentas podem variar conforme a versão do servidor Linear MCP. Se uma ferramenta não existir ou um parâmetro for rejeitado, liste as ferramentas `mcp__linear-server__*` disponíveis e ajuste os nomes dos campos ao que o servidor expõe.

## Fallback (MCP ausente)
Se nenhuma ferramenta `mcp__linear-server__*` existir: gere o draft em markdown, avise que o MCP não está conectado e não crie nada. Setup:
```
claude mcp add --transport sse linear-server https://mcp.linear.app/sse
```
