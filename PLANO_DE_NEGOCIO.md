# Plano de negócio — GreenV

> Medir a vegetação das faixas de domínio a partir de um celular em um veículo em movimento,
> para que a Motiva mande a equipe de roçada para onde ela é necessária, e não para onde o
> calendário manda.

Este documento faz parte da entrega da Sprint 4. Os números de custo vêm do repositório principal
do projeto ([GreenV](https://github.com/henrikmm/GreenV)) e da apresentação de pitch do grupo. Os
números de mercado vêm de fontes públicas citadas ao longo do texto. **O que é medição está
indicado como medição, e o que é premissa está indicado como premissa**, para ser validado no piloto.

---

## 1. O problema no contexto da Motiva

A Motiva administra **5.044 km de rodovias concessionadas** (divulgação de resultados do
2º trimestre de 2026). Em toda essa malha a vegetação da faixa de domínio cresce o ano inteiro, e
mais rápido na estação chuvosa. Quando cresce demais, ela:

- **encobre placas e sinalização vertical**, reduzindo o tempo de reação do motorista;
- **fecha o acostamento e as áreas de escape**, onde um veículo avariado precisa parar;
- **obstrui dispositivos de drenagem**, acelerando a deterioração do pavimento;
- **aumenta o risco de incêndio** em períodos secos.

Por isso, os contratos de concessão definem obrigações de conservação da faixa de domínio, e cada
Programa de Exploração da Rodovia (PER) define essas obrigações de um jeito. O Ministério Público
Federal, por exemplo, já cobrou a roçada de toda a faixa, e não apenas dos quatro metros junto à
pista, em um contrato federal ([MPF, BR-050](https://www.mpf.mp.br/o-mpf/unidades/pr-mg/noticias/mpf-pede-rocada-total-na-br-050-contra-fumaca-de-queimadas-e-risco-de-acidentes)).
Ou seja: a roçada é um item **regulatório**, e não só estético.

Hoje a decisão de onde roçar se apoia em duas coisas:

1. **Calendário fixo**: a equipe passa a cada N semanas, haja ou não vegetação alta. Em trechos
   que cresceram pouco, o serviço é pago sem necessidade.
2. **Inspeção visual**: alguém percorre a rodovia, olha a margem e anota. A avaliação é subjetiva,
   não deixa evidência auditável e não cabe em centenas de quilômetros com frequência semanal.

O resultado é um custo recorrente alto, que poderia ser mais bem distribuído, e pouca evidência
para mostrar à agência reguladora que um trecho estava dentro do padrão em determinada data.

## 2. Proposta de valor

**GreenV transforma uma passagem comum de veículo pela rodovia em uma lista de trechos ordenada
pela altura da vegetação, com a foto que justifica cada leitura.**

| Para quem | O que entrega |
|---|---|
| Supervisor de conservação | Mapa e lista de trechos de ~25 m classificados por altura (< 10 cm, 10–30 cm, > 30 cm), com o quadro de vídeo como evidência, e abertura da ordem de serviço no próprio trecho |
| Operador de campo | Um aplicativo de um botão: grava vídeo, GPS e sensores, funciona sem sinal e só apaga o arquivo depois que a nuvem confirma o recebimento |
| Gestão de contrato / engenharia | Histórico datado e georreferenciado de cada trecho para auditoria, indicadores de conservação e planejamento do orçamento de roçada |
| Equipe de roçada (própria ou terceirizada) | Ordens de serviço com prioridade, área, nível e localização, em vez de rotas fixas |

Em uma frase para a Motiva: **roçar menos onde não precisa, roçar antes onde é crítico e provar
as duas coisas.**

## 3. Personas e público-alvo

### Personas

**Carla, supervisora de conservação rodoviária** (usuária principal do painel e do app)
- Responsável por cerca de 150 km. Recebe reclamações de usuários e cobranças da agência.
- *Dor:* decide a programação da roçada com base em planilhas e relatos verbais; não sabe onde a
  vegetação passou do limite até alguém reclamar.
- *Ganho com o GreenV:* abre o mapa na segunda-feira, filtra os trechos acima de 30 cm e emite as
  ordens da semana em minutos.

**Marcos, inspetor de tráfego** (quem coleta)
- Já percorre a rodovia diariamente na viatura de inspeção.
- *Dor:* não pode parar a cada quilômetro para avaliar a margem, e preencher formulários dirigindo
  é inseguro.
- *Ganho:* o celular fica no suporte e grava sozinho. Ele não precisa digitar nada, e sem sinal a
  coleta fica guardada até a rede voltar.

**Rafael, gerente de contrato** (decisor econômico)
- Responde pelo orçamento de conservação e pelos indicadores contratuais da concessão.
- *Dor:* o custo de roçada é grande e recorrente, e é difícil justificar ou contestar medições sem
  evidências.
- *Ganho:* relatório por trecho e por data, com foto e altura, e uma base para renegociar a
  frequência de roçada com dados.

### Público-alvo

| Segmento | Descrição | Prioridade |
|---|---|---|
| **Motiva, concessões rodoviárias** | 5.044 km. Primeiro cliente, com um piloto no Rodoanel Oeste (SP-021), que já está mapeado no GreenV (km 0–29,3) | Alta, cliente âncora |
| Outras concessionárias rodoviárias | Operadoras de rodovias estaduais e federais com as mesmas obrigações de conservação | Média, expansão após o piloto |
| Prestadoras de serviço de roçada | Empresas terceirizadas que precisam comprovar o serviço executado | Média, canal de parceria |
| Órgãos públicos (DER, DNIT) | Rodovias não concedidas | Baixa, ciclo de venda longo |

## 4. A solução e o que já funciona

| Camada | Estado na Sprint 4 |
|---|---|
| App de coleta (Flutter, este repositório) | **Funcionando.** APK Android, segmentos de 10 s com GPS e sensores, fila offline persistente, login corporativo |
| API e filas (Spring Boot no Azure Container Apps) | **No ar** em `greenvapi.matomomitsu.com`, com escala até zero quando não há uso |
| Reconstrução 3D e medição (GPU sob demanda no RunPod) | **Executa de ponta a ponta.** Em 11/09/2026, quatro segmentos foram do upload à medição sem intervenção, usando de 3,4 a 24,8 s de GPU cada |
| Painel web (Cloudflare Pages) | **No ar** em `greenv.matomomitsu.com`: sessões, trechos, mapa, ordens e equipes |
| Acurácia com seleção manual | **Medida:** erro absoluto médio de 0,91 cm em 6 ensaios na rodovia e de 2,11 cm em 32 ensaios no total (12 alvos, 6 vídeos) |
| Acurácia da medição automática | **Ainda não comparada com trena.** É o primeiro objetivo do piloto |

O GreenV já usa os **642 polígonos de trechos fornecidos pela Motiva (KMZ)** no painel de
demonstração. A integração com a forma como a Motiva já divide a malha está encaminhada.

## 5. Diferenciais competitivos

| Alternativa | Limitação | Vantagem do GreenV |
|---|---|---|
| Inspeção visual | Subjetiva, sem evidência, não escala | Altura em centímetros, foto por trecho, histórico datado |
| Mapeamento móvel com LiDAR | Veículo dedicado, equipamento caro e operador especializado | Celular comum em um suporte de R$ 20 a R$ 110; usa a viatura que já circula |
| Drones | Restrições de voo (ANAC/DECEA), linha de visada e pouca autonomia para 5.044 km | Coleta contínua em velocidade de rodovia, sem piloto nem autorização de voo |
| Imagem de satélite | Vista de cima, com resolução e revisita insuficientes para medir 30 cm de altura na margem; nuvens na estação chuvosa | Vista lateral, ao nível da pista, a cada passagem |

Diferenciais estruturais:

1. **Custo de captura quase zero.** As concessionárias já mantêm viaturas de inspeção de tráfego
   circulando pela malha. O GreenV aproveita esse trajeto e não exige uma rota nova.
2. **Evidência auditável.** Cada leitura guarda o quadro de vídeo, a posição GPS e a data. Isso
   serve para a operação e para a fiscalização.
3. **Funciona offline.** Trechos sem cobertura celular atrasam o envio, mas não perdem a coleta.
4. **Arquitetura modular.** A entrada pode ser celular, câmera fixa ou, no futuro, LiDAR. A nuvem
   pode ser Azure, AWS ou outra, sem reescrever a aplicação.
5. **Base para previsão.** Cada passagem alimenta um histórico por trecho. O módulo de previsão de
   crescimento (Random Forest, ainda testado só com dados simulados do Rodoanel Oeste e clima real
   de 2023 a 2026) é o próximo passo para antecipar a roçada.

## 6. Modelo de receita

Cobrança **por quilômetro processado**: cada quilômetro de vídeo que passa pela medição, em um
sentido. É simples de explicar, acompanha o uso real (mais passagens na estação chuvosa, menos na
seca) e fica bem acima do custo de processamento, de ≈ R$ 3,51 por km. A ele se somam um piloto
pago e serviços opcionais.

| Fonte de receita | Como funciona | Preço proposto |
|---|---|---|
| **Piloto pago** | 3 meses no Rodoanel Oeste (29,3 km, dois sentidos): implantação, cadastro de usuários, calibração com trena em campo, treinamento e operação assistida | R$ 45.000 (valor fechado) |
| **Processamento GreenV** | App, painel, medição de cada km gravado, armazenamento de 12 meses e suporte em horário comercial. Faturado pelo total de km processados no mês | **R$ 10 por km processado** |
| Kit de captura fixo (opcional) | Duas câmeras de ação 5,3K com GPS e suportes de teto, para mais precisão. Revenda ou comodato | ≈ R$ 8.000 por veículo (custo de referência) |
| Integração | Conexão com o sistema de ordens de serviço ou o ERP da Motiva | Projeto sob orçamento |

**Alternativa para a Motiva:** licença anual com serviço gerenciado (nós operamos a nuvem) ou
implantação na nuvem da própria Motiva, com o mesmo valor por km e um desconto pela infraestrutura
que ela assume.

Exemplo, malha completa da Motiva com 2 passagens por mês em cada sentido: 5.044 km × 2 sentidos ×
2 passagens = 20.176 km processados por mês × R$ 10 = **R$ 202 mil por mês (R$ 2,42 milhões por
ano)**, o equivalente a **R$ 480 por km de rodovia por ano**.

## 7. Custos operacionais estimados

### 7.1 Custo de processamento por quilômetro

A maior parte do custo variável é a GPU que executa o modelo de profundidade.

| Item | Valor | Origem |
|---|---|---|
| Máquina de referência | 1 GPU NVIDIA L4, 8 vCPUs e 32 GiB, **US$ 1,42 por hora** | Google Cloud Run, preços de 10/09/2026 |
| Premissas | 2 quadros por metro, lotes de 100 quadros em 60 s, margem de 30% | Slides de apoio do pitch |
| **Custo por km processado** | **US$ 0,62 ≈ R$ 3,18** | US$ 0,6157 × R$ 5,17 (câmbio de 14/09/2026) |
| Rodoanel Oeste, um sentido | ≈ US$ 18 por passagem | 29,3 km × US$ 0,6157 |
| Malha completa, ida e volta | ≈ US$ 6.211 por ciclo (≈ R$ 32 mil) | 10.088 km |

Isso é uma estimativa de planejamento, e não o custo medido do sistema atual, que roda no RunPod.
Se o lote levar 30 s em vez de 60 s, o custo cai pela metade; se levar 120 s, dobra.

### 7.2 Custo mensal por cenário

Premissas: 2 passagens por mês em cada sentido, ou seja, 4 km processados por km de rodovia por
mês. Custo variável ≈ **R$ 3,51 por km processado** (R$ 3,18 de GPU + ≈ R$ 0,33 de
armazenamento, transferência e extração de quadros), contra o preço de R$ 10.

| Item | Piloto (29,3 km) | 1.000 km | Malha Motiva (5.044 km) |
|---|---:|---:|---:|
| GPU (processamento) | R$ 373 | R$ 12.730 | R$ 64.210 |
| Armazenamento, filas e transferência | R$ 300 | R$ 1.300 | R$ 6.560 |
| Infraestrutura fixa (API, banco Neon, painel, monitoramento) | R$ 500 | R$ 3.000 | R$ 5.000 |
| Equipe: 1 eng. de visão computacional, 1 dev full-stack/mobile, 1 analista de campo e suporte, ½ produto/comercial | R$ 70.000 | R$ 70.000 | R$ 70.000 |
| Reserva para licença comercial do modelo de profundidade (ver riscos) | — | R$ 10.000 | R$ 10.000 |
| **Custo total mensal** | **≈ R$ 71 mil** | **≈ R$ 97 mil** | **≈ R$ 156 mil** |
| Km processados por mês | 117 | 4.000 | 20.176 |
| Receita mensal | R$ 15 mil (piloto de R$ 45 mil em 3 meses) | R$ 40 mil | R$ 202 mil |
| **Resultado mensal** | investimento | − R$ 57 mil | **+ R$ 46 mil (23%)** |

Ponto de equilíbrio, com custos fixos de ≈ R$ 85 mil por mês e margem de R$ 6,49 por km
processado: **≈ 13.100 km processados por mês**, o equivalente a ≈ 3.300 km de rodovia com 2
passagens por mês em cada sentido (65% da malha da Motiva). Na estação chuvosa, com mais passagens,
o mesmo contrato passa do ponto de equilíbrio com menos quilômetros. O piloto é investimento de
validação e não precisa se pagar.

Custo de captura em campo: **R$ 0 em câmeras novas** no cenário com celular (só o suporte veicular,
de R$ 20 a R$ 110) e cerca de R$ 8 mil por veículo no cenário com câmeras fixas. O trajeto é o da
viatura que já circula, então não há custo adicional de combustível ou motorista.

## 8. Impacto esperado para a Motiva e para as rodovias

### 8.1 Economia em roçada (premissas a validar no piloto)

Referência pública de custo direto de roçada (composições SICRO, SP, abril de 2026, sem BDI):
**R$ 0,06/m²** (mecanizada de arraste), **R$ 0,10/m²** (mecanizada articulada) e **R$ 0,26/m²**
(manual) ([orçamentor, SICRO](https://orcamentor.com/sicro/composicoes/4915740/)).

| Premissa | Valor usado |
|---|---|
| Faixa roçada por km de rodovia | 2 bordos × 4 m = 8.000 m² |
| Custo médio ponderado | R$ 0,15/m² → **R$ 1.200 por ciclo por km** |
| Ciclos por ano no calendário fixo | 6 |
| Gasto anual estimado | R$ 7.200/km → **≈ R$ 36 milhões por ano** na malha de 5.044 km |

Se o GreenV permitir **pular de 10% a 20% dos ciclos** em trechos que não cresceram (abaixo de
10 cm) e concentrar o esforço onde passou de 30 cm, a economia direta fica entre **R$ 720 e
R$ 1.440 por km por ano**, contra um custo de **R$ 480 por km por ano** com 2 passagens mensais
em cada sentido. É um retorno de **1,5 a 3 vezes** só em roçada, antes dos ganhos abaixo, que
são mais difíceis de colocar em reais.

### 8.2 Impactos não financeiros

- **Segurança viária:** placas, sinalização e acostamentos visíveis, com prioridade objetiva para
  os trechos críticos, e não pela ordem do calendário.
- **Conformidade regulatória:** evidência fotográfica, datada e georreferenciada de cada trecho
  para responder à fiscalização da ANTT ou da ARTESP e contestar autuações.
- **Produtividade da inspeção:** o inspetor deixa de avaliar e anotar a margem; o celular faz a
  leitura enquanto ele cumpre a rota normal.
- **ESG:** menos ciclos desnecessários significam menos combustível, menos emissões e menos
  exposição das equipes ao tráfego.
- **Planejamento:** histórico por trecho para dimensionar contratos de roçada e, com a previsão de
  crescimento, programar a equipe antes de a vegetação passar do limite.

## 9. Principais riscos e mitigação

| Risco | Impacto | Mitigação |
|---|---|---|
| **Licença do modelo de profundidade** (Depth Anything 3, hoje só para uso pessoal e de pesquisa) | Alto: impede o uso comercial na forma atual | Negociar licença comercial (reserva de custo no item 7.2) ou trocar por um modelo de licença permissiva. A arquitetura modular isola o modelo em um único serviço |
| **Acurácia automática não validada com trena** | Alto: sem ela, a leitura não deve gerar ordem sozinha | Primeiro objetivo do piloto: campanha com trena em trechos do Rodoanel Oeste. Até lá o sistema marca as medições como `not-ready` e o supervisor confirma pela foto |
| **Sem referência quilométrica (km da rodovia)** | Médio: o trecho fica no mapa, mas não no km do contrato | Integrar a referência linear da Motiva (marcos quilométricos e os 642 polígonos KMZ) |
| **Custo de GPU acima do estimado** | Médio | Margem de quase 3 vezes entre o preço (R$ 10) e o custo estimado (R$ 3,51) por km processado, reajuste contratual pelo custo de GPU e possibilidade de trocar de fornecedor de GPU sem mudar o código |
| **LGPD**: vídeos captam placas e pessoas | Médio | Retenção limitada, acesso por perfil, processamento apenas dos quadros da margem e anonimização de placas e rostos antes de arquivar |
| **Adoção pela operação** | Médio | Captura sem digitação, aproveitando a rota existente; treinamento no piloto; ordens de serviço no mesmo fluxo |
| **Condições de captura** (chuva, noite, baixa velocidade de quadros em celulares simples) | Médio | Indicação de qualidade do GPS na tela, escada de formatos de câmera, opção de câmeras fixas e recusa de medição quando a evidência é insuficiente |
| **Segurança do operador** | Alto se mal usado | O app só funciona com o celular fixo no suporte e não exige interação durante a direção |

## 10. Próximos passos

1. **Piloto de 3 meses no Rodoanel Oeste (SP-021):** coleta pela viatura de inspeção, campanha
   com trena e meta de erro abaixo de 5 cm na medição automática.
2. **Resolver a licença do modelo** antes de qualquer contrato comercial.
3. **Integrar o km da rodovia e o sistema de ordens da Motiva.**
4. **Expandir** para as demais concessões rodoviárias da Motiva, com o mesmo preço por km processado.
5. **Ativar a previsão de crescimento** com o histórico real das passagens.

---

*Fontes:* Motiva, malha de 5.044 km (resultado do 2T2026, consultado em 10/09/2026); Google Cloud
Run, preços da GPU L4 (10/09/2026); câmbio de R$ 5,17 (14/09/2026); preços da loja GoPro Brasil e
de suportes (14/09/2026); composições SICRO no
[orçamentor](https://orcamentor.com/sicro/composicoes/4915740/); [MPF sobre a roçada da
BR-050](https://www.mpf.mp.br/o-mpf/unidades/pr-mg/noticias/mpf-pede-rocada-total-na-br-050-contra-fumaca-de-queimadas-e-risco-de-acidentes);
documentação do repositório [GreenV](https://github.com/henrikmm/GreenV) (`docs/STATE-OF-THE-SYSTEM.md`,
`docs/INFRASTRUCTURE_MVP.md`, `measurement/MEASUREMENTS.md`).
