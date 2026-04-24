# PRD — Caso Simulado "ForgePag"

- **Propósito:** Contexto simulado para avaliação do método DPE na Seção 4 do TCC.
- **TCC:** Método para geração de valor mensurável em decisões automatizadas por inteligência artificial: confiabilidade, tempestividade e conexão com métricas de negócio.
- **Autores:** Jorge Kennedy S. Oliveira, Joao Paulo L. S. Polotto, Johnny Cardoso Marques.
- **Status:** Documento consolidado, alinhado ao artigo final, publicado em repositório aberto como material complementar.
- **Repositório público:** https://github.com/jorgeac12/tcc-ita-dpe-forgepag-case
---

## 1. Visão geral do contexto simulado

### 1.1 Identidade

A ForgePag é uma fintech brasileira fictícia, construída para fins de avaliação qualitativa do método DPE. O contexto simulado não substitui validação empírica com dados reais. Os volumes, proporções e padrões de comportamento foram calibrados para refletir ordens de magnitude realistas de bancos digitais brasileiros de médio-grande porte no horizonte 2025-2026.

### 1.2 Portfólio de produtos

A ForgePag opera cinco produtos principais:

1. **Conta de Pagamento (CP)**: produto-âncora, serve de hub transacional
2. **PIX**: transferências instantâneas, modalidades pagamento e recebimento
3. **Cartão**: débito e crédito, modalidades físico e virtual
4. **Onboarding & KYC**: jornada de cadastro e validação de identidade
5. **Crédito**: empréstimo pessoal e limite rotativo

### 1.3 Porte e volumes

| Métrica | Valor | Fonte/racional |
|---|---|---|
| Base de clientes ativos | 10 milhões | Compatível com fintechs brasileiras de porte médio-grande |
| Novos signups por dia | 20 mil | Taxa anualizada de ~7M, realista em mercado maduro |
| Transações PIX por dia | 50 milhões | ~5 transações/cliente/dia, consistente com BACEN 2025 |
| Valor transacionado PIX por dia | R$ 3 bilhões | Ticket médio ~R$ 60, típico para banco de varejo |
| Tentativas de fraude PIX por dia (estimada) | 5-15 mil | 0,01% a 0,03% das transações, faixa reportada pela indústria |
| Perda por fraude PIX sem método (estimada) | R$ 800 mil a R$ 2 milhões/dia | Cenário sem detecção robusta |
| Taxa de churn mensal (clientes que encerram conta ou tornam-se inativos) | 1,5% a 2,5% | Típico em fintechs brasileiras maduras |
| Clientes em risco de churn no próximo trimestre (estimado) | 600 mil a 1 milhão | 6% a 10% da base ativa |
| Custo de aquisição de cliente (CAC) | R$ 80 a R$ 150 | Mercado fintech de varejo brasileiro |
| LTV médio dos clientes ativos | R$ 600 a R$ 1.500 em 24 meses | Derivado de receita por cliente |
| Tickets de suporte abertos por dia | 50 mil a 80 mil | 0,5% a 0,8% da base ativa, típico |

### 1.4 Estrutura organizacional por domínios

A ForgePag organiza seus dados analíticos em domínios de negócio segundo o paradigma *data mesh* (Dehghani, 2022). Cada domínio tem *owner* explícito responsável pelos seus produtos de dados:

| Domínio | Owner | Produtos de dados principais |
|---|---|---|
| Onboarding | Head de Produto Onboarding | `clientes_cadastro`, `kyc_eventos`, `device_fingerprint` |
| Conta de Pagamento | Head de Produto CP | `saldo_diario`, `limite_disponivel`, `movimentacao` |
| PIX | Head de Produto PIX | `transacao_pix`, `chave_pix`, `meds_historico` |
| Cartão | Head de Produto Cartão | `transacao_cartao`, `autorizacao`, `chargebacks` |
| Crédito | Head de Crédito | `score_bureau`, `emprestimos`, `inadimplencia` |
| Risco & Fraude | Head de Risco | `score_fraude`, `blocklist_destinatarios`, `alertas` |
| Engajamento & Retenção | Head de Growth | `healthy_score`, `churn_probability`, `ltv_cliente`, `campanhas_ativas` |
| Atendimento | Head de Customer Experience | `tickets`, `nps_scores`, `tempo_resolucao`, `canais_atendimento` |

---

## 2. Decisões automatizadas sob análise

O método DPE é aplicado a duas decisões automatizadas analisadas em profundidade, escolhidas para demonstrar a generalidade do método em contextos contrastantes: uma decisão de custo evitado com tempestividade extrema (fraude PIX) e uma decisão de receita preservada com tempestividade longa (retenção proativa).

### 2.1 Decisão profunda 1: Detecção de fraude em transação PIX

**Descrição:** Para cada tentativa de transação PIX iniciada por um cliente ForgePag, decidir em tempo real, no intervalo da autorização, se a transação deve ser aprovada, desafiada com autenticação adicional (*step-up*) ou bloqueada por suspeita de fraude.

**Justificativas da escolha como decisão profunda:**

- **Tempestividade extrema:** decisão precisa ser tomada em milissegundos
- **Confiabilidade crítica:** regulação BACEN e LGPD tornam falha cara em reputação e multa
- **Valor de negócio direto:** perda por fraude é métrica observável em R$
- **Expressividade didática:** múltiplos *data points* heterogêneos convergem para a mesma decisão
- **Familiaridade dos autores:** domínio fintech brasileira é área de especialização

### 2.2 Decisão profunda 2: Acionamento proativo de retenção para clientes com sinal de churn

**Descrição:** Para cada cliente ativo da ForgePag, decidir periodicamente se ele deve ser incluído em campanha proativa de retenção, com intervenção direcionada (contato humano, oferta de produto premium, antecipação de limite, benefício personalizado), com base em healthy score multi-fonte que indica risco de descontinuidade de uso ou cancelamento.

**Justificativas da escolha como decisão profunda:**

- **Contraste de tempestividade:** decisão opera em janela de dias a semanas, contrastando com a tempestividade de milissegundos da fraude PIX
- **Natureza econômica complementar:** valor de negócio predominante é receita preservada (LTV dos clientes retidos), complementando o custo evitado da fraude
- **Evidência multi-fonte:** o healthy score consolida sinais de sete famílias de *data points* distintas, demonstrando a aplicação do método DPE a evidência composta
- **Sinal de resultado tardio:** a confirmação do churn só acontece quando o cliente efetivamente encerra a conta ou torna-se inativo, condição que torna a calibração de tempestividade mais sutil que em fraude PIX
- **Relevância estratégica:** retenção de clientes é alavanca de valor reconhecidamente crítica em fintechs brasileiras, onde a curva de LTV é sensível ao engajamento contínuo

---

## 3. Decisão profunda 1: anatomia da detecção de fraude PIX

### 3.1 Camada D (Decisão) — formulação

A decisão automatizada tem três resultados possíveis:

1. **Aprovar:** transação prossegue sem intervenção
2. **Desafiar com *step-up*:** cliente recebe solicitação de autenticação adicional (biometria, token, pergunta de segurança)
3. **Bloquear:** transação é recusada e cliente é informado

A decisão é tomada pelo serviço de autorização PIX em sequência síncrona no *path* da transação. A decisão é binária (aprovar/não aprovar) com caminho intermediário de *step-up* quando a incerteza está em faixa específica do score de risco.

### 3.2 Camada P (Pergunta) — perguntas que fundamentam a decisão

Seis perguntas formais são respondidas simultaneamente durante a autorização:

