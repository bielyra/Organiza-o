# Pendências — SGMI (front)

Lista única de pendências do seu front. Junta o antigo `pendencias.md` (itens 1 a 14) e o `alteracoes.md` (pedidos ao back). Os dois originais estão em `arquivo/`.

- **Última verificação:** 19/10/2026, lendo o código do `dev-front` em `e74ca4b` (igual à branch `rf021/front-gabriel`) e as branches abertas dos colegas. **Não foram rodados testes.**
- **Caminhos:** relativos a `apps/sgmi/templates/sgmi/`, já com a reorganização da Vanessa (`e74ca4b`).
- **Números:** os itens 1 a 14 mantêm o número antigo; os itens 15 a 21 são novos.

**Legenda:** 🔴 aberta · 🟡 resolvida fora do `dev-front` (falta integrar) ou parcial · ⏸️ adiada ou esperando definição · ✅ resolvida

## Resumo

| # | Pendência | Com quem | Prioridade | Situação |
|---|---|---|---|---|
| 1 | "Enviar Mudança" volta a ficar habilitado depois de salvar (RF-001 × RF-013/RF-014) | Vanessa + você | Média | 🔴 |
| 2 | Matriz RACI: remover atribuição falha em mudanças com "0" no número | Amanda | Alta | 🟡 corrigida na branch dela |
| 3 | Matriz RACI: erro de JavaScript para quem não pode ver responsabilidades | Amanda | Média | 🟡 parcial na branch dela |
| 4 | Histórico: eventos de stakeholders, impactos, responsabilidades e modelos aparecem como "Registro" | Amanda | Baixa | 🔴 |
| 5 | Back troca "º" e "ª" por "o" e "a" (stakeholders e escopos) | Back (Vicenzo) | Média | 🔴 |
| 6 | "Enviar Mudança" só valida; a rota que submete não é usada | A definir | Média | 🔴 |
| 7 | Papéis da Matriz RACI não vêm nas migrações | BDA / back | Baixa | 🔴 |
| 8 | Foco invisível nos botões (o tema zera o anel de foco) | Você (+ time) | Média | 🔴 |
| 9 | RF-005: item removido em outra aba gera mensagem genérica | Você | Baixa | 🔴 |
| 10 | RF-005: "Nenhum escopo afetado cadastrado." antes de a lista carregar | Você | Baixa | 🔴 |
| 11 | Edição de mudança submetida abre vazia, sem caminho para os detalhes | Você | Baixa | 🔴 |
| 12 | Modal do responsável ainda lista usuários inativos | Você | Baixa | 🔴 |
| 13 | RF-010: remover impacto (o back já criou a rota) | Você, depois da integração | Média | 🟡 back no `dev-back` |
| 14 | Teste do back espera o caminho antigo da tela de detalhes (1 falha na suíte) | Amanda / back | Média | 🔴 |
| 15 | Nenhuma tela leva à página de detalhes | Você | Média | 🔴 novo |
| 16 | RF-004: exigências e descrição dos tipos lidas da API | Você + back | Baixa | 🟡 novo |
| 17 | Modal do responsável: listar ao abrir e comentário desatualizado | Você | Baixa | 🔴 novo |
| 18 | Login e permissões das telas de interface | Back | — | ⏸️ adiada |
| 19 | RF-004: campos que aparecem ou somem conforme o tipo | Quem define os requisitos | — | ⏸️ |
| 20 | RF-021 bloqueada: sem critérios e sem back integrado | Você + Rodrigo / Max | Alta | 🔴 novo |
| 21 | Não existe tela que liste as mudanças | Quem define os requisitos | — | ⏸️ pergunta |

---

## Suas (front)

### 8. Foco invisível nos botões
- **O que acontece:** o AdminLTE zera o anel de foco do Bootstrap. Navegando com Tab:
  - os botões de contorno (ex.: "Editar" e "Remover" dos escopos) não mudam nada;
  - os coloridos ("Salvar Rascunho", "Adicionar escopo") só escurecem um pouco.
