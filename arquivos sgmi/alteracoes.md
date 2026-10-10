# Alterações pedidas ao back-end (SGMI)

Pedidos do front (RF-001 a RF-005, branch `rf001/front-gabriel`) que dependem de arquivos de back.
Levantados em 05/10/2026, durante o desenvolvimento e os testes do RF-001, do RF-002 e do RF-004.

| # | Pedido | Tipo | Prioridade | Situação |
|---|---|---|---|---|
| 1 | Rota da tela de detalhes da mudança | Rota nova | **Alta** (bloqueia o RF-002 e, depois, o RF-003 e o RF-005) | Pendente |
| 2 | Busca de usuários com o campo vazio dá erro 500 | Bug | Média | Pendente |
| 3 | Busca de usuários traz usuários inativos | Melhoria | Baixa (desejável) | Pendente |
| 4 | Login e permissões das telas de interface | Ajuste | Adiado (decidido deixar para depois) | Adiado |
| 5 | Lista de tipos de mudança com descrição e exigências | Melhoria | Baixa (desejável; não bloqueia o RF-004) | Pendente |

---

## 1. Rota da tela de detalhes da mudança

**Por quê:** o RF-002 (tarefa 3), o RF-003 (tarefa 4) e o RF-005 (tarefa 5) pedem uma página de detalhes da mudança. O template já está pronto no front; falta só a rota da tela.

**Onde:** `apps/sgmi/urls.py`, junto das outras rotas de interface (`interface_nova_mudanca`, `interface_editar_rascunho`…):

```python
path(
    'mudancas/interface/<int:pk>/detalhes/',
    TemplateView.as_view(template_name='sgmi/detalhe_mudanca.html'),
    name='interface_detalhe_mudanca'
),
```

**Observações:**
- O template `apps/sgmi/templates/sgmi/detalhe_mudanca.html` já existe (front).
- Ele busca os dados na rota JSON que já existe: `sgmi:change_detail` (`GET /sgmi/mudancas/<id>/`, que exige a permissão `sgmi.view_change`).
- Depois que a rota existir, o front vai adicionar os links para os detalhes nas outras telas.

---

## 2. Bug: busca de usuários com o campo vazio dá erro 500

**Onde:** `apps/portal/views.py`, `SearchEnjoyerView.get` (linha 165).

**O que acontece:** quando `search` vem vazio, o código faz `enjoyers = enjoyers[:15]` e, logo depois, `enjoyers = enjoyers.distinct()[:25]`. O Django não permite `distinct()` depois de um corte:

```
TypeError: Cannot create distinct fields once a slice has been taken.
```

**Como reproduzir:** acessar `/portal/buscar-utilizador-parcial/` sem o parâmetro `search` (ou com ele vazio).

**Correção sugerida:** aplicar o `distinct()` antes do corte, ou cortar uma única vez no final. Por exemplo:

```python
if query:
    enjoyers = enjoyers.filter(...).distinct()[:25]
else:
    enjoyers = enjoyers[:15]
```

**Impacto no front:** o modal de seleção de responsável (RF-001) hoje pede para digitar antes de listar, justamente para não cair nesse erro. Com a correção, ele pode listar os usuários assim que abre.

---

## 3. Busca de usuários traz usuários inativos (desejável)

**Onde:** `apps/portal/views.py`, `SearchEnjoyerView` e `SearchEnjoyerJSONView`.

**O que acontece:** a busca lista Enjoyers com `active=False`. O cadastro de mudança recusa responsável inativo ("Faça uma escolha válida…"), e o front mostra esse erro junto ao campo, mas o ideal é nem listar.

**Sugestão:** filtrar `active=True` nessas buscas, ou criar um parâmetro para isso se outras telas precisarem dos inativos.

---

## 4. Login e permissões das telas (adiado)

Fica registrado para não esquecer; **não é para agora**.
- As rotas de interface do SGMI (`TemplateView`) **não exigem login**. Isso afeta o critério "O formulário pode ser acessado por usuário autorizado" (RF-001, tarefa 1).
- Criar mudança exige só login, mas reabrir e salvar rascunho exigem `sgmi.change_change`. Um usuário comum consegue criar o rascunho e leva 403 ao reabrir.

---

## 5. Lista de tipos de mudança com descrição e exigências (desejável)

**Por quê:** o RF-004 pede uma explicação de cada tipo (tarefa 2) e que a tela siga as regras do back (tarefa 3). Hoje, o front mantém uma **cópia** de duas coisas:
- as **descrições** dos tipos (copiadas dos dados iniciais do BDA, branch `dev-data`, `apps/parametros`);
- as **exigências de cada tipo** (`CHANGE_TYPE_REQUIREMENTS`).

Se alguém mudar isso no back, a tela só mostra a regra nova depois de salvar.

**Onde:**
- `apps/sgmi/models.py` (`Parameter`): não tem campo de descrição. O BDA já modelou `descricao` no `ParametroBase`.
- `apps/sgmi/services.py` (`_serialize_parameter`) e `apps/sgmi/views.py` (`ParameterListView`): a lista `GET /sgmi/parametros/CHANGE_TYPE/` devolve só `id`, `category`, `code`, `name`, `active`.

**Sugestão:**
- incluir `description` no `Parameter`, com os textos do BDA;
- na lista de tipos, devolver `description` e `required_flags` (calculado por `calculate_required_flags`).

Assim o front passa a ler tudo da API.

**Impacto no front:** nenhum bloqueio. O RF-004 já funciona com a cópia (documentado no `RF004.md`). Com a mudança, basta trocar as constantes `TIPOS` do `includes/classificacao_mudanca.html` pela leitura da API.

---

## Avisos para o time (não são pedidos ao back)

- **Ambiente:** rodar o projeto com o **Python 3.13 da `.venv`**. Com o Python 3.14, o Django 5.1 quebra ao renderizar *inclusion tags* (`AttributeError: 'super' object has no attribute 'dicts'`). Isso afeta a tela "Nova Mudança" e o admin do Django.
- **Pergunta para quem define os requisitos:** o SGMI ainda não tem uma tela que **liste as mudanças**. Alguma RF prevê isso? Sem ela, só se chega aos detalhes de uma mudança pela URL ou por links de outras telas.
- **Pergunta para quem define os requisitos (RF-004, tarefa 3):** os critérios falam em "campos exclusivos das críticas" e "campos adicionais exigidos pelo tipo".
  - Hoje não existe nenhum campo específico por tipo, e o back exige os mesmos campos para todos.
  - Quais campos seriam esses? Vêm de outra RF (ex.: aprovação, risco, plano de reversão) ou o back vai criá-los?
  - Até haver definição, essa parte do RF-004 fica pendente.

---

## Histórico deste documento

| Versão | O que mudou |
|---|---|
| 1ª versão | Pedidos 1 a 4 e avisos para o time, levantados no RF-001 e no RF-002 |
| 2ª versão | Pedido 5 (descrição e exigências na lista de tipos) e pergunta sobre os campos por tipo, levantados no RF-004 |
