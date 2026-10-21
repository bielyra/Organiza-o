# Padrões do front — SGMI

Base: `dev-front` em `e74ca4b` (19/10/2026).
Caminhos relativos a `apps/sgmi/templates/sgmi/`.

---

## 1. Mapa das pastas

Reorganização da Vanessa (`e74ca4b`, 17/10). A regra completa está no `README.md` da pasta de templates.
**Regra:** o arquivo fica na pasta da faixa da RF que ele atende, mesmo quando é incluído por uma tela de outra faixa.

| Pasta | Faixa | O que tem hoje |
|---|---|---|
| `mudanca/` | RF-001 a RF-008 (+ histórico e detalhes) | `formulario_mudanca`, `editar_rascunho`, `detalhe_mudanca`, `historico_mudanca`, `card_formulario`, `card_anexos`, `classificacao_mudanca`, `secao_referencia`, `secao_escopos`, `selecionar_modelo`, `copiar_mudanca` |
| `responsabilidades/` | RF-009 a RF-014 | `secao_stakeholders`, `secao_impactos`, `secao_responsabilidades`, `scripts_matriz`, `alertas_papeis`, `conflito_executor` |
| `riscos/` | RF-015 a RF-022 | `secao_recuperacao`, `detalhe_recuperacao` (RF-019). **O RF-021 entra aqui** |
| `aprovacao/` | RF-023 a RF-030 | vazia |
| `execucao/` | RF-031 a RF-039 | vazia |
| `consulta/` | RF-040 a RF-044 | vazia |
| `includes/` | componentes sem dono de RF (modal genérico, paginação…) | vazia |

**Prefixos:**
- `secao_*`: bloco de edição, incluído no `mudanca/card_formulario.html`;
- `detalhe_*`: bloco de leitura, incluído no `mudanca/detalhe_mudanca.html`;
- `card_*`: agrupador de seções;
- `scripts_*`: JavaScript de um domínio.

Cada componente tem o próprio `<script>` numa IIFE e expõe a API pública em `window`.

## 2. Onde está cada RF sua

| RF | Arquivos | Objeto JS |
|---|---|---|
| RF-001 | `mudanca/card_formulario.html`, `mudanca/formulario_mudanca.html`, `mudanca/editar_rascunho.html` | `FormularioMudanca` |
| RF-002 | campo "Identificador" no `card_formulario.html`; `mudanca/detalhe_mudanca.html` | `FormularioMudanca.preencherIdentificador` |
| RF-003 | `mudanca/secao_referencia.html`; seção nos detalhes | `ReferenciaInstitucional` |
| RF-004 | `mudanca/classificacao_mudanca.html`; campo tipo no `card_formulario.html` | `ClassificacaoMudanca` |
| RF-005 | `mudanca/secao_escopos.html`; seção nos detalhes | `EscoposAfetados` |
| RF-009 | `responsabilidades/secao_stakeholders.html`; seção nos detalhes | `StakeholdersMudanca` |
| RF-010 | `responsabilidades/secao_impactos.html`; seção nos detalhes | `ImpactosMudanca` |

Objetos dos colegas que a sua tela chama ou que dependem de você: `ConfirmacaoRecuperacao` (RF-019, Amanda), `ResponsabilidadesMudanca` (Matriz RACI, Amanda), `AlertasPapeis` (RF-013) e `ConflitoExecutor` (RF-014), da Vanessa.

**Telas compartilhadas:** `card_formulario.html`, `editar_rascunho.html` e `detalhe_mudanca.html`. Ao mexer nelas:
- não mudar `id`s nem funções que outros usam;
- registrar no `entregas/` o que mudou, para avisar os colegas.

## 3. Ordem das seções no formulário

Em `mudanca/card_formulario.html`:
1. classificação (RF-004);
2. referência (RF-003);
3. escopos (RF-005);
4. stakeholders (RF-009);
5. impactos (RF-010);
6. recuperação (RF-019);
7. Matriz RACI (RF-011/012);
8. alertas de papéis (RF-013);
9. conflito de executor (RF-014).

Ao abrir o rascunho, `editar_rascunho.html` chama `carregar(situacao)` de cada seção. "Salvar Rascunho" e "Enviar Mudança" salvam os pendentes nesta ordem: escopo → stakeholder → impacto → recuperação.

## 4. Padrão das seções em lista (escopos, stakeholders, impactos)

