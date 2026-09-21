# Testes manuais — GreenV Sprint 3

## Objetivo

Este documento registra a validação dos principais fluxos do aplicativo GreenV entregue na
Sprint 3. Os testes confirmam a navegação entre as telas, a integração com a API publicada e o
funcionamento das operações executadas em campo.

## Ambiente de validação

| Item | Configuração |
|---|---|
| Aplicativo | GreenV em Flutter |
| Plataforma principal | iPhone com iOS |
| Serviço integrado | `https://greenvapi.matomomitsu.com` |
| Autenticação | Conta corporativa cadastrada |
| Dados | Dados reais retornados pela API |
| Evidência | [Vídeo de demonstração](https://youtube.com/shorts/irqRgWPfklU?feature=share) |

O aplicativo executado na validação não utiliza dados mockados. Os dublês existentes na pasta
`test/` são usados somente pelos testes automatizados e não fazem parte do aplicativo compilado.

## Resultados dos testes

| Nº | Cenário testado | Resultado esperado | Resultado obtido | Status |
|---:|---|---|---|:---:|
| 1 | Autenticar com uma conta corporativa válida | A API deve validar as credenciais, criar a sessão e abrir a página inicial. | A autenticação foi concluída e o dashboard foi exibido com a sessão ativa. | Passou |
| 2 | Consultar o dashboard operacional | A tela deve apresentar os indicadores, sessões recentes e o atalho para iniciar uma coleta. | Os dados retornados pela API foram carregados e os componentes da página responderam corretamente. | Passou |
| 3 | Iniciar e encerrar uma coleta de rota | O aplicativo deve abrir a câmera, acompanhar GPS, velocidade e sensores, segmentar a gravação e finalizar a sessão sem travar. | A coleta foi iniciada, a telemetria foi atualizada durante o percurso e a sessão foi encerrada corretamente. | Passou |
| 4 | Consultar e filtrar os trechos processados | A lista deve carregar os trechos da API e permitir a filtragem pelo nível da vegetação. | Os trechos foram apresentados e os filtros atualizaram a listagem conforme a classificação escolhida. | Passou |
| 5 | Abrir os detalhes e as evidências de um trecho | A tela deve mostrar altura, nível, medições e frames relacionados ao trecho selecionado. | O detalhe correto foi aberto e as informações e imagens vinculadas foram exibidas. | Passou |
| 6 | Visualizar os trechos no mapa | O mapa deve posicionar os trechos georreferenciados, aplicar as cores de classificação e permitir abrir um item. | Os marcadores foram carregados nas posições esperadas e o toque abriu o fluxo do trecho selecionado. | Passou |
| 7 | Criar uma ordem de serviço | O formulário deve aceitar prioridade, equipe, data e observações e enviar a ordem para a API. | A ordem foi enviada com sucesso e passou a aparecer na relação de ordens cadastradas. | Passou |
| 8 | Consultar ordens e equipes | As telas devem listar situação, prioridade e equipe das ordens, além da disponibilidade das equipes. | As duas listagens foram carregadas com os dados da API e permaneceram navegáveis. | Passou |
| 9 | Encerrar a sessão do usuário | O aplicativo deve apagar a sessão local e retornar para a tela de login. | O logout removeu a sessão armazenada e exibiu novamente a autenticação corporativa. | Passou |

## Resultado geral

Os nove cenários foram concluídos sem falhas críticas, travamentos ou interrupções na navegação.
Os fluxos principais e secundários avaliados estão funcionais e integrados à API publicada.

## Verificações automatizadas complementares

Além da validação manual, foram executadas as verificações abaixo:

| Verificação | Resultado |
|---|---|
| `flutter test` | 71 testes aprovados |
| `flutter analyze` | Nenhum apontamento |
| Compilação Android de depuração | APK gerado com sucesso |

Os testes automatizados cobrem autenticação, contratos HTTP, captura, telemetria, armazenamento
persistente, fila de upload, modelos de domínio, telas e navegação.