- **Situação em 19/10:** 🔴 aberta. O `:focus-visible` só existe em `responsabilidades/secao_stakeholders.html` e `responsabilidades/secao_impactos.html`.
- **Correção dos relatórios:** a checagem "foco visível nos botões" do RF-001 ao RF-005 era **falso positivo** (aceitava sombra de 0px). Já anotado em `entregas/RF001.md`.
- **Sugestão:**
  - uma regra única de `.btn:focus-visible` no CSS do projeto (`base/static/`), que vale para todas as telas (combinar com o time);
  - ou repetir a regra do RF-009 nas seções do RF-001 ao RF-005.

### 9. RF-005: item já removido em outra aba gera mensagem genérica
- **Onde:** `mudanca/secao_escopos.html`, linha 414: `if (resultado.json && resultado.status === 404)`.
- **O que acontece:** o back responde 404 em HTML, e a tela mostra "Não foi possível remover o escopo…" em vez de "Este escopo não existe mais. A lista foi atualizada.".
- **Situação em 19/10:** 🔴 aberta.
- **Correção:** tirar o `resultado.json &&` (1 linha), como no RF-009.

### 10. RF-005: "Nenhum escopo afetado cadastrado." antes de carregar
- **Onde:** `mudanca/secao_escopos.html`, linha 28, parágrafo `#escopos-vazio`.
- **O que acontece:** na edição, a mensagem aparece antes de a lista vir do back. Se o rascunho não abrir, ela fica afirmando algo que não foi verificado.
- **Situação em 19/10:** 🔴 aberta.
- **Correção:** começar com `d-none` na edição (`{% if view.kwargs.pk %} d-none{% endif %}`), como no RF-009 (1 linha).

### 11. Edição de mudança submetida abre vazia
- **Onde:** `mudanca/editar_rascunho.html`.
- **O que acontece:** o back recusa abrir o rascunho de uma mudança que não está em rascunho (409). A tela abre vazia, só com "Apenas mudanças em rascunho podem ser alteradas.".
- **Situação em 19/10:** 🔴 aberta.
- **Sugestão:** nesse caso, mostrar um link para os detalhes (`{% url 'sgmi:interface_detalhe_mudanca' pk=view.kwargs.pk %}`). Pode ser feito junto com o item 15.

### 12. Modal do responsável ainda lista usuários inativos
- **O que acontece:** o back atendeu o pedido com o parâmetro opcional `?active=true` na busca do portal (`cb342ae`). O modal não envia esse parâmetro, e um usuário inativo ainda aparece. O back recusa ao salvar, e a tela mostra o erro junto ao campo.
- **Situação em 19/10:** 🔴 aberta. `portal/components/modal_select_enjoyer.html` faz `hx-get="{% url search_url %}"` sem o parâmetro.
- **Sugestão:** fazer o seletor enviar `active=true` (ex.: `hx-vals`). O componente é do app `portal` e é usado por outras telas, então conferir antes e combinar.

### 13. RF-010: remover impacto
- **Histórico:** o back de impactos não tinha rota de remoção; a seção avisa "depois de salvo, pode ser editado, mas não removido".
- **Situação em 19/10:** 🟡 o Vicenzo criou a rota no `dev-back` (`a1d9108`, 15/10):
  - `POST mudancas/<id>/impactos/<impacto>/remover/`, nome `sgmi:change_impact_delete`;
  - permissões `view_change` + `delete_changeimpact`, só em rascunho, com auditoria `DELETE` (migração `0021`);
  - **ainda não está no `dev-front`**, que é a base da sua branch.
- **O que fazer (você):** depois que o `dev-back` for integrado ao `dev-front`, incluir "Remover" em `responsabilidades/secao_impactos.html`, no padrão dos stakeholders:
  - `confirm()` nomeando o impacto;
  - URL trocando só o trecho `/impactos/0/`;
  - 404 tratado e foco no vizinho;
  - botão só com `perms.sgmi.delete_changeimpact` e a mudança em rascunho;
  - tirar o aviso "não removido" da ajuda;
  - retestar a numeração "Impacto 1, 2…" depois de uma remoção.

  Os critérios do RF-010 não exigem a remoção; é para manter o padrão das outras seções.