| ID | Pergunta | Família de *data point* |
|---|---|---|
| P1 | A geolocalização da transação é consistente com o padrão histórico do cliente? | Geolocalização |
| P2 | O valor é consistente com o comportamento do cliente para este dia do mês e para este destinatário? | Comportamento transacional |
| P3 | O destinatário tem histórico de envolvimento em MEDs ou alertas de fraude? | Rede/grafo |
| P4 | O valor é compatível com o poder aquisitivo observável do cliente? | Poder financeiro |
| P5 | O dispositivo utilizado tem fingerprint reconhecido e histórico saudável? | Dispositivo |
| P6 | Há sinais comportamentais de sessão que indicam engenharia social ou coação? | Comportamento de sessão |

### 3.3 Camada E (Evidência) — *data points* por família

#### Família 1 — Geolocalização (responde P1)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Geolocalização do signup (IP, lat/long aproximada) | Evento de cadastro | Geração → persistência | Histórico |
| Geolocalização do último login | Evento de login | Streaming | Minutos |
| Geolocalização da transação atual | Evento transacional | Streaming | Milissegundos |
| Distância entre signup e transação | *Feature* derivada | Transformação | Tempo real |
| Padrão histórico de geolocalização (N últimas transações) | *Feature* agregada | Transformação batch | Diário |

**Desafios de confiabilidade:** VPN, *mobile* vs desktop, imprecisão de geolocalização por IP (pode errar em dezenas de km).

#### Família 2 — Comportamento transacional (responde P2)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Valor médio de transação PIX por dia do mês | Agregação 12 meses | Transformação batch | Diário |
| Valor médio de transação para este destinatário | Agregação histórica | Transformação batch | Diário |
| Desvio-padrão do valor por cliente | Agregação histórica | Transformação batch | Diário |
| Número de transações PIX nas últimas 24h | Agregação janela móvel | Streaming | Minutos |
| Flag de primeira transação para este destinatário | Derivação lógica | Transformação | Tempo real |

**Desafios de confiabilidade:** clientes novos (<30 dias) têm histórico incompleto; agregações batch podem atrasar se *pipeline* falhar.

#### Família 3 — Rede/grafo (responde P3)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Score de risco do destinatário | Modelo batch | Transformação batch | Horas a diário |
| Quantidade de MEDs envolvendo o destinatário nos últimos 90 dias | Agregação histórica | Transformação batch | Diário |
| Cluster de fraude ao qual o destinatário pertence (se algum) | Análise de grafo | Transformação batch | Semanal |
| Chave PIX do destinatário (tipo: CPF, telefone, chave aleatória) | Metadados da chave | Ingestão | Tempo real |
| Banco do destinatário | Metadados da chave | Ingestão | Tempo real |

**Desafios de confiabilidade:** *lineage* do score MED (de onde vem o sinal? DICT? sistema interno? reportado por outros bancos?); atualização do score depende de eventos reportados por terceiros.

#### Família 4 — Poder financeiro (responde P4)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Saldo disponível na Conta de Pagamento | Sistema transacional | Streaming | Tempo real |
| Limite rotativo disponível | Sistema de crédito | API síncrona | Tempo real |
| Salário autodeclarado no onboarding | Dados do cadastro | Histórico | Estático |
| Score de bureau (Serasa, Boa Vista) | Consulta bureau | Transformação batch | Mensal |
| Histórico de movimentação mensal (últimos 6 meses) | Agregação histórica | Transformação batch | Diário |

**Desafios de confiabilidade:** salário autodeclarado sem validação cruzada; *score* de bureau pode estar desatualizado; múltiplas fontes com *lineage* distinto.

