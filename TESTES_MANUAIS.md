# Testes — GreenV (Sprints 3 e 4)

## Objetivo

Este documento registra a validação dos fluxos do aplicativo GreenV. A Sprint 3 validou os
caminhos de sucesso em um aparelho real. Na Sprint 4, atendendo à avaliação da sprint anterior,
foram acrescentados os **cenários de erro**: sem rede, credenciais inválidas, serviço
indisponível, sessão expirada, listas vazias, ordem recusada pela API e permissão de câmera negada.

## Ambiente de validação

| Item | Configuração |
|---|---|
| Aplicativo | GreenV em Flutter, versão 1.1.0 (build 2) |
| Plataformas | iPhone com iOS (Sprint 3) e APK Android de release (Sprint 4) |
| Serviço integrado | `https://greenvapi.matomomitsu.com` |
| Autenticação | Conta corporativa cadastrada na API |
| Dados | Dados reais retornados pela API |
| Evidência em vídeo | [Demonstração no YouTube](https://youtube.com/shorts/irqRgWPfklU?feature=share) |

O aplicativo testado não usa dados mockados. Os dublês da pasta `test/` são usados somente pelos
testes automatizados e não fazem parte do aplicativo compilado.

## 1. Fluxos principais (caminho de sucesso)

| Nº | Cenário testado | Resultado esperado | Resultado obtido | Status |
|---:|---|---|---|:---:|
| 1 | Autenticar com uma conta corporativa válida | A API valida as credenciais, cria a sessão e abre a página inicial. | A autenticação foi concluída e o dashboard foi exibido com a sessão ativa. | Passou |
| 2 | Consultar o dashboard operacional | A tela apresenta os indicadores, as sessões recentes e o atalho para iniciar uma coleta. | Os dados da API foram carregados e os componentes da página responderam corretamente. | Passou |
| 3 | Iniciar e encerrar uma coleta de rota | O app abre a câmera, acompanha GPS, velocidade e sensores, segmenta a gravação e finaliza a sessão sem travar. | A coleta foi iniciada, a telemetria foi atualizada durante o percurso e a sessão foi encerrada corretamente. | Passou |
| 4 | Consultar e filtrar os trechos processados | A lista carrega os trechos da API e permite filtrar pelo nível da vegetação. | Os trechos foram apresentados e os filtros atualizaram a lista conforme a classificação escolhida. | Passou |
| 5 | Abrir os detalhes e as evidências de um trecho | A tela mostra altura, nível, medições e frames do trecho selecionado. | O detalhe correto foi aberto, com as informações e imagens do trecho. | Passou |
| 6 | Visualizar os trechos no mapa | O mapa posiciona os trechos georreferenciados, aplica as cores de classificação e permite abrir um item. | Os marcadores apareceram nas posições esperadas e o toque abriu o trecho selecionado. | Passou |
| 7 | Criar uma ordem de serviço | O formulário aceita prioridade, equipe, data e observações e envia a ordem para a API. | A ordem foi enviada e passou a aparecer na lista de ordens. | Passou |
| 8 | Consultar ordens e equipes | As telas listam situação, prioridade e equipe das ordens, além da disponibilidade das equipes. | As duas listas foram carregadas com os dados da API e permaneceram navegáveis. | Passou |
| 9 | Encerrar a sessão do usuário | O app apaga a sessão local e volta para a tela de login. | O logout removeu a sessão armazenada e exibiu novamente o login. | Passou |

## 2. Cenários de erro, sem rede e listas vazias (Sprint 4)

Cada cenário abaixo tem um teste automatizado correspondente, que monta a tela real do app,
provoca a falha (rede cortada, resposta de erro da API, lista vazia, câmera recusada) e confere a
mensagem exibida. O **resultado obtido** é o desses testes. A coluna **Como reproduzir no
aparelho** é o roteiro para repetir a verificação manualmente com o APK instalado.

| Nº | Cenário testado | Como reproduzir no aparelho | Resultado esperado | Resultado obtido | Teste automatizado | Status |
|---:|---|---|---|---|---|:---:|
| 10 | Login com senha errada | Digitar um e-mail válido e uma senha incorreta | Permanecer no login com “E-mail ou senha inválidos.”, sem dizer qual dos dois está errado | Mensagem exibida e o app permaneceu no login | `login_screen_test`: *stays on the login screen and explains a refusal* | Passou |
| 11 | Login sem internet | Ativar o modo avião e tocar em **Entrar** | “Sem conexão com o servidor. Verifique a rede e tente novamente.”, com o botão liberado para tentar de novo | Mensagem exibida; o botão voltou a ficar habilitado | `error_states_test`: *says the server is unreachable when there is no network* | Passou |
| 12 | Serviço de autenticação fora do ar | API responde 503 no endpoint de token | “O serviço de autenticação está indisponível…” | Mensagem exibida | `error_states_test`: *says the identity provider is down on a 503* | Passou |
| 13 | Dashboard sem internet e recuperação | Abrir o app em modo avião, desativar o modo avião e tocar em **Tentar de novo** | Cartão “Não foi possível carregar” com “Sem resposta da API…”; o atalho de coleta continua disponível; ao tentar de novo, os indicadores aparecem | Erro exibido sem travar a tela; após a nova tentativa os indicadores carregaram | `error_states_test`: *without network the home screen offers a retry that works* | Passou |
| 14 | API devolve erro com mensagem própria | API fora do ar (503) ao abrir **Trechos** | O cartão de erro mostra o texto devolvido pela API e o botão **Tentar de novo** | Texto da API e botão exibidos | `operations_screens_test`: *shows the API's words and a retry when it cannot load* | Passou |
| 15 | Sessão expirada ou revogada | Revogar a sessão no servidor e abrir **Ordens** | “Sua sessão terminou. Entre de novo.” e, se a renovação falhar, retorno ao login | Mensagem exibida; com a renovação recusada, o app voltou ao login | `error_states_test`: *an expired session says so…* e `login_screen_test`: *a lost session sends the person back to sign in* | Passou |
| 16 | Listas vazias | Conta sem coletas, ordens ou equipes | Cada tela explica o que falta: “Nenhum trecho medido ainda.”, “Nenhuma leitura com esse filtro.”, “Nenhum trecho medido com posição ainda.”, “Nenhuma ordem aberta ainda…”, “Nenhuma equipe cadastrada.” | Todas as mensagens apareceram, sem tela em branco | `error_states_test`: *empty lists say what is missing on every screen* | Passou |
| 17 | Filtro sem resultados | Escolher um nível sem trechos | “Nenhuma leitura com esse filtro.” | Mensagem exibida | `error_states_test` (mesmo teste) | Passou |
| 18 | Ordem recusada pela API | Enviar a ordem com a API recusando o pedido (422) | O formulário continua aberto, mostra a mensagem da API e nenhuma ordem é criada | Formulário aberto com a mensagem; nenhuma ordem registrada | `error_states_test`: *a refused order keeps the form open with the API words* | Passou |
| 19 | Ordem para trecho sem altura medida | Abrir um trecho “Vegetação vista, sem altura” | Botão **Abrir ordem de serviço** desabilitado, com a explicação | Botão desabilitado e explicação exibida | `operations_screens_test`: *refuses to open an order on a stretch nobody could measure* | Passou |
| 20 | Coleta sem internet e reenvio | Gravar uma rota em modo avião, encerrar, desativar o modo avião | Durante o modo avião: “1 segmento na fila”; com a rede de volta, a fila é enviada e aparece “Tudo enviado” | Segmento mantido na fila e enviado quando a conexão voltou | `error_states_test`: *a capture made offline waits in the queue…* e `queue_uploader_test` | Passou |
| 21 | Permissão de câmera negada | Negar a permissão de câmera e tocar em **Iniciar coleta** | Mensagem clara (“Permissão da câmera negada. Libere o acesso…”), sem texto técnico, e o botão continua disponível | Mensagem em português exibida; o botão permaneceu habilitado | `error_states_test`: *a refused camera permission is explained…* | Passou |
| 22 | GPS impreciso durante a coleta | Gravar em local fechado ou com a localização desligada | Aviso de que, nessa precisão, a coleta não gera um trecho medível; o aviso some quando o sinal melhora | Aviso exibido e removido com o sinal recuperado | `capture_app_test`: *warns while recording that the GNSS cannot measure a trecho* | Passou |
| 23 | App vai para segundo plano durante a coleta | Gravar e apertar o botão de início do aparelho | O segmento atual é fechado e salvo na fila; a gravação não continua escondida | Segmento salvo e a coleta encerrada | `capture_coordinator_test`: *backgrounding closes the current segment without restarting* | Passou |
| 24 | Abrir tela protegida sem login | Abrir o app sem sessão salva | Toda tela, exceto abertura e login, redireciona para o login | Redirecionamento confirmado | `login_screen_test`: *refuses to open a screen behind the guard without a session* | Passou |
| 25 | Fechar o teclado tocando fora do campo | Tocar no campo de e-mail e depois no fundo da tela | O teclado fecha e o campo perde o foco; os campos continuam respondendo ao toque | Teclado fechado e foco removido | `login_screen_test`: *a tap outside the fields puts the keyboard away* | Passou |

### Execução do APK final no Android

O APK de release (`GreenV-1.1.0.apk`) foi instalado em um emulador Android (Android 16, API 36) e
testado contra a API publicada:

| Verificação | Resultado |
|---|---|
| Instalação com `adb install` | Concluída, sem erros |
| Abertura do app | Tela de login exibida, sem travamentos nem erros no log do aplicativo |
| Toque fora dos campos com o teclado aberto | O campo perde o foco e o teclado fecha (correção da Sprint 4) |
| Cenário 10, senha errada | A API recusou e o app exibiu “E-mail ou senha inválidos.” |
| Cenário 11, modo avião | O app exibiu “Sem conexão com o servidor. Verifique a rede e tente novamente.” |

| Senha errada (API real) | Sem internet (modo avião) |
|:---:|:---:|
| <img src="docs/screenshots/error-login-invalid.jpg" alt="Login recusado pela API" width="250"> | <img src="docs/screenshots/error-login-offline.jpg" alt="Login sem conexão" width="250"> |

## Correções feitas a partir destes cenários

Escrever os cenários de erro e usar o APK revelou três defeitos, corrigidos na Sprint 4:

1. **Erro não tratado sem rede no dashboard e em Ordens.** As duas telas fazem duas requisições
   em paralelo e aguardavam uma de cada vez. Sem rede, as duas falhavam e o erro da segunda ficava
   sem tratamento. Agora as duas são aguardadas juntas (`both()` em `remote_data.dart`) e a tela
   mostra só o cartão de erro.
2. **Mensagem técnica na falha de câmera.** O app exibia o texto cru da exceção
   (`CameraException(CameraAccessDenied, …)`). Agora as falhas de câmera e de armazenamento são
   traduzidas para mensagens que o operador consegue resolver (`capture_error_message.dart`).
3. **Teclado preso no login.** No celular, tocar fora do campo não fechava o teclado, que ficava
   sobre o botão **Entrar**. Agora um toque em qualquer área que não seja um campo, ou arrastar a
   tela, fecha o teclado em todo o app (`capture_app.dart`, `auth_layout.dart`, `order_form.dart`).

## Resultado geral

Os 9 fluxos principais e os 16 cenários de erro e usabilidade foram concluídos sem falhas críticas, travamentos
ou interrupções na navegação.

## Verificações automatizadas

| Verificação | Resultado |
|---|---|
| `flutter test` | **83 testes aprovados** (71 na Sprint 3 + 12 na Sprint 4: erros, mensagens e teclado) |
| `flutter analyze` | Nenhum apontamento |
| `dart format --set-exit-if-changed` | Nenhuma alteração pendente |
| `flutter build apk --release` | APK gerado com sucesso, apontando para a API publicada |

Os testes cobrem autenticação, contratos HTTP, captura, telemetria, armazenamento persistente,
fila de upload, modelos de domínio, telas, navegação e os estados de erro, vazio e sem rede.