### 15. Nenhuma tela leva à página de detalhes
- **O que acontece:** a rota `interface_detalhe_mudanca` existe desde `89c627b`, mas nenhum template a usa. O "Enviar Mudança" válido volta para a página inicial. Só se chega aos detalhes digitando a URL.
- **Situação em 19/10:** 🔴 aberta. Era o próximo passo do RF-002 ("quando o back criar a rota de detalhes").
- **Sugestão:**
  - botão "Ver Detalhes" no cabeçalho da edição (`mudanca/card_formulario.html`, só com id);
  - depois do "Enviar Mudança" válido, ir para os detalhes em vez da página inicial (combinar com o item 6);
  - link na edição de mudança submetida (item 11).

### 16. RF-004: exigências e descrição dos tipos pela API
- **O que acontece:** o front mantém uma cópia das descrições e das exigências de cada tipo (constantes `TIPOS` e `EXIGENCIAS` em `mudanca/classificacao_mudanca.html`).
- **Situação em 19/10:** 🟡 parcial.
  - O back já devolve `required_flags` na lista de tipos (`GET /sgmi/parametros/CHANGE_TYPE/`, `_serialize_change_type_parameter`, commit `4061335` de 06/10). O front ainda não usa.
  - A **descrição** dos tipos ainda não vem: `_serialize_parameter` (`apps/sgmi/views.py`, linha 399) devolve só `id`, `category`, `code`, `name` e `active`.
- **Sugestão:**
  - **você:** ler as exigências do `required_flags` da lista, em vez da cópia;
  - **back (desejável):** incluir `description` no `Parameter`, com os textos do BDA, e devolvê-la na lista.

### 17. Modal do responsável: listar ao abrir e comentário desatualizado
- **Onde:** `mudanca/card_formulario.html`, no `shown.bs.modal` do modal do responsável.
- **O que acontece:** o comentário diz que "a busca do portal com o campo vazio retorna erro no back", mas o back já corrigiu (`c0060ac`). O modal ainda pede para digitar antes de listar.
- **Sugestão (opcional):** listar os usuários ao abrir e atualizar o comentário. Dá para fazer junto com o item 12.

### 20. RF-021 bloqueada
- **Critérios:** ainda não estão em nenhum arquivo. Você vai colocá-los em `requisitos/rf021.md`.
- **Back:** nada do RF-021 está no `dev-back` nem no `dev-front`.
  - `rf021/back-rodrigo` (`2964233`, 15/10): `Checklist` e `ItemChecklist` em `base/models.py`, ligados ao `MudancaInstitucional` do template inicial, e não ao `Change` do `apps/sgmi`. A branch parte do commit inicial (`703b203`), não do `dev-back`. Não há views nem rotas.
  - `rf021/bda-max` (`07206b1`, 19/10): app `apps/checklists` (modelos, serviços, consultas, auditoria), sem views nem rotas e fora do `dev-data`.
- **Sugestão:** perguntar ao Rodrigo qual modelo será o oficial e quando as rotas JSON chegam ao `dev-back`. Sem rotas, o front só consegue planejar as telas. Pasta prevista: `riscos/`.

---

## Com colegas

### 1. "Enviar Mudança" reabilitado depois de salvar (RF-001 × RF-013/RF-014)
- **Com quem:** Vanessa (alertas do RF-013 e conflito do RF-014). O código que reabilita o botão é seu, do RF-001. **Sua decisão (13/10): avisar, não corrigir agora.**
- **Onde:** `mudanca/editar_rascunho.html`, no `finally` de "Salvar Rascunho" (linha 360) e de "Enviar Mudança" (linha 443): `btnSubmeter.disabled = false;`.
- **O que acontece:** as telas da Vanessa desabilitam o "Enviar Mudança" enquanto faltam os papéis Decisão, Execução e Validação ou há conflito de executor. Depois de salvar, o seu código reabilita o botão, e o alerta continua na tela. O back recusa o envio do mesmo jeito.
- **Situação em 19/10:** 🔴 aberta. A branch `RF013/RF014-vanessa-front` está igual ao `dev-front`.
- **Sugestão:** no `finally`, chamar `AlertasPapeis.atualizarBotaoSubmissao()` quando existir, em vez de habilitar direto. Ele já consulta o conflito do RF-014 (`ConflitoExecutor.temConflito()`). Combinar quem faz.

