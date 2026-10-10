# RFs de Front-end — SGMI

Sistema de Gestão de Mudanças Institucionais da PMPB, desenvolvido pela Fábrica de Software Unipê.
Stack do front: templates Django 5.1 + AdminLTE 3 (Bootstrap 4, jQuery, toastr, Font Awesome). Cada tela é uma casca HTML, e o JS da página chama a API JSON via `fetch` (com `FormData` e o cabeçalho `X-CSRFToken`).

---

## Fluxo geral

Hoje a única situação de uma mudança no back é "Rascunho" (DRAFT).

---

## RF-001 — Registrar a mudança como rascunho

- **Objetivo:**
- **Responsável (front):** Gabriel
- **Status:**
- **Depende de:** back do RF-001 (já no `dev-front`); busca de responsáveis do `apps/portal` (`/portal/api/search-enjoyer-json/?search=...` ou o modal `render_enjoyer_selector`)
- **É usado por:** RF-007 e RF-008 (os dois fluxos terminam redirecionando para "Editar Rascunho")
- **Telas envolvidas:**
  - Nova Mudança Manual (`formulario_mudanca.html`), provisória, feita por Amanda
  - Editar Rascunho (`editar_rascunho.html`), provisória, feita por Amanda
- **Detalhes técnicos (back):**
  - Campos: título (até 200 caracteres), descrição, justificativa, resultado esperado, data pretendida (AAAA-MM-DD) e responsável (id de um Enjoyer ativo).
  - Salvar rascunho aceita campos vazios. Os 6 campos só são obrigatórios na validação para envio.
  - Cada salvamento sobrescreve todos os campos, então o que não for enviado vira vazio.
  - Criar rascunho: `POST /sgmi/mudancas/`
  - Reabrir e salvar rascunho: `GET` e `POST /sgmi/mudancas/<id>/rascunho/`
  - Validar para envio: `POST /sgmi/mudancas/<id>/validar-submissao/`
  - Listar tipos de mudança: `GET /sgmi/parametros/change_type/`
  - Respostas: sucesso `{message, data}`; erro de campo `{errors: {campo: [{message, code}]}}`; erro geral `{message}` ou `errors.__all__`.

### Tarefas e critérios de aceite

#### 1. Criar o formulário de cadastro
Objetivo: Disponibilizar a interface utilizada para registrar uma nova mudança institucional.
- O formulário pode ser acessado por usuário autorizado.
- Todos os campos do RF-001 estão disponíveis.
- Os campos possuem rótulos claros.
- A organização do formulário facilita o preenchimento.
- A tela não apresenta erros visuais ou funcionais.

#### 2. Exibir os campos obrigatórios
Objetivo: Informar claramente quais dados precisam ser preenchidos para a mudança avançar no fluxo.
- Os campos obrigatórios possuem identificação visual.
- A identificação não depende somente de cor.
- Campos vazios são destacados antes da submissão.
- A mensagem aparece próxima ao campo correspondente.
- A mensagem desaparece após a correção.

#### 3. Permitir selecionar o responsável
Objetivo: Permitir que o usuário escolha o responsável principal pela mudança.
- O campo apresenta usuários disponíveis para seleção.
- O usuário consegue localizar e selecionar um responsável.
- A identificação do responsável é enviada corretamente.
- Uma seleção inválida apresenta mensagem de erro.
- O responsável salvo aparece ao reabrir o rascunho.

#### 4. Permitir salvar e continuar editando
Objetivo: Permitir que o usuário salve o rascunho e retorne posteriormente para concluir o cadastro.
- Existe uma ação clara para salvar o rascunho.
- Os dados preenchidos são enviados ao Back-end.
- O sistema confirma o salvamento.
- O rascunho pode ser reaberto para edição.
- Os dados salvos são apresentados novamente.
- Alterações posteriores podem ser salvas.

#### 5. Exibir mensagens de erro e confirmação
Objetivo: Informar ao usuário o resultado das operações realizadas no formulário.
- O salvamento bem-sucedido exibe uma confirmação.
- Erros de validação aparecem junto aos campos correspondentes.
- Erros gerais são apresentados de forma compreensível.
- As mensagens não exibem informações técnicas internas.
- As mensagens podem ser percebidas por leitores de tela.

