# SGMI — documentação do front (Gabriel)

Pasta de trabalho fora do repositório. Reorganizada em 19/10/2026 a partir dos relatórios de 05 a 15/10 (os originais estão em `arquivo/`).

## Situação em 19/10/2026

- **Branch atual:** `rf021/front-gabriel`, no mesmo commit do `dev-front` (`e74ca4b`), sem alterações.
- **Entregues e no `dev-front`:**
  - RF-001 a RF-005 (merge `f870b2c`);
  - RF-009 e RF-010 (merge `d1f83ef`).
- **Próxima:** RF-021 (checklist de implantação). Bloqueada: faltam os critérios e um back integrado (`pendencias.md`, item 20).

## O que tem em cada lugar

| Arquivo ou pasta | Conteúdo |
|---|---|
| `regras/regras-gerais.md` | Limites do front, time, stack e padrão da API, fluxo de cada RF, testes, ambiente e commits |
| `regras/padroes-front.md` | Mapa das pastas de templates, onde está cada RF e os padrões de código das seções |
| `requisitos/rf001-rf008.md` | Critérios do RF-001 ao RF-008 |
| `requisitos/rf009-rf010.md` | Critérios do RF-009 e do RF-010 |
| `requisitos/rf021.md` | Critérios do RF-021 (você preenche) e o que se sabe do back |
| `entregas/RFxxx.md` | Uma síntese por RF entregue: situação, commit, arquivos, decisões e pendências ligadas |
| `pendencias.md` | Lista única de pendências (inclui o antigo `alteracoes.md`) |
| `arquivo/` | Relatórios completos originais. Não editar; só para consultar detalhes de testes ou o histórico |

## Qual contexto usar em cada tarefa

| Tarefa | Arquivos |
|---|---|
| **Desenvolver uma RF nova (ex.: RF-021)** | `regras/regras-gerais.md` + `regras/padroes-front.md` + `requisitos/<da RF>.md` + `pendencias.md` + a entrega mais parecida (`entregas/RF010.md` para uma seção em lista) |
| Corrigir uma pendência | `pendencias.md` (o item) + `regras/padroes-front.md` + `entregas/<RF do arquivo>.md` |
| Conferir choque com RFs anteriores | `requisitos/rf001-rf008.md` + `requisitos/rf009-rf010.md` + as `entregas/` das telas tocadas |
| Ver o detalhe de um teste antigo | `arquivo/RFxxx.md`, seção 6 |

## Ao terminar uma RF

1. Criar `entregas/RFxxx.md` no formato das outras. Se precisar registrar os testes em detalhe, usar `entregas/RFxxx-testes.md`.
2. Atualizar o `pendencias.md`: situação de cada item e itens novos.
3. Atualizar a seção "Situação" deste arquivo.