### 2. Matriz RACI: remover atribuição em mudanças com "0" no número
- **Com quem:** Amanda (RF-011/012).
- **Onde:** `responsabilidades/scripts_matriz.html`, linha 246: `URL_REMOVER.replace('0', resp.id)`.
- **O que acontece:** o `replace` troca o **primeiro** "0" da URL, que fica no número da mudança. Na mudança 10, remover a atribuição 7 chama `/sgmi/mudancas/17/responsabilidades/0/remover/`.
- **Situação em 19/10:** 🟡 corrigida na branch `ajustes-rf019/front-amanda` (`40de781`, 16/10), com `replace(/\/0(\/|$)/, …)`. **Ainda não está no `dev-front`.**
- **Falta:** a Amanda abrir ou concluir o Merge Request.

### 3. Matriz RACI: erro de JavaScript para quem não pode ver responsabilidades
- **Com quem:** Amanda.
- **Onde:** `responsabilidades/scripts_matriz.html`, função `recarregar()`: `fetch(URL_MATRIZ).then(res => res.json())`, sem tratar erro.
- **O que acontece:** sem a permissão, o back devolve uma página HTML de 403, o `res.json()` quebra e a seção não avisa a pessoa.
- **Situação em 19/10:** 🟡 parcial na mesma branch (`40de781`): agora confere o `res.ok` antes do `res.json()`. **Falta conferir** se a pessoa recebe um aviso de permissão, como nas seções de escopos e stakeholders.

### 4. Histórico: eventos aparecem como "Registro"
- **Com quem:** Amanda (RF-042).
- **Onde:** `mudanca/historico_mudanca.html`, objeto `tiposAuditoria` (linhas 55 a 61).
- **O que acontece:** ele só conhece `change`, `affected_scope`, `institutional_reference`, `change_attachment` e `change_copy`. Os eventos `change_stakeholder`, `change_impact`, `change_responsibility` e `change_template` (e os de recuperação) aparecem como "Registro".
- **Situação em 19/10:** 🔴 aberta.
- **Sugestão:** incluir os rótulos ("Stakeholder", "Impacto", "Responsabilidade", "Modelo"…).

### 14. Teste do back espera o caminho antigo da tela de detalhes
- **Com quem:** Amanda (reorganização `e290294`) / back, porque o arquivo é um teste em Python.
- **Onde:** `apps/sgmi/tests/test_change_detail_interface.py`, linha 64, que espera `'sgmi/detalhe_mudanca.html'`. A view usa `'sgmi/mudanca/detalhe_mudanca.html'` (`views.py`, linha 830).
- **Situação em 19/10:** 🔴 aberta (conferido por leitura). A suíte do back continua com 1 falha.
- **Sugestão:** trocar o caminho no teste. Não é para você mexer (arquivo de back).

---

## Back e BDA

### 5. Back troca "º" e "ª" por "o" e "a"
- **Com quem:** back, Vicenzo (RF-009; vale também para o nome do escopo do RF-005).
- **Onde:** `apps/sgmi/models.py`, `canonicalize_affected_scope_name` e `canonicalize_stakeholder_text` (linhas 23 e 34), com `unicodedata.normalize('NFKC', …)`.
- **O que acontece:** "1º Batalhão de Polícia Militar" é gravado como "1o Batalhão de Polícia Militar". Os nomes de unidades da PM usam muito os ordinais.
- **Situação em 19/10:** 🔴 aberta.
- **Sugestão:** usar NFKC só na chave de comparação (`normalize_*`) e gravar o texto com NFC.