#### 6. Garantir responsividade e acessibilidade
Objetivo: Manter o formulário utilizável em desktop e dispositivos móveis, inclusive por usuários que utilizem recursos de acessibilidade.
- O formulário funciona nos tamanhos de tela homologados.
- Nenhum campo ou botão fica cortado ou sobreposto.
- A navegação por teclado segue uma ordem lógica.
- Todos os campos possuem rótulos associados.
- O foco dos elementos é visível.
- Textos e controles possuem contraste legível.

### Dúvidas em aberto
1. As rotas das telas não exigem login, o que afeta o critério "acesso por usuário autorizado".
2. Criar exige só login; reabrir e salvar exigem a permissão `sgmi.change_change`.
3. O back passou a exigir "tipo de mudança" (RF-004) na validação para envio, mas esse campo não é do RF-001. Não se sabe quem faz o front do RF-004.
4. Em "campos vazios destacados antes da submissão", submissão quer dizer salvar o rascunho ou um botão separado de validação/envio?

---

## RF-002 — Gerar identificador único

- **Objetivo:**
- **Responsável (front):**
- **Status:**
- **Depende de:** | **É usado por:**
- **Telas envolvidas:**

### Tarefas e critérios de aceite

#### 1. Exibir o identificador após o salvamento
Objetivo: Informar ao usuário o código atribuído à mudança criada.
- O identificador aparece após o primeiro salvamento.
- O valor exibido corresponde ao retornado pelo Back-end.
- O código permanece visível ao reabrir a mudança.
- O identificador não aparece vazio quando a criação é concluída.
- Falhas na geração apresentam mensagem compreensível.

#### 2. Bloquear a edição do identificador
Objetivo: Impedir que o usuário altere o código gerado automaticamente.
- O identificador é exibido somente para leitura.
- O usuário não consegue digitar ou substituir o valor.
- O campo não envia alterações ao Back-end.
- O bloqueio permanece durante todas as etapas da mudança.

#### 3. Exibir o identificador nos detalhes da mudança
Objetivo: Facilitar a identificação e a localização do registro durante seu acompanhamento.
- O código aparece na página de detalhes.
- O identificador possui posição visível e de fácil localização.
- O valor exibido corresponde à mudança consultada.
- O código permanece legível em desktop e dispositivos móveis.
- O identificador pode ser selecionado e copiado pelo usuário.

### Dúvidas em aberto

---

## RF-003 — Vincular a mudança a uma referência institucional

- **Objetivo:**
- **Responsável (front):**
- **Status:**
- **Depende de:** | **É usado por:**
- **Telas envolvidas:**

### Tarefas e critérios de aceite

#### 1. Criar a seção de referência institucional
Objetivo: Disponibilizar no formulário os campos necessários para vincular a mudança a uma iniciativa existente.
- A seção aparece no cadastro da mudança.
- Os campos possuem rótulos claros.
- O usuário consegue preencher todos os dados do vínculo.
- Os valores salvos aparecem ao reabrir o registro.
- A seção funciona em desktop e dispositivos móveis.

#### 2. Permitir selecionar o tipo da referência
Objetivo: Permitir que o usuário informe se o vínculo corresponde a um projeto, programa ou atividade.
- O campo apresenta as três opções permitidas.
- Apenas uma opção pode ser selecionada.
- O valor escolhido é enviado corretamente ao Back-end.
- A seleção salva reaparece ao editar a mudança.
- O campo apresenta mensagem quando estiver inválido.

#### 3. Permitir informar identificador e endereço
Objetivo: Coletar os dados necessários para localizar a iniciativa no sistema institucional.
- Existem campos para identificador e endereço.
- Os valores são enviados sem alterações indevidas.
- O endereço recebe validação básica de formato.
- Erros são exibidos próximos aos campos correspondentes.
- Os dados permanecem preenchidos após erro de validação.

#### 4. Exibir o acesso à referência
Objetivo: Permitir que o usuário abra a iniciativa vinculada no sistema institucional.
- O endereço é apresentado como link nos detalhes da mudança.
- O link corresponde ao endereço salvo.
- A referência é aberta sem substituir indevidamente a tela atual.
- O identificador e o tipo aparecem junto ao link.
- Endereços ausentes não geram links quebrados.

