### Bug 01
* **Arquivo:** `lib/models/ocorrencia.dart`
* **Sintoma:** O `autorId` do objeto ficava igual ao `autorNome`.
* **Causa:** Leitura do campo errado no `fromDoc`.
* **Correção:** Alterado `autorId: d['autorNome']` para `autorId: d['autorId']`.
* **Teste:** Confirmado abrindo o objeto e checando as permissões do usuário.

---

### Bug 02
* **Arquivo:** `lib/models/ocorrencia.dart`
* **Sintoma:** O status de "resolvido" aparecia invertido nos cards da lista.
* **Causa:** Negação lógica `!(d['resolvido'])` no método `fromDoc`.
* **Correção:** Removida a negação `!`.
* **Teste:** Cadastrado objeto e verificado status inicial "Em aberto".

---

### Bug 03
* **Arquivo:** `lib/pages/central_page.dart`
* **Sintoma:** O filtro de objetos filtrava ao contrário e o switch piscava/invertia estado.
* **Causa:** Lógica de toggle `!value` invertida e filtro `.where((o) => o.resolvido)` buscando resolvidos em vez de abertos.
* **Correção:** Ajustado `onChanged` para receber `value` e o filtro para `.where((o) => !o.resolvido)`.
* **Teste:** Marcado o switch e confirmado que exibe apenas itens não resolvidos.

---

### Bug 04
* **Arquivo:** `lib/pages/central_page.dart`
* **Sintoma:** A tela de detalhes abria sem os dados do objeto selecionado.
* **Causa:** Instanciação do `DetalhePage()` sem passar o parâmetro `ocorrencia: o`.
* **Correção:** Alterado para `DetalhePage(ocorrencia: o)`.
* **Teste:** Clicado em diferentes itens e validado se as informações abertas no detalhe correspondiam ao card.

---

### Bug 05
* **Arquivo:** `lib/pages/central_page.dart`
* **Sintoma:** O diálogo de cadastro permanecia aberto após clicar em "Registrar".
* **Causa:** Ausência de `Navigator.pop(context)` ao concluir com sucesso o cadastro.
* **Correção:** Adicionado `if (mounted) Navigator.of(context).pop();` após o `service.registrar()`.
* **Teste:** Cadastrado um item e verificado fechamento automático do modal.

---

### Bug 06
* **Arquivo:** `lib/pages/detalhe_page.dart` / `lib/services/central_service.dart`
* **Sintoma:** O botão "Marcar como resolvido" não aparecia para o autor da postagem.
* **Causa:** O `autorId` salvo no Firestore recebia o e-mail em vez do `uid` do usuário.
* **Correção:** Corrigido no `CentralService` para gravar `u.uid`.
* **Teste:** Logado na conta do autor e verificado aparecimento do botão na tela de detalhes.

---

### Bug 07
* **Arquivo:** `lib/pages/detalhe_page.dart`
* **Sintoma:** O autor não conseguia alterar o status pois o app tratava inconsistência de permissão na verificação `dono`.
* **Causa:** Comparação do `currentUser?.uid` falhava devido ao erro do Bug 06.
* **Correção:** Sincronizado o uso de `u.uid` no modelo e serviços.
* **Teste:** Testado alternar entre "Marcar como resolvido" e "Reabrir".

---

### Bug 08
* **Arquivo:** `lib/pages/detalhe_page.dart`
* **Sintoma:** Pistas não eram enviadas quando o campo continha texto.
* **Causa:** Condicional `if (pista.text.trim().isNotEmpty) return;` abortava o envio se houvesse texto.
* **Correção:** Corrigido para `if (pista.text.trim().isEmpty) return;`.
* **Teste:** Digitada uma mensagem e clicado em enviar.

---

### Bug 09
* **Arquivo:** `lib/pages/detalhe_page.dart`
* **Sintoma:** As pistas gravadas no banco ficavam em branco.
* **Causa:** Chamada `service.enviarPista(widget.ocorrencia.id, '')` passando string vazia estática.
* **Correção:** Alterado para passar `pista.text`.
* **Teste:** Enviada uma pista e conferida a mensagem renderizada na lista.

---

### Bug 10
* **Arquivo:** `lib/services/central_service.dart`
* **Sintoma:** Inconsistência no ID do usuário nos registros do Firestore.
* **Causa:** Uso de `u.email` no campo `'autorId'`.
* **Correção:** Substituído por `'autorId': u.uid`.
* **Teste:** Verificado documento criado no Firestore.

---

### Bug 11
* **Arquivo:** `lib/services/central_service.dart`
* **Sintoma:** Pistas enviadas em uma ocorrência apareciam em outra ou sumiam.
* **Causa:** Caminho fixo no Firestore `'ocorrencias/pistas/pistas'` em vez do ID dinâmico da ocorrência.
* **Correção:** Corrigido para `_db.collection('ocorrencias').doc(ocorrenciaId).collection('pistas')`.
* **Teste:** Enviadas pistas na Conta B e verificado surgimento na ocorrência correspondente.

---

### Bug 12
* **Arquivo:** `lib/services/central_service.dart`
* **Sintoma:** Ao clicar para alterar status, o valor no Firestore não mudava.
* **Causa:** O método `alterarStatus` enviava `'resolvido': item.resolvido` mantendo o mesmo valor.
* **Correção:** Alterado para `'resolvido': !item.resolvido`.
* **Teste:** Alterado status no aplicativo e confirmado no banco e na interface.