#### Família 5 — Dispositivo (responde P5)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| *Device fingerprint* (ID agregado de parâmetros do dispositivo) | Evento de sessão | Streaming | Tempo real |
| Versão do sistema operacional | Metadados da sessão | Streaming | Tempo real |
| Histórico deste *fingerprint* (primeira vista, transações anteriores) | Agregação histórica | Transformação | Diário |
| Flag de *rooted*/*jailbroken* | Análise do SO | Streaming | Tempo real |
| Número de contas ForgePag já associadas a este *fingerprint* | Agregação | Transformação batch | Diário |

**Desafios de confiabilidade:** *fingerprint* pode ser falsificado por atacantes sofisticados; identificador pode mudar após atualização do SO.

#### Família 6 — Comportamento de sessão (responde P6)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Tempo desde o login até o PIX | Evento de sessão | Streaming | Tempo real |
| Número de tentativas de PIX na sessão | Contador de sessão | Streaming | Tempo real |
| Padrão de digitação (velocidade, erros, pausas) | Telemetria comportamental | Streaming | Tempo real |
| Caminho de navegação até a tela do PIX | Eventos de UI | Streaming | Tempo real |
| Horário da transação (madrugada, fim de semana, horário comercial) | Metadado da transação | Tempo real | Tempo real |
| Valor em formato "redondo" (R$ 500, R$ 1.000, R$ 5.000) | Derivação lógica | Transformação | Tempo real |

**Desafios de confiabilidade:** telemetria comportamental pode ter qualidade variável por dispositivo; falta de histórico para clientes novos.

### 3.4 Cenário contrafactual para atribuição de valor

A atribuição rigorosa do valor econômico gerado pela aplicação do método DPE à decisão de fraude PIX exige comparação com cenário contrafactual, isto é, estimativa do que teria ocorrido na ausência do método. O contrafactual adotado neste caso simulado é a **operação por regras estáticas (baseline)**, correspondente ao estado anterior ao método DPE.

#### 3.4.1 Definição do baseline estático

O baseline é composto por regras determinísticas sem aprendizagem, tipicamente utilizadas em operações maduras antes da introdução de modelos de aprendizado de máquina. Exemplos ilustrativos de regras que compõem o baseline:

- Bloqueio quando valor da transação excede R$ 5.000 fora do horário comercial
- Desafio por step-up quando dispositivo é desconhecido e transação ocorre em rota geográfica não usual
- Bloqueio quando destinatário aparece em lista negra construída a partir de ocorrências anteriores no MED
- Limite diário de transações PIX por cliente para novos cadastros (< 30 dias)

O baseline é escolhido por três razões: reflete o estado operacional de muitas instituições antes da adoção de métodos DPE maduros; é reprodutível em ambiente simulado porque suas regras são transparentes; e serve como piso de referência realista, não como adversário artificialmente fraco.

#### 3.4.2 Métricas de comparação

A comparação entre método DPE aplicado e baseline estático mobiliza as seguintes métricas:

| Métrica | O que mede | Direção esperada |
|---|---|---|
| Taxa de detecção verdadeira (recall) | Fraudes corretamente bloqueadas / total de fraudes | Maior no método DPE |
| Taxa de falsos positivos | Transações legítimas bloqueadas / total de transações legítimas | Menor no método DPE |
| Perda financeira por fraude | R$ perdidos por fraudes não bloqueadas | Menor no método DPE |
| Custo de atrito por step-up excessivo | R$ estimados por clientes que abandonaram transação após step-up | Menor no método DPE |

#### 3.4.3 Limitações da comparação em ambiente simulado

A comparação em ambiente simulado não substitui experimentação A/B em produção. O baseline estático aqui adotado é aproximação; o baseline real de cada instituição depende de seu histórico específico de regras e exceções. A avaliação qualitativa deste PRD limita-se a demonstrar **que** o método DPE oferece resposta estruturada em todas as seis células da matriz DPE×CT, não **quanto** de ganho adicional ele entrega em instância específica. A quantificação rigorosa é proposta como trabalho futuro na Seção 5.1 do artigo.

---

## 4. Decisão profunda 2: anatomia do acionamento proativo de retenção

### 4.1 Camada D (Decisão) — formulação

A decisão automatizada responde à pergunta: este cliente deve ser incluído em campanha proativa de retenção no próximo ciclo operacional? Três resultados são possíveis:

1. **Não acionar:** cliente está saudável, permanece fora da campanha
2. **Acionar com intervenção leve:** cliente recebe comunicação digital personalizada (*push*, e-mail, oferta em app) sem custo humano
3. **Acionar com intervenção alta:** cliente recebe contato humano direto (ligação, *chat* prioritário, gerente dedicado) além da intervenção digital

A decisão é tomada em ciclo batch diário pelo domínio Engajamento & Retenção. A cada madrugada, o serviço de scoring computa o healthy score de todos os clientes ativos e materializa a lista de clientes candidatos a intervenção para o próximo ciclo. Ao contrário da fraude PIX, a decisão não é síncrona no caminho de uma transação: ela é proativa e assíncrona, operando por antecipação.

### 4.2 Camada P (Pergunta) — perguntas que fundamentam a decisão

Sete perguntas formais são respondidas simultaneamente durante o ciclo de scoring:

| ID | Pergunta | Família de *data point* |
|---|---|---|
| P1 | O cliente está engajado transacionalmente em intensidade consistente com seu histórico? | Engajamento transacional |
| P2 | O cliente está utilizando os produtos contratados (cartão, PIX, conta, crédito) em padrão saudável? | Engajamento de produto |
| P3 | A tendência de uso do cliente nos últimos 90 dias é crescente, estável ou decrescente? | Tendência temporal |
| P4 | A qualidade da experiência do cliente é satisfatória, sem fricção elevada? | Qualidade de experiência |
| P5 | A percepção externa do cliente (NPS, indicações) é positiva? | Indicadores externos |
| P6 | O cliente apresenta sinais financeiros saudáveis (movimentação, saldo, uso de crédito adimplente)? | Sinais financeiros |
| P7 | Os sinais de vida do cliente (cadastro, chaves, cartão) indicam conta ativa e pronta para uso? | Sinais de vida |

### 4.3 Camada E (Evidência) — o healthy score como evidência multi-fonte

Diferentemente da decisão de fraude PIX, em que a evidência é composta por múltiplas *features* consumidas diretamente pelo modelo, a decisão de retenção consome uma **evidência derivada**: o **healthy score**, um escore consolidado no intervalo [0, 100] que agrega os sinais das sete famílias em uma única métrica interpretável pelo domínio de Engajamento & Retenção.

O healthy score é produto de dado *per se* (`healthy_score` do domínio Engajamento & Retenção), com *owner* explícito, frescor contratado diário e SLA de qualidade. A evidência que alimenta a decisão de retenção é, portanto, **o healthy score** e não os *data points* brutos. Mas a confiabilidade e a tempestividade do healthy score dependem, por transitividade, da confiabilidade e tempestividade dos *data points* que o compõem. Esta é uma aplicação natural do pilar de *lineage* (Seção 3.4 do TCC), em que a evidência final herda a qualidade dos insumos ao longo da cadeia.

As sete famílias que alimentam o healthy score são detalhadas a seguir.

#### Família 1 — Engajamento transacional (responde P1)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Logins no app nos últimos 7, 30 e 90 dias | Evento de sessão | Transformação batch | Diário |
| Transferências PIX enviadas nos últimos 30 dias | Agregação histórica | Transformação batch | Diário |
| Pagamentos de boletos nos últimos 30 dias | Agregação histórica | Transformação batch | Diário |
| Compras no cartão nos últimos 30 dias | Agregação histórica | Transformação batch | Diário |
| Frequência semanal de uso (dias da semana com ao menos uma transação) | *Feature* derivada | Transformação batch | Diário |

**Desafios de confiabilidade:** dependência do *pipeline* de eventos de sessão; clientes que usam canais alternativos (Internet Banking, caixas 24h) podem aparecer como inativos mesmo sendo ativos.

#### Família 2 — Engajamento de produto (responde P2)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Produtos contratados pelo cliente (Conta, PIX, Cartão, Crédito, Investimentos) | Catálogo de produtos | Tabela batch | Diário |
| Módulos do app abertos nos últimos 30 dias | Eventos de navegação | Transformação batch | Diário |
| Novos produtos ativados nos últimos 90 dias | Evento de ativação | Transformação batch | Diário |
| Tempo médio de sessão no app nas últimas 4 semanas | Agregação de sessão | Transformação batch | Diário |
| Flag de uso de funcionalidades premium (investimentos, limite adicional, cartão múltiplo) | Derivação lógica | Transformação batch | Diário |

**Desafios de confiabilidade:** telemetria de navegação pode ter perda em versões antigas do app; mudanças de *layout* do app alteram rotas de eventos.

#### Família 3 — Tendência temporal (responde P3)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Número de transações nos últimos 30 dias vs 30 dias anteriores | *Feature* derivada | Transformação batch | Diário |
| Valor movimentado nos últimos 30 dias vs 30 dias anteriores | *Feature* derivada | Transformação batch | Diário |
| Frequência de login nos últimos 30 dias vs 30 dias anteriores | *Feature* derivada | Transformação batch | Diário |
| Sequência de dias consecutivos sem transação | Contador temporal | Transformação batch | Diário |
| Inclinação da curva de uso ajustada por janela móvel (30/60/90 dias) | *Feature* derivada por regressão local | Transformação batch | Semanal |

**Desafios de confiabilidade:** clientes com padrão sazonal podem ser classificados como em declínio quando estão apenas em ciclo natural; calibração exige histórico suficiente (>180 dias) para ser robusta.

#### Família 4 — Qualidade de experiência (responde P4)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Número de tickets de suporte nos últimos 90 dias | Domínio Atendimento | Transformação batch | Diário |
| Tempo médio de resolução dos tickets do cliente | Domínio Atendimento | Transformação batch | Diário |
| Taxa de resolução no primeiro contato (FCR) para este cliente | Domínio Atendimento | Transformação batch | Diário |
| Número de erros técnicos enfrentados pelo cliente (transações recusadas, timeout, falhas de login) | Telemetria de erro | Transformação batch | Diário |
| Latência média experimentada pelo cliente no app (p90 de tempo de resposta) | Telemetria de performance | Transformação batch | Diário |

**Desafios de confiabilidade:** telemetria de erro pode ter lacunas se o app perder conexão; atribuir qualidade a sessões específicas exige *lineage* robusto.

#### Família 5 — Indicadores externos (responde P5)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Score NPS mais recente do cliente | Pesquisa NPS | Transformação batch | Mensal |
| Score CSAT dos últimos tickets resolvidos | Pesquisa CSAT pós-atendimento | Transformação batch | Diário |
| Indicações de novos clientes (referral) feitas pelo cliente | Evento de referral | Transformação batch | Diário |
| Avaliações em lojas de aplicativos associadas ao cliente (quando rastreáveis) | Integração externa | Transformação batch | Semanal |
| Reclamações públicas em canais externos (Reclame Aqui, redes sociais) vinculadas ao CPF | Integração externa | Transformação batch | Semanal |

**Desafios de confiabilidade:** pesquisas têm baixa taxa de resposta (10-20%); vinculação a dados externos é probabilística e pode gerar falsos positivos.

#### Família 6 — Sinais financeiros (responde P6)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Saldo médio da Conta de Pagamento nos últimos 90 dias | Agregação histórica | Transformação batch | Diário |
| Movimentação média mensal (entradas + saídas) | Agregação histórica | Transformação batch | Diário |
| Utilização percentual do limite de crédito nos últimos 90 dias | Agregação histórica | Transformação batch | Diário |
| Flag de adimplência (em dia, atrasado <30 dias, atrasado >30 dias) | Domínio Crédito | Transformação batch | Diário |
| Presença de recebimentos recorrentes (salário, benefícios, faturamento) | Análise de padrão | Transformação batch | Mensal |

**Desafios de confiabilidade:** classificação de entrada como salário vs outros depende de algoritmo de categorização; clientes com múltiplas fontes bancárias podem parecer menos engajados.

#### Família 7 — Sinais de vida (responde P7)

| *Data point* | Fonte | Estágio na cadeia | Frescor típico |
|---|---|---|---|
| Flag de cadastro atualizado nos últimos 12 meses | Domínio Onboarding | Transformação batch | Diário |
| Chaves PIX ativas e utilizáveis | Domínio PIX | Transformação batch | Diário |
| Cartão físico desbloqueado e ativo | Domínio Cartão | Transformação batch | Diário |
| Último acesso ao Internet Banking ou app | Telemetria de sessão | Transformação batch | Diário |
| Consentimentos LGPD vigentes e termos aceitos | Domínio Onboarding | Transformação batch | Diário |

**Desafios de confiabilidade:** sinais de vida são estáveis mas podem ser desatualizados se integração com outros domínios tiver latência.

### 4.4 Composição do healthy score

O healthy score é computado como função ponderada dos sete sinais de família, cada um normalizado no intervalo [0, 100]. A composição é formalizada como:

```
healthy_score = Σ (wi × scorei),  para i = 1..7
```

Onde:
- `scorei` é o score normalizado [0, 100] da família i
- `wi` é o peso da família i
- `Σ wi = 1` (soma dos pesos é unitária)
- `healthy_score ∈ [0, 100]`

#### 4.4.1 Pesos de referência

Os pesos de referência para o segmento Pessoa Física Geral (*value tier* médio, sem especialização por sub-segmento) são:

| Família | Peso (wi) | Justificativa |
|---|---|---|
| Engajamento transacional | 0,25 | Sinal mais direto de relacionamento ativo |
| Engajamento de produto | 0,15 | Sinaliza profundidade de adoção |
| Tendência temporal | 0,20 | Sinal antecedente mais relevante para churn |
| Qualidade de experiência | 0,10 | Fricção operacional é preditora de abandono |
| Indicadores externos | 0,05 | Taxa de resposta baixa limita peso |
| Sinais financeiros | 0,15 | Saúde financeira condiciona permanência |
| Sinais de vida | 0,10 | Piso de atividade, alto sinal quando ausente |
| **Total** | **1,00** | |

A concentração em Engajamento transacional e Tendência temporal (soma de 0,45) reflete a observação empírica de que os sinais mais preditores de churn em fintechs brasileiras são comportamentais e antecedentes.

#### 4.4.2 Normalização de cada família

Cada score de família é obtido por agregação ponderada dos *data points* da família, com cada *data point* previamente normalizado para [0, 100] segundo seu tipo:

- **Métricas de volume (transações, logins, valores movimentados):** normalização por percentil na distribuição da base ativa. O cliente no percentil 50 recebe score 50; no percentil 90, score 90
- **Métricas binárias ou flags (adimplência, cadastro atualizado, chave PIX ativa):** normalização direta como 0 (ausente) ou 100 (presente)
- **Métricas de tendência (crescimento vs declínio):** normalização em torno do ponto zero, com tendência positiva mapeando para faixa [50, 100] e tendência negativa para [0, 50]
- **Métricas de tempo (dias desde última transação, idade da sessão):** normalização inversa, com tempos curtos próximos de 100 e longos próximos de 0

Detalhes operacionais completos da normalização de cada *data point* são mantidos em catálogo separado no repositório, uma vez que extrapolam o escopo desta avaliação qualitativa.

#### 4.4.3 Exemplo numérico ilustrativo

Para ilustrar a composição, considera-se um cliente fictício com os seguintes scores de família em ciclo batch diário:

| Família | Score normalizado | Peso | Contribuição |
|---|---|---|---|
| Engajamento transacional | 85 | 0,25 | 21,25 |
| Engajamento de produto | 72 | 0,15 | 10,80 |
| Tendência temporal | 60 | 0,20 | 12,00 |
| Qualidade de experiência | 90 | 0,10 | 9,00 |
| Indicadores externos | 78 | 0,05 | 3,90 |
| Sinais financeiros | 88 | 0,15 | 13,20 |
| Sinais de vida | 95 | 0,10 | 9,50 |
| **Healthy score** | | | **79,65** |

Com healthy score 79,65, o cliente seria classificado em faixa de baixa probabilidade de churn no próximo trimestre. A Família 3 (Tendência temporal, score 60) opera como sinal amarelo que merece acompanhamento, mesmo com o score total alto.

#### 4.4.4 Tradução em probabilidade de churn e segmentação

O healthy score final é traduzido em probabilidade de churn no próximo trimestre por modelo calibrado pelo domínio Engajamento & Retenção sobre histórico de rotulagem de churn dos últimos 12 meses. A faixa de valores tipicamente observada:

| Faixa de healthy score | Probabilidade de churn no trimestre | Ação de retenção |
|---|---|---|
| [0, 30] | > 40% | Intervenção alta (contato humano direto) |
| [30, 50] | 20% a 40% | Intervenção alta se *value tier* alto, leve caso contrário |
| [50, 70] | 8% a 20% | Intervenção leve (comunicação digital) |
| [70, 85] | 3% a 8% | Não acionar (monitoramento contínuo) |
| [85, 100] | < 3% | Não acionar (cliente saudável) |

A decisão final de acionamento combina a probabilidade de churn com o *value tier* do cliente (LTV estimado) para priorização de orçamento de campanha, conforme Seção 7.

#### 4.4.5 Ciclo de recalibração

Os pesos `wi` e os *thresholds* de tradução em probabilidade de churn são recalibrados **trimestralmente** pelo domínio Engajamento & Retenção, com as seguintes etapas:

1. Avaliação retrospectiva dos últimos 90 dias: quais clientes com score acima de 70 efetivamente saíram, e quais com score abaixo de 30 permaneceram. Este passo fecha o *loop* de aprendizagem do modelo
2. Análise por sub-segmento: Pessoa Física Geral, Pessoa Física Alto Valor, Pessoa Jurídica Pequena, Pessoa Jurídica Média. Cada sub-segmento pode receber pesos especializados se a análise retrospectiva evidenciar padrões distintivos
3. Calibração de novos pesos por otimização supervisionada sobre a base rotulada
4. Aprovação dos novos pesos pelo comitê de domínio Engajamento & Retenção, com registro de decisão para fins de auditoria e *lineage* (ver Seção 7)
5. Publicação dos novos pesos com versionamento explícito do produto de dado `healthy_score`, permitindo comparabilidade histórica

O ciclo trimestral é justificado pela estabilidade relativa dos padrões comportamentais em horizonte curto, pela disponibilidade de dados rotulados suficientes em 90 dias e pelo custo operacional de recalibração mais frequente. Em situações de mudança abrupta de contexto (lançamento de produto, campanha de aquisição em massa, choque externo regulatório ou macroeconômico), a recalibração pode ser antecipada com aprovação extraordinária do comitê de domínio.

O healthy score alimenta, além da decisão de acionamento de retenção, outras decisões da organização que não são analisadas em profundidade neste PRD: oferta de produtos premium e antecipação de limite de crédito. Esta reutilização reforça o status de `healthy_score` como produto de dado com ciclo de vida próprio, sob responsabilidade do domínio Engajamento & Retenção.

### 4.5 Cenário contrafactual para atribuição de valor

A atribuição rigorosa do valor econômico gerado pela decisão de retenção proativa apresenta desafio estruturalmente mais complexo do que a atribuição na decisão de fraude PIX. Na fraude, o valor evitado é observável aproximadamente: fraudes bloqueadas deixam registro direto. Na retenção, o cliente que **não** saiu após ser acionado por campanha pode ter permanecido independentemente da intervenção, configurando problema clássico de inferência causal.

#### 4.5.1 Abordagem quase-experimental com grupo de controle

O contrafactual adotado para a retenção é a construção de **grupo de controle quase-experimental** com as seguintes características:

- **Critério de elegibilidade idêntico:** clientes com healthy score abaixo de 50 e no *value tier* elegível para campanha no trimestre
- **Atribuição por amostragem aleatória:** dentre os elegíveis, aproximadamente 10% são alocados ao grupo de controle (sem intervenção) e 90% ao grupo de tratamento (com intervenção apropriada ao *value tier*)
- **Janela de observação uniforme:** 90 dias após a data de potencial acionamento
- **Desfecho observado:** indicador binário de churn confirmado ao final da janela, complementado por valor presente líquido da receita preservada (LTV atribuído)

A proporção de 10% para o grupo de controle resulta de equilíbrio entre rigor estatístico (amostra suficiente para detecção de efeitos) e responsabilidade operacional (não privar a maior parte dos clientes em risco de intervenção potencialmente benéfica).

#### 4.5.2 Métricas de comparação

A comparação entre grupos de tratamento e controle mobiliza as seguintes métricas:

| Métrica | O que mede | Direção esperada |
|---|---|---|
| Taxa de churn no grupo tratamento | % de clientes acionados que saíram em 90 dias | Menor que controle |
| Taxa de churn no grupo controle | % de clientes não acionados que saíram em 90 dias | Baseline |
| Diferença percentual (uplift) | Diferença entre as duas taxas | Positiva |
| Receita preservada líquida | (LTV preservado em tratamento − LTV preservado em controle) − custo de campanha | Positiva |
| Custo por cliente retido | Custo total da campanha / (clientes retidos a mais no tratamento) | Inferior a LTV médio |

#### 4.5.3 Considerações éticas e regulatórias

A utilização de grupo de controle em ambiente operacional real envolve considerações éticas e regulatórias não-triviais:

- Clientes no grupo de controle podem ser interpretados como privados de intervenção potencialmente benéfica, o que exige transparência nos termos de uso e consentimento LGPD
- A alocação aleatória deve respeitar segmentos sensíveis (pessoas em vulnerabilidade financeira confirmada) que podem justificar exclusão do controle por política interna
- O desenho quase-experimental deve ser aprovado por comitê interno de ética de dados, com documentação para auditoria e conformidade regulatória

#### 4.5.4 Limitações da comparação em ambiente simulado

Para os fins deste PRD, o desenho contrafactual é declarado como condição teórica de atribuição rigorosa, não como experimento efetivamente executado. A avaliação qualitativa da Seção 4 do artigo limita-se a demonstrar que o método DPE, aplicado à retenção, **comporta** a definição formal de contrafactual; a execução do experimento e a mensuração precisa do uplift ficam como trabalho futuro na Seção 5.1 do artigo. Esta limitação é compartilhada por grande parte da literatura de avaliação de campanhas de retenção em contexto não-experimental, e seu reconhecimento explícito é, em si, contribuição metodológica do método DPE em comparação com abordagens que reportam ganhos sem contrafactual declarado.

---

## 5. Produtos de dados envolvidos

Os *data points* apresentados nas Seções 3 e 4 são servidos por produtos de dados identificáveis e de *ownership* explícito. As tabelas abaixo consolidam os principais produtos que alimentam cada decisão profunda.

### 5.1 Produtos de dados da decisão de fraude PIX

| Produto de dado | Domínio owner | Tipo | Frescor contratado |
|---|---|---|---|
| `clientes_cadastro` | Onboarding | Tabela batch | Diário |
| `kyc_eventos` | Onboarding | Stream | Tempo real |
| `device_fingerprint` | Onboarding | Stream + tabela histórica | Tempo real / Diário |
| `saldo_diario` | Conta de Pagamento | Tabela batch + cache tempo real | Diário / Tempo real |
| `limite_disponivel` | Conta de Pagamento | API síncrona | Tempo real |
| `movimentacao` | Conta de Pagamento | Tabela batch | Diário |
| `transacao_pix_stream` | PIX | Stream | Tempo real |
| `chave_pix` | PIX | Tabela + API | Tempo real |
| `meds_historico` | PIX | Tabela batch | Diário |
| `score_bureau` | Crédito | API externa + cache | Mensal |
| `score_fraude_destinatario` | Risco & Fraude | Modelo batch + cache | Horas |
| `blocklist_destinatarios` | Risco & Fraude | Tabela + cache | Minutos |
| `features_comportamentais_cliente` | Risco & Fraude | *Feature store* | Diário |
| `features_sessao_corrente` | Risco & Fraude | *Feature store* tempo real | Tempo real |

### 5.2 Produtos de dados da decisão de retenção proativa

| Produto de dado | Domínio owner | Tipo | Frescor contratado |
|---|---|---|---|
| `sessao_login_historico` | Onboarding | Tabela batch | Diário |
| `engajamento_transacional_agregado` | Engajamento & Retenção | Tabela batch | Diário |
| `engajamento_produto_agregado` | Engajamento & Retenção | Tabela batch | Diário |
| `tendencia_uso_cliente` | Engajamento & Retenção | *Feature store* | Diário |
| `healthy_score` | Engajamento & Retenção | Tabela batch | Diário |
| `churn_probability` | Engajamento & Retenção | Modelo batch | Diário |
| `ltv_cliente` | Engajamento & Retenção | Modelo batch | Semanal |
| `value_tier_cliente` | Engajamento & Retenção | Tabela batch | Diário |
| `campanhas_ativas` | Engajamento & Retenção | Tabela batch | Diário |
| `tickets` | Atendimento | Stream + tabela batch | Tempo real / Diário |
| `nps_scores` | Atendimento | Tabela batch | Mensal |
| `tempo_resolucao_ticket` | Atendimento | Tabela batch | Diário |
| `csat_pos_atendimento` | Atendimento | Tabela batch | Diário |
| `erros_experiencia` | Atendimento | Stream + tabela batch | Tempo real / Diário |
| `latencia_p90_cliente` | Atendimento | Tabela batch | Diário |
| `reclamacoes_externas` | Atendimento | Tabela batch | Semanal |
| `referrals_feitos` | Engajamento & Retenção | Tabela batch | Diário |
| `consentimentos_lgpd` | Onboarding | Tabela batch | Diário |

---

## 6. Métricas de valor (OE2 aplicado ao caso)

As duas decisões profundas apresentam naturezas econômicas complementares. A detecção de fraude PIX tem efeito predominante em custo evitado; a retenção proativa tem efeito predominante em receita preservada. As subseções a seguir detalham as métricas aplicáveis a cada decisão.

### 6.1 Métricas de valor para detecção de fraude PIX

#### 6.1.1 Receita incremental

- **Taxa de aprovação de transações legítimas:** proporção de PIX genuínos aprovados sem fricção
- **Receita por cliente preservada:** valor anualizado de clientes que não cancelaram a Conta de Pagamento após experiência positiva de segurança

#### 6.1.2 Custo evitado

- **Perda por fraude evitada:** R$ de fraudes bloqueadas que, em ausência do método, teriam se concretizado
- **Custo de MED evitado:** custo operacional e regulatório do Mecanismo Especial de Devolução quando bloqueio preventivo é bem-sucedido
- **Multas regulatórias evitadas:** penalidades por descumprimento de diretrizes BACEN sobre prevenção de fraude

#### 6.1.3 Eficiência operacional

- **Throughput do serviço de autorização:** número de decisões por segundo que o serviço sustenta
- **Taxa de *step-up* adequado:** proporção de desafios que efetivamente bloqueiam fraude versus falsos positivos
- **Tempo médio de análise manual:** horas economizadas em revisão humana por fraudes detectadas automaticamente

#### 6.1.4 Cenário de referência para atribuibilidade

O cenário contrafactual usado para atribuir valor à decisão automatizada é a **regra baseline** de bloqueio somente por lista estática de destinatários conhecidos e por valor acima de limite fixo. A diferença de perda por fraude entre o método DPE e o cenário baseline é a medida atribuível.

### 6.2 Métricas de valor para acionamento de retenção

#### 6.2.1 Receita preservada

- **LTV preservado dos clientes retidos:** valor presente líquido dos clientes que não encerraram a conta após intervenção de retenção, descontado do custo da intervenção
- **Receita recorrente mantida:** receita mensal que permanece ativa dos clientes incluídos em campanhas bem-sucedidas
- **Cross-sell induzido:** receita incremental gerada por clientes que ativaram novos produtos como parte da intervenção de retenção

#### 6.2.2 Custo evitado

- **Custo de aquisição de cliente substituto:** R$ 80 a R$ 150 por cliente que precisaria ser adquirido para compensar cada churn, custo evitado quando a retenção é bem-sucedida
- **Custo operacional de reativação:** custo das campanhas de reativação de clientes inativos, mais alto que o custo de retenção proativa

#### 6.2.3 Eficiência operacional

- **Taxa de precisão do acionamento:** proporção de clientes acionados que efetivamente apresentariam churn no horizonte, calibrando o custo/benefício da campanha
- **Taxa de conversão da intervenção:** proporção de clientes acionados que permanecem ativos após 90 dias
- **Custo marginal por cliente retido:** custo total da campanha dividido pelo número de clientes efetivamente retidos

#### 6.2.4 Cenário de referência para atribuibilidade

O cenário contrafactual usado para atribuir valor à decisão de retenção é o **grupo de controle estatisticamente equivalente** não acionado pela campanha. A diferença de taxa de retenção entre o grupo acionado e o grupo de controle, multiplicada pelo LTV médio, é a medida atribuível. Esta abordagem, usada em práticas de marketing quase-experimental, requer desenho cuidadoso de amostragem para evitar viés de seleção.

---

## 7. Operação do método DPE no caso

A operação do método DPE é apresentada para as duas decisões profundas, em estrutura simétrica que evidencia como os mesmos eixos qualificadores (confiabilidade e tempestividade) são calibrados de forma contrastante conforme a natureza da decisão.

### 7.1 Operação aplicada à detecção de fraude PIX

#### 7.1.1 Cadeia de decisão da fraude PIX mapeada aos sete estágios (Seção 3.3 do TCC)

| Estágio | O que acontece na decisão de fraude PIX |
|---|---|
| 1. Geração | Cliente inicia PIX no app (tap no botão "Enviar") |
| 2. Ingestão | Evento é enviado ao Kafka; produtos de dados em streaming são atualizados |
| 3. Transformação | *Feature store* tempo real computa *features* de sessão e de comportamento; *feature store* batch serve agregações históricas |
| 4. Entrega | Serviço de autorização PIX consulta *feature stores*, APIs síncronas de saldo e limite, e cache de score de risco |
| 5. Inferência | Modelo de score de fraude executa com as *features* consolidadas, produz score no intervalo [0, 1] |
| 6. Decisão | Regra de negócio aplica threshold: score < 0.3 aprova, 0.3-0.7 *step-up*, > 0.7 bloqueia |
| 7. Observação | Resultado é registrado; se houve fraude confirmada posteriormente, label retorna para retreino do modelo e atualização de blocklist |

#### 7.1.2 Critérios de confiabilidade aplicados (Seção 3.4 → Tabela 2 do TCC)

A Tabela 2 do TCC será preenchida no artigo com os exemplos concretos deste PRD. Exemplos de célula:

- **Estágio 2 (Ingestão) × *Freshness*:** latência máxima aceitável de 50ms entre evento PIX e disponibilidade na *feature store* tempo real
- **Estágio 5 (Inferência) × *Distribution*:** distribuição das predições do modelo é monitorada por janela de 1 hora; desvio > 2σ aciona alerta
- **Estágio 7 (Observação) × *Lineage*:** rótulo de fraude confirmada precisa ser rastreável até a transação original via *transaction_id* único

#### 7.1.3 Critérios de tempestividade aplicados (Seção 3.5 → Tabela 3 do TCC)

Calibração por dimensão para fraude PIX:

| Dimensão | Horizonte calibrado |
|---|---|
| Frescor da informação | Milissegundos (features de sessão) a horas (score de destinatário) |
| Frescor do modelo | Retreino adaptativo acionado por drift (Bayram et al., 2024); idade máxima do modelo em produção: 30 dias |
| Janela de oportunidade da decisão | <500ms (antes da autorização ser enviada ao PIX do destinatário) |

#### 7.1.4 Matriz DPE×CT preenchida para fraude PIX

| | Confiabilidade | Tempestividade |
|---|---|---|
| **Decisão** | A decisão de bloquear fraude está conectada à meta de reduzir perda PIX? SIM (OKR do domínio Risco & Fraude). | A decisão é capaz de atuar antes da liquidação? SIM se <500ms; senão a autorização já foi enviada. |
| **Pergunta** | As 6 perguntas são verificáveis por dados existentes na cadeia? SIM, cada pergunta tem família de *data point* dedicada. | As 6 perguntas são formuladas no momento certo? SIM se no intervalo da autorização; perguntas post-hoc não alimentam a decisão. |
| **Evidência** | Os 30+ *data points* atendem aos 5 pilares em cada um dos 7 estágios? Varia por *feature*; `meds_historico` é frágil em *freshness*, `features_sessao_corrente` é robusto. | Os *data points* estão dentro do horizonte calibrado? Varia por *feature*; `score_bureau` (mensal) é aceito por ser dimensão lenta, `features_sessao_corrente` exige milissegundos. |

### 7.2 Operação aplicada ao acionamento de retenção

#### 7.2.1 Cadeia de decisão da retenção mapeada aos sete estágios (Seção 3.3 do TCC)

| Estágio | O que acontece na decisão de retenção |
|---|---|
| 1. Geração | Eventos comportamentais, transacionais e de atendimento do cliente são produzidos ao longo do dia em múltiplos sistemas-fonte |
| 2. Ingestão | Eventos são capturados em batches diários noturnos pelos *pipelines* dos domínios Engajamento & Retenção e Atendimento |
| 3. Transformação | Agregações históricas, *features* derivadas, métricas de tendência e cálculo dos sete sinais de família são computados no ciclo batch |
| 4. Entrega | Healthy score, churn probability, LTV e value tier são materializados como produtos de dados consumíveis pelos *owners* de domínio |
| 5. Inferência | Modelo de probabilidade de churn executa com o healthy score e metadados do cliente, produzindo probabilidade no intervalo [0, 1] |
| 6. Decisão | Regra de negócio aplica threshold: churn_probability < 0.3 não aciona, 0.3-0.6 aciona intervenção leve, > 0.6 aciona intervenção alta |
| 7. Observação | Taxa de retenção do cliente após 30, 60 e 90 dias da intervenção é observada; sinal retorna para retreino do modelo e ajuste de pesos do healthy score |

#### 7.2.2 Critérios de confiabilidade aplicados

Exemplos de célula da aplicação dos cinco pilares aos sete estágios para retenção:

- **Estágio 2 (Ingestão) × *Volume*:** volume diário de eventos de sessão recebido deve permanecer na faixa de 150M a 250M; desvios indicam falha na telemetria do app
- **Estágio 3 (Transformação) × *Schema*:** contratos do `engajamento_transacional_agregado` são monitorados por validação de schema em tempo de build do *pipeline*
- **Estágio 4 (Entrega) × *Lineage*:** o healthy score deve rastrear até os sete sinais de família e destes aos *data points* brutos, permitindo auditoria em caso de anomalia
- **Estágio 5 (Inferência) × *Distribution*:** distribuição do churn_probability na base é monitorada semanalmente; desvio > 2σ aciona revisão do modelo
- **Estágio 7 (Observação) × *Freshness*:** sinal de retenção confirmada só é conhecido 30 a 90 dias após a intervenção, condição que introduz latência estrutural no *loop* de aprendizado

#### 7.2.3 Critérios de tempestividade aplicados

Calibração por dimensão para retenção proativa:

| Dimensão | Horizonte calibrado |
|---|---|
| Frescor da informação | Diário para a maior parte dos sinais de família (agregações D-1); mensal para NPS; milissegundos irrelevantes dada a natureza batch |
| Frescor do modelo | Retreino mensal programado, com disparo antecipado se *drift* for detectado; idade máxima do modelo em produção: 90 dias |
| Janela de oportunidade da decisão | 7 a 30 dias antes do churn previsto; janelas mais curtas comprometem eficácia da intervenção, janelas mais longas geram ruído |

#### 7.2.4 Matriz DPE×CT preenchida para retenção

| | Confiabilidade | Tempestividade |
|---|---|---|
| **Decisão** | A decisão de acionar retenção está conectada à meta de preservar LTV? SIM (OKR do domínio Engajamento & Retenção). | A decisão é capaz de atuar antes do churn? SIM se acionada com 7 a 30 dias de antecedência; acionamentos tardios não evitam o cancelamento. |
| **Pergunta** | As 7 perguntas são verificáveis por dados existentes nos produtos de dados dos domínios? SIM, cada pergunta tem família dedicada e dados correspondentes. | As 7 perguntas são formuladas no momento certo? SIM no ciclo batch diário; perguntas feitas apenas mensalmente perdem sinais de tendência curta. |
| **Evidência** | Os sinais de família que compõem o healthy score atendem aos 5 pilares? Varia por família; `engajamento_transacional_agregado` é robusto, `reclamacoes_externas` é frágil em *distribution* por depender de fontes externas. | Os sinais estão dentro do horizonte calibrado? Varia por família; NPS (mensal) é aceito por ser dimensão estrutural, `engajamento_transacional_agregado` exige atualização diária. |

### 7.3 Contraste entre as duas decisões profundas

A aplicação do método DPE às duas decisões evidencia que a matriz DPE×CT acomoda calibrações radicalmente diferentes sem alterar sua estrutura. A tabela abaixo sintetiza o contraste:

| Dimensão | Fraude PIX | Retenção proativa |
|---|---|---|
| Natureza econômica | Custo evitado | Receita preservada |
| Tempestividade da decisão | Milissegundos | Dias a semanas |
| Ciclo de operação | Síncrono, tempo real | Batch diário |
| Observação de resultado | Horas a dias | 30 a 90 dias |
| Natureza da evidência | *Features* consumidas diretamente pelo modelo | Healthy score consolidado (evidência derivada) |
| Número de *data points* brutos | 30+ | 35+ |
| Retreino do modelo | Adaptativo por *drift* | Programado mensal + adaptativo |
| Risco predominante no eixo C | *Freshness* e *lineage* em tempo real | *Distribution* e *volume* em batch |
| Risco predominante no eixo T | Janela de oportunidade menor que latência da cadeia | *Drift* lento mascarado por janelas longas |

O contraste sustenta a tese central do método DPE: a generalidade não se dá por ampliação do método, mas por **calibração dos seus critérios** conforme a decisão sob análise.

---

## 8. Limitações do contexto simulado

O PRD é fictício e serve apenas à avaliação qualitativa do método DPE. As seguintes limitações devem ser declaradas explicitamente na Seção 4.3 do artigo (Discussão, limitações e material complementar).

### 8.1 Limitações gerais do contexto simulado

1. **Ausência de dados reais:** nenhum dado de clientes ou transações reais é utilizado; volumes e proporções são calibrados em ordens de magnitude de mercado
2. **Simplificação da realidade operacional:** um sistema real envolve dezenas de sinais adicionais, regras regulatórias específicas e integrações com sistemas de terceiros não modeladas aqui
3. **Calibração qualitativa:** *thresholds*, janelas e horizontes são indicativos; a calibração real exige experimentação controlada em ambiente de produção
4. **Ausência de validação experimental:** o método DPE, tal como aplicado neste caso, não foi testado contra conjuntos de controle reais. Os cenários contrafactuais (baseline estático para fraude PIX na Seção 3.4, grupo de controle quase-experimental para retenção na Seção 4.5) são declarados como condições teóricas de atribuição, e sua execução empírica fica como trabalho futuro

### 8.2 Limitações específicas da decisão de fraude PIX

1. **Diversidade de padrões de fraude não exaurida:** o caso simulado considera seis famílias de sinais, mas fraudadores sofisticados exploram padrões emergentes que exigem atualização contínua do catálogo
2. ***Trade-off* entre segurança e experiência:** bloqueios excessivos geram fricção em clientes legítimos; falsos positivos acarretam custo de reputação não modelado quantitativamente aqui
3. **Integração com DICT e BACEN:** a modelagem assume integração plena com o Diretório de Identificadores de Contas Transacionais e com o sistema de MED, que na prática apresenta latências e falhas não tratadas no caso simulado

### 8.3 Limitações específicas da decisão de retenção proativa

1. **Definição de churn depende do contexto:** churn pode significar encerramento formal da conta, inatividade prolongada, migração parcial de uso para concorrentes, ou redução significativa de engajamento; a modelagem adotada trata inatividade e cancelamento como proxies, mas há espectro de comportamentos intermediários
2. **Causalidade vs correlação na retenção:** clientes acionados por campanha e que permanecem ativos podem ter permanecido independentemente da intervenção. A atribuição rigorosa de valor exige grupo de controle, cujo desenho quase-experimental é formalizado na Seção 4.5, mas não executado neste contexto simulado
3. **Modelos de LTV são sensíveis a premissas:** LTV médio depende de premissas sobre horizonte de retenção, taxa de desconto e estabilidade do modelo de receita, todas simplificadas neste contexto simulado
4. **Cold start para clientes novos:** clientes com menos de 90 dias de histórico têm healthy score pouco informativo; a decisão de retenção é estruturalmente mais difícil para esse grupo, o que o caso simulado reconhece mas não resolve
5. **Dimensões subjetivas escapam ao modelo:** sinais como NPS e reclamações externas capturam percepção, mas não explicam causas profundas de insatisfação; o healthy score, por ser métrica consolidada, perde granularidade que um diagnóstico qualitativo revelaria

---

## 9. Referências consultadas para calibração realista

Os volumes e ordens de magnitude foram estimados com base em três categorias de fontes, nenhuma delas contendo dados proprietários ou confidenciais.

### 9.1 Fontes regulatórias e institucionais

- Relatórios públicos da Febraban sobre PIX no mercado brasileiro
- Relatórios do BACEN sobre volume transacional de instituições de pagamento
- Divulgações institucionais do Sistema de Pagamentos Instantâneos (SPI) e do Diretório de Identificadores de Contas Transacionais (DICT)

### 9.2 *Benchmarks* divulgados publicamente pela indústria

- Relatórios de resultado trimestral (divulgação pública) de fintechs brasileiras listadas
- *Benchmarks* de indústria de fintechs brasileiras (Nubank, Inter, C6, PicPay, Mercado Pago) em materiais públicos (apresentações a investidores, releases de imprensa, reportagens especializadas)
- Relatórios de consultoria sobre o mercado fintech brasileiro (BCG, McKinsey, Accenture) já citados como referências empíricas gerais no artigo (BCG, 2025)

### 9.3 Fontes específicas para calibração de retenção e engajamento

- Literatura pública sobre curva de LTV em fintechs brasileiras
- *Benchmarks* de taxa de churn em bancos digitais divulgados em materiais de mercado
- Estudos públicos sobre CAC em fintechs brasileiras, incluindo relatórios de venture capital e análises setoriais
- Materiais da ABES (Associação Brasileira das Empresas de Software) sobre métricas SaaS aplicáveis a fintechs

### 9.4 Conhecimento de domínio dos autores

O contexto simulado também reflete o conhecimento acumulado dos autores como profissionais do setor fintech brasileiro, sem comprometer confidencialidade de dados proprietários de qualquer instituição específica.

### 9.5 Referências bibliográficas do TCC ativadas na Seção 4

As obras citadas na Seção 4 do artigo são aquelas já introduzidas na Seção 2 e nas subseções da Seção 3, sem introdução de referências novas: Moses, Gavish e Vorwerck (2024); Bayram, Ahmed e Hallin (2024); Mohammed et al. (2025); Oliveira et al. (2023); Kore et al. (2024); Agrahari e Singh (2022); Huyen (2024); Dehghani (2023); Reis e Housley (2023); Kleppmann (2017); Olesen-Bagneux (2023); Charan (2019); Gudigantala, Madhavaram e Bicen (2023); Venancio, De Rolt e Darold (2026); Marques e Fook (2022); BCG (2025); Gartner (2024, 2025).

---

## 10. Anexo público: estratégia de material complementar em repositório aberto

O volume de detalhamento apresentado neste PRD excede o limite de páginas do formato SBC adotado pelo artigo. Para preservar a profundidade técnica sem comprometer a extensão do manuscrito, a estratégia adotada é hospedar o material complementar em repositório público referenciado no artigo como anexo.

### 10.1 Repositório público

**URL:** https://github.com/jorgeac12/tcc-ita-dpe-forgepag-case
**Plataforma:** GitHub, com licença MIT para reprodutibilidade e reuso acadêmico
**Status:** repositório ativo, referenciado no corpo da Seção 4 do artigo como material complementar

### 10.2 Conteúdo hospedado no repositório

O repositório contém:

1. **PRD completo do caso ForgePag** (este documento, em sua versão consolidada)
2. **Detalhamento das duas decisões profundas** com todas as tabelas de *data points*, produtos de dados e matrizes DPE×CT preenchidas em versão densa
3. **Catálogo expandido de *data points*** com descrição técnica completa, frescor, tipo de fonte e *owner* do produto de dados
4. **Tabelas estendidas** das matrizes DPE×CT preenchidas com evidências específicas do caso, em versão densa (complementar às tabelas enxutas do artigo)
5. **Descrições textuais das figuras** utilizadas para construir os infográficos da cadeia de decisão e da matriz DPE×CT do artigo
6. **Arquivo CITATION.cff** para citação formal do repositório como material complementar do artigo

### 10.3 Conteúdo mantido exclusivamente no artigo SBC

O artigo, na Seção 4, contém a versão sintética do caso aplicado, suficiente para cumprimento do OE6 sem dependência do repositório:

- Apresentação da fintech ForgePag e do portfólio na abertura da seção (volumes, domínios, duas decisões profundas)
- Aplicação sintética do método DPE à decisão de fraude PIX na Seção 4.1, com matriz DPE×CT preenchida em versão enxuta
- Aplicação sintética do método DPE à decisão de retenção proativa na Seção 4.2, com matriz DPE×CT preenchida em versão enxuta
- Discussão comparativa das duas decisões profundas, limitações principais e menção explícita ao repositório para detalhamento complementar na Seção 4.3

### 10.4 Referência cruzada no artigo

A Seção 4 do artigo menciona o repositório no parágrafo de abertura (com URL completa) e novamente na Seção 4.3 (material complementar). As tabelas presentes no artigo carregam nota indicando que a versão densa está disponível no repositório. Isso permite à banca consultar o material expandido sem que o artigo ultrapasse o limite de páginas do template SBC.

### 10.5 Benefícios da estratégia

1. **Respeito ao formato SBC:** o artigo permanece dentro do limite de páginas previsto pelo template
2. **Profundidade técnica preservada:** o detalhamento rico não é perdido por restrição de espaço
3. **Reprodutibilidade acadêmica:** outros pesquisadores podem aplicar o método DPE seguindo o detalhamento do PRD
4. **Transparência metodológica:** o PRD público permite à banca verificar a consistência entre método proposto e caso aplicado
5. **Alinhamento com a prática de ciência aberta:** o repositório público é citável e compõe portfólio público dos autores

---

## 11. Roadmap de uso do PRD na escrita da Seção 4

A tabela abaixo mapeia cada subseção da Seção 4 do artigo (versão final) ao material deste PRD que a sustenta.

| Subseção da 4 no artigo | Material deste PRD a ser mobilizado |
|---|---|
| Abertura da Seção 4 (contexto ForgePag, volumes, apresentação das duas decisões) | Seções 1 e 2 deste PRD (identidade da ForgePag, portfólio, volumes, domínios e apresentação das duas decisões profundas) |
| 4.1 — Aplicação do método à detecção de fraude em PIX | Seções 3, 5.1, 6.1, 7.1 deste PRD (anatomia da decisão, produtos de dados, métricas e operação) |
| 4.2 — Aplicação do método ao acionamento proativo de retenção | Seções 4, 5.2, 6.2, 7.2 deste PRD (anatomia da decisão, produtos de dados, métricas e operação, com destaque para a composição do healthy score na Seção 4.4) |
| 4.3 — Discussão, limitações e material complementar | Seções 7.3, 8 e 10 deste PRD (contraste entre as duas decisões profundas, limitações explícitas e estratégia do repositório público) |

A Seção 7.3 do PRD (contraste entre as duas decisões profundas) é particularmente relevante para sustentar a tese, na Seção 4.3 do artigo, de que a generalidade do método DPE se dá por calibração dos eixos de Confiabilidade e Tempestividade, não por reformulação da estrutura processual. As duas decisões profundas operam em regimes de urgência e tolerância a erro radicalmente distintos (fraude PIX em milissegundos com baixíssima tolerância a falso negativo de alto valor; retenção em dias com tolerância maior a falso positivo), e ainda assim a estrutura Decisão → Pergunta → Evidência permanece inalterada. Essa é a contribuição que o caso simulado é chamado a evidenciar.

---

**Fim do PRD.**