#### 5. Exibir mensagens de validação
Objetivo: Orientar o usuário quando os dados da referência estiverem ausentes ou incorretos.
- Campos inválidos recebem mensagens específicas.
- A mensagem aparece próxima ao campo correspondente.
- O sistema não apresenta somente uma mensagem geral.
- A mensagem desaparece após a correção.
- Os avisos podem ser identificados por leitores de tela.

### Dúvidas em aberto

---

## RF-004 — Classificar o tipo da mudança

- **Objetivo:**
- **Responsável (front):**
- **Status:**
- **Depende de:** | **É usado por:**
- **Telas envolvidas:**

### Tarefas e critérios de aceite

#### 1. Criar o campo de classificação
Objetivo: Permitir que o usuário selecione o tipo da mudança no formulário.
- O campo apresenta os tipos ativos.
- Apenas um tipo pode ser selecionado.
- A opção escolhida é enviada corretamente ao Back-end.
- O valor salvo reaparece ao editar a mudança.
- A submissão sem classificação apresenta mensagem de erro.

#### 2. Explicar os tipos de mudança
Objetivo: Ajudar o usuário a escolher a classificação adequada.
- Cada tipo possui uma explicação clara e resumida.
- As explicações diferenciam simples, planejada, crítica e emergencial.
- O conteúdo pode ser consultado sem sair do formulário.
- As orientações permanecem legíveis em dispositivos móveis.
- As informações são acessíveis por teclado e leitor de tela.

#### 3. Adaptar o formulário à classificação
Objetivo: Exibir somente os campos e controles necessários ao tipo selecionado.
- Mudanças simples não exibem campos exclusivos das críticas.
- Campos adicionais aparecem quando exigidos pelo tipo.
- Os dados já preenchidos não são perdidos sem aviso.
- A tela é atualizada após a alteração da classificação.
- As regras visuais correspondem às regras aplicadas pelo Back-end.

### Dúvidas em aberto
1. O back passou a exigir "tipo de mudança" na validação para envio do RF-001. Não se sabe quem faz o front do RF-004.

---

## RF-005 — Registrar o escopo afetado

- **Objetivo:**
- **Responsável (front):**
- **Status:**
- **Depende de:** | **É usado por:**
- **Telas envolvidas:**

### Tarefas e critérios de aceite

#### 1. Criar a seção de escopos afetados
Objetivo: Disponibilizar no formulário uma área para registrar os elementos impactados.
- A seção está disponível no cadastro da mudança.
- Os campos possuem rótulos claros.
- O usuário consegue cadastrar um escopo.
- Os escopos salvos aparecem ao reabrir a mudança.
- A seção funciona em desktop e dispositivos móveis.

#### 2. Permitir adicionar vários escopos
Objetivo: Permitir que o usuário informe mais de um elemento afetado pela mesma mudança.
- Existe uma ação para adicionar novos escopos.
- Cada escopo é exibido separadamente.
- A inclusão de um item não apaga os anteriores.
- O usuário consegue revisar todos os itens cadastrados.
- Erros em um item não apagam os demais.

#### 3. Permitir selecionar o tipo de escopo
Objetivo: Permitir a classificação do elemento como sistema, serviço, processo, dado, tecnologia ou unidade.
- O campo apresenta todas as categorias permitidas.
- Apenas uma categoria é selecionada por item.
- O valor escolhido é enviado corretamente.
- A seleção salva reaparece na edição.
- Valores não permitidos não são apresentados.

#### 4. Permitir editar e remover escopos
Objetivo: Possibilitar a correção ou remoção dos itens antes da submissão da mudança.
- Existe uma ação para editar cada item.
- Existe uma ação para remover cada item.
- A remoção solicita confirmação.
- O item atualizado é exibido corretamente.
- Os controles respeitam as permissões e a situação da mudança.

#### 5. Exibir os escopos nos detalhes da mudança
Objetivo: Apresentar de forma organizada tudo o que poderá ser afetado pela mudança.
- Todos os escopos associados são exibidos.
- Cada item apresenta tipo, nome e descrição.
- As informações correspondem aos dados armazenados.
- A lista permanece legível quando houver vários itens.
- A visualização funciona em desktop e dispositivos móveis.

