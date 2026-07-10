# Taxonomia de categorias

Classifique a issue em **UMA** categoria. Use o quadro de desambiguação para decidir entre categorias vizinhas; depois faça **apenas** as perguntas do bloco escolhido.

## Quadro de desambiguação (leia primeiro)
| Categoria | Escolha quando… | NÃO confunda com… |
|---|---|---|
| **Aspectos Visuais** | a UI/UX visível muda: estilo, layout, responsividade, animação | *Usabilidade* (funciona mas confunde) |
| **Bug** | comportamento quebrado/incorreto **com passos de reprodução** | *Revisão* (emergencial já diagnosticado); *Exceção* (causa incerta) |
| **Exceção** | problema **incerto**, precisa investigar antes de agir (spike) | *Bug* (já sabe reproduzir) |
| **Recurso** | funcionalidade/endpoint/integração **nova**, não existia | *Tarefa* (já 100% definido, mecânico) |
| **Tarefa** | trabalho **mecânico já definido** (subir lib, rodar migração) | *Recurso* (precisa de design/decisão) |
| **Problema de Usabilidade** | funciona tecnicamente, mas **confunde / gera fricção** | *Aspectos Visuais* (é estética/layout) |
| **Problema de Desempenho** | lentidão, memória, gargalo — **métrica** comprometida | *Bug* (quebrado, não lento) |
| **Revisão** | correção **emergencial e simples**, causa já conhecida | *Bug* (precisa de repro/investigação) |

---

## Aspectos Visuais
- Qual **componente/página** exata? (ex.: `src/components/Cart/CheckoutButton.tsx`)
- **Estado atual** (estilo/comportamento) vs. **estado esperado** (link do Figma ou descrição).
- Afeta **mobile, tablet, desktop** ou todos?
- Há **condição de borda**? (texto longo, tela 320px, hover/active states)

## Bug
- **Passos exatos para reproduzir** (1, 2, 3…). *[crítico]*
- **Comportamento atual** (logs, stack trace, screenshot). *[crítico]*
- **Comportamento esperado.** *[crítico]*
- **Ambiente/navegador/versão** em que ocorre.
- **Gravidade** — bloqueia fluxo crítico?

## Exceção (incerto / spike)
- Quais **interpretações/caminhos** estão em consideração? *[crítico]*
- Qual a **principal dúvida** técnica/funcional a responder? *[crítico]*
- **Prazo máximo** para a investigação.
- **Recursos/docs** para consultar e desempatar.

## Recurso (Feature)
- Qual o **resultado observável** para o usuário final? (sem user story) *[crítico]*
- **Fluxo técnico** (ex.: front chama API X, valida Y, salva em Z). *[crítico]*
- **Limites/restrições** (rate limit, permissões de role, tempo de resposta).
- Há **dependência** de outro time/sistema?

## Tarefa
- **Passos exatos** a executar. *[crítico]*
- Há **script/comando** pronto?
- **Resultado esperado** ao final. *[crítico]*

## Problema de Usabilidade
- Qual **persona/tarefa** o usuário tenta realizar? *[crítico]*
- Onde no fluxo ele **trava/se confunde**? (botão escondido, label ambígua, falta de feedback) *[crítico]*
- Qual a **métrica de sucesso**? (ex.: reduzir cliques de 5→2)

## Problema de Desempenho
- Qual **métrica** comprometida? (FCP, TTI, latência de API, bundle size) *[crítico]*
- **Valor atual** vs. **valor alvo/aceitável**. *[crítico]*
- Sob quais **condições** é percebido? (1k usuários, 3G, 50 imagens)
- Há **ferramenta de medição** para comparar antes/depois? (Datadog, Lighthouse)

## Revisão (Hotfix/Fix)
- O que exatamente está quebrado e precisa de ajuste? *[crítico]*
- A **causa raiz** já foi identificada? Qual é? *[crítico]*
- Precisa de **deploy urgente** (fora do ciclo)?
- Afeta outros pontos? (risco de regressão)