1. **Opção A:** sem id (tela nova), a seção aparece desabilitada, com o aviso "Salve o rascunho para…". Na edição, funciona por completo. O back só aceita itens de uma mudança já criada.
2. **Campos fora do formulário da mudança.** O back recusa campos extras, então os campos da seção ficam sem `name`. Nos grupos de rádio, que precisam de `name`, usar `form="sem-formulario-…"`, apontando para um formulário que não existe. Cada seção faz o próprio envio.
3. **API pública do objeto:**
   - `carregar(situacao)`: busca a lista e mostra os controles conforme a situação e as permissões;
   - `salvarPendente()`: salva o que está no editor, se houver;
   - `avisarFalha(resultado, resumo)`;
   - `limparErros()`.

   As operações devolvem `{ ok, salvou, mensagem }`.
4. **Nada se perde.** O que está no editor é salvo com "Salvar Rascunho" e antes do "Enviar Mudança". Com erro: "O rascunho foi salvo, mas o … em edição não. Corrija os campos destacados.". Uma só confirmação lista tudo o que foi salvo.
5. **Um único editor** para incluir e editar: "Salvar alterações", "Cancelar edição" e o item marcado "(em edição)". Pergunta antes de descartar dados não salvos.
6. **A lista é sempre recarregada do back** depois de incluir, editar ou remover, com a contagem no título ("Stakeholders cadastrados (4)").
7. **Remoção:** `confirm()` nomeando o item. Depois de remover, o foco vai para um item vizinho.
8. **URL do item:** trocar só o trecho, por exemplo `'/stakeholders/0/'` → `` `/stakeholders/${id}/` ``. **Nunca** `replace('0', id)`, que falha em mudanças com 0 no número (10, 20, 100…).
9. **Permissões e situação:**
   - `perms.sgmi.*` no template decide o editor e os botões (ver, adicionar, editar, remover);
   - um 409 do back trava a seção e mantém a lista.
10. **Lista vazia:** "Nenhum … cadastrado." começa com `d-none` na edição e só aparece depois de a lista carregar.
11. **404:** tratar qualquer 404, mesmo em HTML, como "Este … não existe mais. A lista foi atualizada.".
12. **Tipos fixos na tela** quando o back não tem endpoint que os liste.
13. **Sem `*`** nos campos das seções opcionais. No formulário, o `*` significa "obrigatório para enviar a mudança"; a ajuda da seção explica o que é obrigatório em cada item.

## 5. Respostas e mensagens

- `FormularioMudanca.lerResposta(res)`: lê a resposta sem quebrar quando ela vem em HTML e devolve `{status, json, body}`.
- `FormularioMudanca.mostrarErros(errors, resumo)`:
  - erro junto ao campo (`is-invalid`, `aria-invalid`, `#erro-<campo>`);
  - resumo no topo (`#mensagens-formulario`), toastr e anúncio;
  - os erros agrupados (`institutional_reference`, `impacts`, `recovery_confirmation`) são encaminhados à seção dona deles.
- Leitores de tela:
  - anúncios em `#anuncio-formulario` (`aria-live`);
  - falhas em `role="alert"` e avisos em `role="status"`;
  - campos com `aria-describedby` ligando a ajuda e o erro.
- Mensagens sem nomes técnicos. Erros 5xx ganham "Tente novamente em instantes.".

## 6. Acessibilidade e visual

- **Foco visível nos botões** (o tema zera o padrão):
  ```css
  #secao-x .btn:focus-visible { outline: 2px solid #0062cc; outline-offset: 2px; }
  .dark-mode #secao-x .btn:focus-visible { outline-color: #66b0ff; }
  ```
- **Cores que passam de 4,5:1:**
  - azul `#0062cc` (claro) e `#66b0ff` (escuro);
  - vermelho de erro `#ff8080` no escuro;
  - verde do "Enviar Mudança" `#1e7e34`;
  - "Editar" e "Remover" de contorno têm cores próprias no tema escuro (copiar de `secao_escopos.html`).
- **Limite de caracteres em texto de várias linhas:** usar contador, e não `maxlength`. O navegador envia a quebra de linha como `\r\n`, que o back conta como 2 caracteres.
- **Datas e horas:** o back devolve em UTC. Mostrar e enviar no fuso do servidor (`America/Recife`), lido com `{% load tz %}{% get_current_timezone as FUSO_SERVIDOR %}`, e não no fuso do navegador (exemplo em `secao_impactos.html`).
- **Edição de campo omitido:** em alguns endpoints o back mantém o valor antigo de um campo não enviado. Para limpar, enviar o campo vazio (caso dos impactos).