### Dúvidas em aberto

---

## RF-006 — Anexar documentos e evidências

- **Objetivo:**
- **Responsável (front):**
- **Status:**
- **Depende de:** | **É usado por:**
- **Telas envolvidas:**

### Tarefas e critérios de aceite

#### 1. Criar o campo de envio de arquivos
Objetivo: Permitir que o usuário selecione documentos e evidências para anexar à mudança.
- Existe um controle para selecionar arquivos.
- O nome do arquivo selecionado é exibido.
- O arquivo é enviado corretamente ao Back-end.
- O controle funciona por teclado.
- A seleção funciona em desktop e dispositivos móveis.

#### 2. Informar formatos e tamanho máximo
Objetivo: Orientar o usuário sobre as regras dos arquivos antes do envio.
- Os formatos permitidos são exibidos.
- O tamanho máximo é informado.
- As orientações aparecem próximas ao campo.
- A informação permanece legível em dispositivos móveis.
- O conteúdo pode ser identificado por leitores de tela.

#### 3. Exibir o andamento e o resultado do envio
Objetivo: Informar ao usuário se o arquivo está sendo enviado e se a operação foi concluída.
- O sistema indica que o envio está em andamento.
- O usuário não dispara envios duplicados acidentalmente.
- O sucesso apresenta uma confirmação.
- A falha apresenta uma mensagem compreensível.
- O arquivo permanece disponível para nova tentativa quando necessário.

#### 4. Listar os arquivos anexados
Objetivo: Apresentar os documentos e evidências já relacionados à mudança.
- Todos os anexos autorizados são exibidos.
- Cada item apresenta nome, tipo, tamanho e data.
- A lista é atualizada após um novo envio.
- A ausência de arquivos apresenta mensagem adequada.
- A listagem permanece organizada com vários anexos.

#### 5. Permitir visualizar ou baixar arquivos
Objetivo: Disponibilizar o acesso aos anexos conforme as permissões do usuário.
- Cada anexo possui uma ação de acesso.
- O arquivo correto é solicitado ao Back-end.
- Usuários sem permissão não visualizam ações indevidas.
- Falhas no acesso apresentam mensagem adequada.
- O download preserva o nome correto do arquivo.

#### 6. Confirmar a remoção do anexo
Objetivo: Evitar a exclusão acidental de documentos ou evidências.
- A ação de remover aparece somente quando permitida.
- O sistema solicita confirmação antes da remoção.
- O cancelamento mantém o arquivo.
- A confirmação remove o item da listagem após o sucesso.
- Uma falha mantém o item e apresenta uma mensagem.
- A operação é acessível por teclado e leitor de tela.

### Dúvidas em aberto

---

## RF-007 — Criar mudança a partir de modelo

- **Objetivo:**
- **Responsável (front):** Amanda
- **Status:**
- **Depende de:** RF-001 (o fluxo termina redirecionando para "Editar Rascunho") | **É usado por:**
- **Telas envolvidas:**
  - Selecionar Modelo
  - Editar Rascunho (`editar_rascunho.html`)

### Tarefas e critérios de aceite

#### 1. Criar a opção “Iniciar por modelo”
Objetivo: Permitir que o usuário escolha utilizar um modelo ao iniciar uma mudança.
- A opção está disponível para usuários autorizados.
- O usuário consegue abrir a seleção de modelos.
- A escolha não impede a criação manual de uma mudança.
- A funcionalidade opera em desktop e dispositivos móveis.
- O controle é acessível por teclado.

#### 2. Listar os modelos disponíveis
Objetivo: Apresentar os modelos aprovados e ativos fornecidos pelo Back-end.
- A lista apresenta somente modelos disponíveis.
- Cada item exibe nome e descrição.
- O usuário consegue selecionar um único modelo.
- A ausência de modelos apresenta mensagem adequada.
- Modelos desativados não são exibidos.