### 7. Papéis da Matriz RACI não vêm nas migrações
- **Com quem:** BDA / back.
- **O que acontece:** os tipos de mudança são criados por migração (`0012_seed_change_type_parameters`), mas os papéis (categoria `ROLE`: Decisão, Execução, Validação…) não. Num banco novo, a Matriz RACI fica sem opções e o "Enviar Mudança" fica sempre bloqueado.
- **Situação em 19/10:** 🔴 aberta no `dev-front`.
- **Sugestão:** confirmar de onde vêm (carga do BDA?) e registrar no README. Nos testes, criar os papéis no banco de teste.

### 18. Login e permissões das telas de interface (adiado)
- **Origem:** item 4 do antigo `alteracoes.md`. **Sua decisão: deixar para outro momento.**
- **O que acontece:**
  - as telas nova, editar, copiar, selecionar modelo e histórico são `TemplateView` **sem login**. Isso afeta o critério "acessado por usuário autorizado" (RF-001, tarefa 1);
  - criar mudança exige só login, mas reabrir e salvar exigem `sgmi.change_change`;
  - a tela de detalhes já exige login, `view_change` e visibilidade.
- **Situação em 19/10:** ⏸️ adiada, sem mudança.

---

## Perguntas para quem define os requisitos

### 6. "Enviar Mudança" só valida
- **O que acontece:** o botão chama `validar-submissao` e volta para a página inicial com "Mudança válida e pronta para seguir fluxo!". A rota `mudancas/<id>/submeter/` (`change_submit`) existe e muda a situação para "Submetida", mas nenhuma tela a usa.
- **Situação em 19/10:** 🔴 aberta.
- **Perguntar:** quem faz o front da submissão? É uma tarefa do RF-001 (seu), do RF-013/RF-014 ou de outra RF?

### 19. RF-004: campos que dependem do tipo
- **O que acontece:** os critérios falam em "campos exclusivos das críticas" e "campos adicionais exigidos pelo tipo", mas não existe campo específico por tipo, e o back exige os mesmos campos para todos.
- **Perguntar:** quais são esses campos? Vêm de outra RF (aprovação, risco, plano de reversão…) ou o back vai criá-los?
- **Situação em 19/10:** ⏸️ sem definição. O caminho no front está em `entregas/RF004.md`.

### 21. Não existe tela que liste as mudanças
- **O que acontece:** sem uma lista, só se chega a uma mudança pela URL ou pelos fluxos de criação, cópia e modelo.
- **Perguntar:** alguma RF prevê essa tela? A faixa RF-040 a RF-044 (consulta) parece o lugar (pasta `consulta/`).

---

## Resolvidas

| Item | Como foi resolvido |
|---|---|
| `alteracoes.md` 1: rota da tela de detalhes | ✅ Back, commit `89c627b` (login, `view_change` e visibilidade) |
| `alteracoes.md` 2: busca de usuários vazia dava erro 500 | ✅ Back, merge `c0060ac`. Sobra o item 17 (listar ao abrir, opcional) |
| `alteracoes.md` 3: busca trazia inativos | Back ofereceu `?active=true` (`cb342ae`); falta o front usar → item 12 |
| `alteracoes.md` 4: login das telas | Continua adiado → item 18 |
| `alteracoes.md` 5: descrição e exigências na lista de tipos | Exigências ✅ (`4061335`); descrição falta → item 16 |

---

## Histórico deste arquivo

| Data | O que mudou |
|---|---|
| 05/10 | `alteracoes.md` v1 e v2: pedidos 1 a 5 ao back (RF-001 a RF-004) |
| 13 e 14/10 | `pendencias.md`, itens 1 a 13 (RF-009 e RF-010) |
| 15/10 | Item 14 (merge do `dev-front`) e caminhos da reorganização da Amanda |
| 19/10 | Junção com o `alteracoes.md`; todos os itens conferidos no `e74ca4b` e nas branches abertas; caminhos da reorganização da Vanessa; itens 15 a 21; os itens 2, 3 e 13 mudaram de situação |
