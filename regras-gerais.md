# Regras gerais do front — SGMI

Sistema de Gestão de Mudanças Institucionais da PMPB, desenvolvido pela Fábrica de Software Unipê.
Repositório: `C:\Users\brgab\Desktop\SGMI\ex2026_2_gestao_mudancas_douglas-andrade` (GitLab).
Esta pasta de documentação fica **fora** do repositório.

---

## 1. Papel e limites

- Você faz o **front**. Pode alterar só:
  - templates (`**/templates/**/*.html`, inclusive `base/templates/base.html`, `sidebar.html` e `login.html`);
  - arquivos estáticos em `base/static/` (CSS, JS, imagens).
- **Nunca** alterar arquivos de back: qualquer `.py` (models, views, `urls.py`, `forms.py`, services, settings, migrations, testes, admin, templatetags, comandos), o `db.sqlite3` e o `requirements.txt`.
- Se a tarefa só fecha com uma mudança no back (ex.: rota nova, ajuste de view), parar, explicar o que falta e registrar no `pendencias.md` para levar ao back.
- Problema no código de colegas **não é corrigido**: vai para o `pendencias.md`.
- **Commit, push e merge: só você faz.** A IA entrega os comandos prontos. Em merge, usar `git merge --no-commit`, resolver, testar e parar antes do commit.
- As branches são criadas por você.
- Ao responder sobre situação ("o que foi feito", "o que falta"), falar do front. Itens de back só entram quando bloqueiam ou liberam o front.

## 2. Time

Segundo os nomes das branches e os commits:

| Área | Pessoa | RFs |
|---|---|---|
| Front | **Gabriel (você)** | RF-001 a RF-005, RF-009, RF-010, RF-021 |
| Front | Amanda Voss | RF-006, 007, 008, 011, 012, 019, 042, 043 |
| Front | Vanessa Lino | RF-013, RF-014; reorganização das pastas de templates (`e74ca4b`) |
| Back | Vicenzo Benelli | RF-001, 009, 010, 019 (e vários ajustes no `dev-back`) |
| Back | Rodrigo Ono Galvão | RF-021, RF-022 |
| Back | Jadson / Camilly | RF-015, 016 / RF-004, 017 |
| BDA | Max Samuel / Kauã Oliveira | estrutura de dados (`*/bda-*`) |

- Branches de trabalho: `rfXXX/front-nome`, `rfXXX/back-nome`, `rfXXX/bda-nome`.
- Integração: `dev-front`, `dev-back`, `dev-data`.
- A Amanda e a Vanessa mexem nas mesmas telas que você. Mantenha o que elas fizeram funcionando.

## 3. Stack e padrão da API

- Templates do Django 5.1 + AdminLTE 3 (Bootstrap 4, jQuery, toastr, Font Awesome). Não usar React, Vue, Tailwind, TypeScript nem Vite.
- Cada tela é uma casca HTML. O JavaScript da página chama a API JSON com `fetch`, usando `FormData` e o cabeçalho `X-CSRFToken`.
- Respostas da API:
  - sucesso: `{message, data}`;
  - erro de campo: `{errors: {campo: [{message, code}]}}`;
  - erro geral: `{message}` ou `errors.__all__`;
  - 403 e 404 podem vir **em HTML**, não em JSON.
- Situações da mudança: `DRAFT` (Rascunho) e `SUBMITTED` (Submetida).
  - Só rascunho aceita alterações (409 fora disso: "Apenas mudanças em rascunho podem ser alteradas.").
  - A edição não abre mudança submetida (409); a consulta é pela página de detalhes.
- Visibilidade: a mudança precisa estar visível para a pessoa (criador, responsável, alguém da Matriz RACI ou a permissão `view_all_changes`).
- Cada seção tem as próprias permissões (`view_`, `add_`, `change_`, `delete_` + modelo). Todas exigem também `view_change`.
- Rotas das telas:
  - nova, editar, copiar, selecionar modelo e histórico são `TemplateView` **sem login** (adiado; `pendencias.md`, item 18);
  - detalhes (`interface_detalhe_mudanca`) exige login, `view_change` e visibilidade.

## 4. Fluxo de cada RF

1. Desenvolver as RFs em ordem crescente.
2. **Sempre reler os critérios** da RF (`requisitos/`) antes de começar **e** antes de fechar.
3. Reler o back da RF (endpoints, campos, regras, situação, permissões) e registrar o que encontrou.
4. Apresentar o plano e esperar o "pode fazer".
5. Desenvolver só no front, seguindo o `regras/padroes-front.md`.
6. Testar todos os critérios (seção 5).
7. Checar choque com as RFs anteriores e com as telas dos colegas.
8. Escrever `entregas/RFxxx.md` (síntese, no formato das outras) e, se precisar registrar os testes em detalhe, `entregas/RFxxx-testes.md`.
9. Atualizar o `pendencias.md` e deixar os comandos de commit para você.

## 5. Testes

- **Nunca** usar o `db.sqlite3` do projeto: servidor temporário com banco descartável, fora do projeto.
- Três camadas:
  - API, pelo cliente de teste do Django;
  - navegador real (Chrome headless controlado por script, via CDP);
  - suíte do back: `python manage.py test apps.sgmi apps.portal`.
- Responsividade: 360, 768 e 1366px, sem rolagem horizontal e sem nada cortado.
- Contraste ≥ 4,5:1 nos temas escuro e claro.
- **Foco visível:** medir um sinal real (contorno maior que 0, sombra maior que 0 ou borda que muda), navegando com Tab. O AdminLTE zera o anel de foco dos botões (sombra de 0px), e isso gerou um falso positivo nos relatórios do RF-001 ao RF-005.
- **Choque:** rodar os roteiros antigos num `git archive` do commit anterior e na versão atual, e comparar as linhas OK/FALHA.
- Para testar o envio, criar no banco de teste os papéis da Matriz RACI (não vêm nas migrações; `pendencias.md`, item 7).
- Situação conhecida da suíte do back: 1 falha que não é sua (`pendencias.md`, item 14).

## 6. Ambiente

- Rodar pelo **Python 3.13 da `.venv`** do repositório. Com o Python 3.14 global, o Django 5.1 quebra nas *inclusion tags* (`AttributeError: 'super' object has no attribute 'dicts'`), o que derruba a tela "Nova Mudança" e o admin.
- Teste manual: entrar como `Admin`.
- Os "tamanhos de tela homologados" não estão definidos no requisito; usamos 360, 768 e 1366px.

```powershell
cd C:\Users\brgab\Desktop\SGMI\ex2026_2_gestao_mudancas_douglas-andrade
.\.venv\Scripts\Activate.ps1
python manage.py runserver
```

## 7. Commits (modelo)

```powershell
git status                          # conferir que só aparecem templates
git add <arquivos do front da RF>
git commit -m "feat(front): <o que foi feito> (RF-XXX)"
git push                            # envia para origin/<sua branch>
```

Depois, abrir o Merge Request da sua branch para o `dev-front` no GitLab.