#### 3. Exibir uma prévia do modelo
Objetivo: Permitir que o usuário confira os dados antes de criar a mudança.
- A prévia identifica o modelo selecionado.
- Os campos que serão preenchidos são apresentados.
- A prévia não mostra informações restritas.
- O usuário pode confirmar ou cancelar a utilização.
- O cancelamento não cria uma mudança.

#### 4. Preencher o formulário com o modelo
Objetivo: Apresentar no cadastro os dados reutilizáveis retornados pelo Back-end.
- O formulário recebe os campos autorizados.
- Os valores correspondem ao modelo escolhido.
- Campos não reutilizáveis permanecem vazios.
- Aprovações, decisões e auditoria não são exibidas.
- O modelo utilizado fica identificado.

#### 5. Permitir revisar e alterar os dados
Objetivo: Possibilitar que o usuário adapte as informações do modelo à nova mudança.
- Os campos reutilizados podem ser editados quando permitido.
- A alteração não modifica o modelo original.
- As validações do cadastro continuam sendo aplicadas.
- O usuário pode completar os campos vazios.
- Os dados revisados são enviados corretamente.

#### 6. Confirmar a criação pelo modelo
Objetivo: Informar que a nova mudança foi criada com base no modelo selecionado.
- O sucesso apresenta uma confirmação.
- A mensagem identifica que um modelo foi utilizado.
- A nova mudança é aberta como rascunho.
- O novo identificador é exibido.
- Falhas apresentam uma mensagem sem criar registros duplicados.

### Dúvidas em aberto

---

## RF-008 — Copiar dados de uma mudança anterior

- **Objetivo:**
- **Responsável (front):** Amanda
- **Status:**
- **Depende de:** RF-001 (o fluxo termina redirecionando para "Editar Rascunho") | **É usado por:**
- **Telas envolvidas:**
  - Copiar Mudança
  - Editar Rascunho (`editar_rascunho.html`)

### Tarefas e critérios de aceite

#### 1. Criar a opção “Copiar mudança anterior”
Objetivo: Permitir que o usuário inicie um cadastro utilizando dados de uma mudança existente.
- A opção está disponível para usuários autorizados.
- O usuário consegue acessar a pesquisa de mudanças.
- A opção não impede o cadastro manual.
- Nenhuma mudança é criada antes da confirmação.
- O controle funciona por teclado.

#### 2. Pesquisar e selecionar a mudança de origem
Objetivo: Permitir que o usuário localize e escolha o registro que servirá de base.
- A pesquisa aceita o identificador da mudança.
- Os resultados exibem identificador, título e situação.
- O usuário consegue selecionar uma mudança.
- Registros não autorizados não aparecem.
- A ausência de resultados apresenta mensagem adequada.

#### 3. Exibir os grupos disponíveis para cópia
Objetivo: Apresentar as categorias de informações que podem ser reutilizadas.
- Cada grupo copiável é apresentado separadamente.
- O usuário pode selecionar os grupos desejados.
- Dados restritos não aparecem entre as opções.
- A interface informa o conteúdo de cada grupo.
- A seleção permanece visível antes da confirmação.

#### 4. Informar os dados que não serão copiados
Objetivo: Evitar que o usuário espere o reaproveitamento de informações exclusivas da mudança anterior.
- A interface informa que o identificador não será copiado.
- Aprovações e decisões aparecem como não copiáveis.
- Auditoria, execução e encerramento aparecem como não copiáveis.
- A orientação é exibida antes da confirmação.
- A informação permanece legível em dispositivos móveis.

#### 5. Confirmar a operação de cópia
Objetivo: Permitir que o usuário revise a origem e os grupos selecionados antes da criação.
- A confirmação identifica a mudança de origem.
- Os grupos selecionados são apresentados.
- O usuário pode confirmar ou cancelar.
- O cancelamento não cria nenhum registro.
- A confirmação envia somente os grupos escolhidos.

#### 6. Abrir a nova mudança para revisão
Objetivo: Apresentar a nova mudança como rascunho para conferência e complementação.
- A nova mudança é aberta após a criação.
- O novo identificador é exibido.
- Os dados copiados aparecem nos campos corretos.
- Campos não selecionados permanecem vazios.
- O usuário pode editar e completar as informações.
- A mudança original permanece inalterada

### Dúvidas em aberto
